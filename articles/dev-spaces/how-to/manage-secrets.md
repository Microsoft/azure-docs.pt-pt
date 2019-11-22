---
title: Como gerenciar segredos ao trabalhar com um espaço de desenvolvimento do Azure
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Desenvolvimento rápido do Kubernetes com contentores e microsserviços no Azure
keywords: Docker, kubernetes, Azure, AKS, serviço de contêiner do Azure, contêineres
ms.openlocfilehash: 49f53683b2499e790414d139dcb0bc0833005647
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279997"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Como gerenciar segredos ao trabalhar com um espaço de desenvolvimento do Azure

Seus serviços podem exigir determinadas senhas, cadeias de conexão e outros segredos, como bancos de dados ou outros serviços seguros do Azure. Ao definir os valores desses segredos em arquivos de configuração, você pode torná-los disponíveis em seu código como variáveis de ambiente.  Eles devem ser tratados com cuidado para evitar comprometer a segurança dos segredos.

O Azure Dev Spaces fornece duas opções recomendadas e simplificadas para armazenar segredos em gráficos Helm gerados pela Azure Dev Spaces ferramentas de cliente: no arquivo Values. dev. YAML e embutido diretamente em azds. YAML. Não é recomendável armazenar segredos em Values. YAML. Fora das duas abordagens para gráficos Helm gerados pela ferramenta do cliente definida neste artigo, se você criar seu próprio gráfico do Helm, poderá usar o gráfico do Helm diretamente para gerenciar e armazenar segredos.

## <a name="method-1-valuesdevyaml"></a>Método 1: Values. dev. YAML
1. Abra VS Code com seu projeto habilitado para Azure Dev Spaces.
2. Adicione um arquivo chamado _Values. dev. YAML_ na mesma pasta que o _azds. YAML_ existente e defina sua chave secreta e valores, como no exemplo a seguir:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds. YAML_ já faz referência ao arquivo _Values. dev. YAML_ , se existir. Se você preferir um nome de arquivo diferente, atualize a seção install. Values:

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. Modifique seu código de serviço para se referir a esses segredos como variáveis de ambiente, como no exemplo a seguir:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Atualize os serviços em execução no cluster com essas alterações. Na linha de comando, execute o comando:

    ```
    azds up
    ```
 
6. Adicional Na linha de comando, verifique se esses segredos foram criados:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Certifique-se de adicionar _valores. dev. YAML_ ao arquivo _. gitignore_ para evitar a confirmação de segredos no controle do código-fonte.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Método 2: embutido diretamente em azds. YAML
1.  Em _azds. YAML_, defina segredos na seção YAML configurações/desenvolver/instalar. Embora você possa inserir valores secretos diretamente ali, isso não é recomendável porque _azds. YAML_ é verificado no controle do código-fonte. Em vez disso, adicione espaços reservados usando a sintaxe "$PLACEHOLDER".

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT"
                host: "$REDIS_HOST"
                key: "$REDIS_KEY"
    ```
     
2.  Crie um arquivo _. env_ na mesma pasta que _azds. YAML_. Insira segredos usando a notação padrão = valor. Não confirme o arquivo _. env_ para o controle do código-fonte. (Para omitir do controle do código-fonte em sistemas de controle de versão baseados em git, adicione-o ao arquivo _. gitignore_ .) O exemplo a seguir mostra um arquivo _. env_ :

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Modifique o código-fonte do serviço para referenciar esses segredos no código, como no exemplo a seguir:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Atualize os serviços em execução no cluster com essas alterações. Na linha de comando, execute o comando:

    ```
    azds up
    ```

4.  adicional Exibir segredos do kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Passos seguintes

Com esses métodos, agora você pode se conectar com segurança a um banco de dados, um cache do Azure para Redis ou acessar serviços seguros do Azure.
 
