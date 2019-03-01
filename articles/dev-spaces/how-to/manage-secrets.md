---
title: Como gerir segredos ao trabalhar com um espaço de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
ms.technology: azds-kubernetes
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contentores
ms.openlocfilehash: 45c893d6d61407f9c0f44b4a5961d351bba0035f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191803"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Como gerir segredos ao trabalhar com um espaço de desenvolvimento do Azure

Os serviços poderão precisar determinadas palavras-passe, cadeias de ligação e outros segredos, como para bases de dados ou outros serviços do Azure seguros. Ao definir os valores destes segredos nos arquivos de configuração, pode disponibilizá-los em seu código como variáveis de ambiente.  Estes devem ser tratados com cuidado para evitar comprometer a segurança dos segredos.

Espaços de desenvolvimento do Azure fornece duas opções para armazenar segredos de recomendado: no ficheiro de values.dev.yaml e inline, diretamente no azds.yaml. Não é recomendado para armazenar segredos no Values.
 
## <a name="method-1-valuesdevyaml"></a>Método 1: values.dev.yaml
1. Abra o VS Code com o seu projeto que está ativado para espaços de desenvolvimento do Azure.
2. Adicionar um arquivo chamado _values.dev.yaml_ na mesma pasta que existente _Values_ e definir a chave secreta e valores, como no exemplo seguinte:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. Atualização _azds.yaml_ dizer espaços de desenvolvimento do Azure para utilizar a nova _values.dev.yaml_ ficheiro. Para tal, adicione esta configuração na secção configurations.develop.container:

    ```yaml
           container:
             values:
             - "charts/webfrontend/values.dev.yaml"
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
                port: "$REDIS_PORT_DEV"
                host: "$REDIS_HOST_DEV"
                key: "$REDIS_KEY_DEV"
    ```
     
2.  Criar uma _. env_ ficheiro na mesma pasta que _azds.yaml_. Introduza segredos através de chave padrão = notação de valor. Não consolidar os _. env_ ficheiro ao controlo de origem. (Para omitir a partir do controlo de origem em sistemas de controle de versão baseada no git, adicione-o para o _. gitignore_ ficheiro.) A exemplo a seguir mostra um _. env_ ficheiro:

    ```
    REDIS_PORT_DEV=3333
    REDIS_HOST_DEV=myredishost
    REDIS_KEY_DEV=myrediskey
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
 
