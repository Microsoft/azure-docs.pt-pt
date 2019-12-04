---
title: Como gerenciar segredos ao trabalhar com um espaço de desenvolvimento do Azure
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, kubernetes, Azure, AKS, serviço de contêiner do Azure, contêineres
ms.openlocfilehash: b184f72dfbbfe093443ab8a9b79bafbece3a3d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790183"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Como gerenciar segredos ao trabalhar com um espaço de desenvolvimento do Azure

Seus serviços podem exigir determinadas senhas, cadeias de conexão e outros segredos, como bancos de dados ou outros serviços seguros do Azure. Ao definir os valores desses segredos em arquivos de configuração, você pode torná-los disponíveis em seu código como variáveis de ambiente.  Eles devem ser tratados com cuidado para evitar comprometer a segurança dos segredos.

O Azure Dev Spaces fornece duas opções recomendadas e simplificadas para armazenar segredos em gráficos Helm gerados pelo Azure Dev Spaces ferramentas de cliente: no arquivo `values.dev.yaml` e embutido diretamente no `azds.yaml`. Não é recomendável armazenar segredos em `values.yaml`. Fora das duas abordagens para gráficos Helm gerados pela ferramenta do cliente definida neste artigo, se você criar seu próprio gráfico do Helm, poderá usar o gráfico do Helm diretamente para gerenciar e armazenar segredos.

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
 
 
## <a name="method-2-azdsyaml"></a>Método 2: azds. YAML
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

## <a name="passing-secrets-as-build-arguments"></a>Passando segredos como argumentos de compilação

As seções anteriores mostraram como passar segredos para uso em tempo de execução do contêiner. Você também pode passar um segredo no momento da compilação do contêiner, como uma senha para um NuGet privado, usando `azds.yaml`.

Em `azds.yaml`, defina os segredos de tempo de compilação em *configurações. desenvolva. Build. args* usando a sintaxe `<variable name>: ${secret.<secret name>.<secret key>}`. Por exemplo:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

No exemplo acima, *mynugetsecret* é um segredo existente e *pattoken* é uma chave existente.

>[!NOTE]
> As chaves e os nomes de segredo podem conter o caractere `.`. Use `\` para escapar `.` ao passar segredos como argumentos de compilação. Por exemplo, para passar um segredo chamado *foo. bar* com a chave de *token*: `MYTOKEN: ${secret.foo\.bar.token}`. Além disso, os segredos podem ser avaliados com o prefixo e o texto do sufixo. Por exemplo, `MYURL: eus-${secret.foo\.bar.token}-version1`. Além disso, os segredos disponíveis nos espaços pai e avô podem ser passados como argumentos de compilação.

Em seu Dockerfile, use a diretiva *ARG* para consumir o segredo e, em seguida, use essa mesma variável posteriormente no Dockerfile. Por exemplo:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Atualize os serviços em execução no cluster com essas alterações. Na linha de comando, execute o comando:

```
azds up
```

## <a name="next-steps"></a>Passos seguintes

Com esses métodos, agora você pode se conectar com segurança a um banco de dados, um cache do Azure para Redis ou acessar serviços seguros do Azure.
 
