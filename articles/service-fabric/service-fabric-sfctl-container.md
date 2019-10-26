---
title: CLI do Azure Service Fabric-contêiner sfctl | Microsoft Docs
description: Descreve os comandos de contêiner do Service Fabric CLI sfctl.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 13de6ff7b3e5a41eced5ca49a3af38fab60ba0a0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901304"
---
# <a name="sfctl-container"></a>sfctl container
Execute comandos relacionados ao contêiner em um nó de cluster.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| Invoke-API | Invocar a API do contêiner em um contêiner implantado em um Service Fabric nó para o pacote de códigos fornecido. |
| registos | Obtém os logs de contêiner para o contêiner implantado em um nó de Service Fabric. |

## <a name="sfctl-container-invoke-api"></a>invocação de contêiner sfctl-API
Invocar a API do contêiner em um contêiner implantado em um Service Fabric nó para o pacote de códigos fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. <br><br> Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --código-pacote-instância-ID [obrigatório] | ID que identifica exclusivamente uma instância do pacote de códigos implantada em um nó do Service Fabric. <br><br> Pode ser recuperado por ' Service Code-pacote-List '. |
| --Code-Package-Name [obrigatório] | O nome do pacote de códigos especificado no manifesto do serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
| --Container-API-URI-caminho [obrigatório] | Caminho do URI da API REST do contêiner, use ' {ID} ' no lugar do nome/ID do contêiner. |
| --node-Name [obrigatório] | O nome do nó. |
| --Service-manifest-Name [obrigatório] | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
| --contêiner-API-corpo | Corpo da solicitação HTTP para a API REST do contêiner. |
| --contêiner-API-tipo de conteúdo | Tipo de conteúdo para a API REST do contêiner, o padrão é ' Application/JSON '. |
| --contêiner-API-http-verbo | Verbo HTTP para API REST de contêiner, o padrão a ser obtido. |
| --Timeout-t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-container-logs"></a>logs de contêiner do sfctl
Obtém os logs de contêiner para o contêiner implantado em um nó de Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Application-ID [obrigatório] | A identidade do aplicativo. <br><br> Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --código-pacote-instância-ID [obrigatório] | ID da instância do pacote de códigos, que pode ser recuperada por ' Service Code-Package-List '. |
| --Code-Package-Name [obrigatório] | O nome do pacote de códigos especificado no manifesto do serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
| --node-Name [obrigatório] | O nome do nó. |
| --Service-manifest-Name [obrigatório] | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
| --cauda | Número de linhas a serem mostradas do final dos logs. O padrão é 100. ' todos ' para mostrar os logs completos. |
| --Timeout-t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |


## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).