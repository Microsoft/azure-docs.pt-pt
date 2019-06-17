---
title: Como gerir segredos ao trabalhar com um espaço de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contentores
ms.openlocfilehash: 900529d54a26729d9d0fb949d9217d5e2d618254
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66515297"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Como gerir segredos ao trabalhar com um espaço de desenvolvimento do Azure

Os serviços poderão precisar determinadas palavras-passe, cadeias de ligação e outros segredos, como para bases de dados ou outros serviços do Azure seguros. Ao definir os valores destes segredos nos arquivos de configuração, pode disponibilizá-los em seu código como variáveis de ambiente.  Estes devem ser tratados com cuidado para evitar comprometer a segurança dos segredos.

Espaços de desenvolvimento do Azure fornece duas opções de recomendado, otimizadas para armazenar segredos em gráficos Helm gerados pelo cliente do Azure Dev espaços ferramentas: no ficheiro de values.dev.yaml e inline, diretamente no azds.yaml. Não é recomendado para armazenar segredos no Values. Fora as duas abordagens para Helm gráficos gerados pelo cliente de ferramentas definido neste artigo, se criar seu próprio gráfico Helm, pode utilizar o gráfico Helm diretamente para gerir e armazenar segredos.

## <a name="method-1-valuesdevyaml"></a>Método 1: values.dev.yaml
1. Abra o VS Code com o seu projeto que está ativado para espaços de desenvolvimento do Azure.
2. Adicionar um arquivo chamado _values.dev.yaml_ na mesma pasta que existente _azds.yaml_ e definir a chave secreta e valores, como no exemplo seguinte:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds.yaml_ já referencia a _values.dev.yaml_ ficheiro se existir. Se preferir um nome de ficheiro diferente, atualize a secção de install.values:

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. Modifique o código de serviço para fazer referência a estes segredos, como variáveis de ambiente, como no exemplo seguinte:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Atualize os serviços em execução no seu cluster com essas alterações. Na linha de comandos, execute o comando:

    ```
    azds up
    ```
 
6. (Opcional) Na linha de comando, verifique que foram criados estes segredos:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Certifique-se de que adicione _values.dev.yaml_ para o _. gitignore_ arquivo para evitar a consolidar os segredos no controle de origem.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Método 2: Inline, diretamente no azds.yaml
1.  Na _azds.yaml_, defina os segredos no yaml secção configurações/desenvolver/instalar. Apesar de poder introduzir segredo valores diretamente, não é recomendado porque _azds.yaml_ check-in no controle de origem. Em vez disso, adicione marcadores de posição usando a sintaxe "$PLACEHOLDER".

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
     
2.  Criar uma _. env_ ficheiro na mesma pasta que _azds.yaml_. Introduza segredos através de chave padrão = notação de valor. Não consolidar os _. env_ ficheiro ao controlo de origem. (Para omitir a partir do controlo de origem em sistemas de controle de versão baseada no git, adicione-o para o _. gitignore_ ficheiro.) A exemplo a seguir mostra um _. env_ ficheiro:

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Modifique o código de origem de serviço para fazer referência a estes segredos no código, como no exemplo seguinte:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Atualize os serviços em execução no seu cluster com essas alterações. Na linha de comandos, execute o comando:

    ```
    azds up
    ```

4.  (opcional) Segredos do modo de exibição de kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Passos Seguintes

Através destes métodos, pode agora ligar a uma base de dados, uma Cache do Azure para Redis, ou de forma segura aceder aos serviços do Azure seguros.
 
