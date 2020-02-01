---
title: CLI do Azure Service Fabric-contêiner sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para contêineres.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 07861e2b67eea67740b341cbea994de978973664
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906112"
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
| --application-id [Obrigatório] | A identidade do aplicativo. <br><br> Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --code-package-instância-id [Obrigatório] | ID que identifica exclusivamente uma instância do pacote de códigos implantada em um nó do Service Fabric. <br><br> Pode ser recuperado por ' Service Code-pacote-List '. |
| --código-pacote-nome [Obrigatório] | O nome do pacote de códigos especificado no manifesto do serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
| --contentor-api-uri-caminho [Obrigatório] | Caminho do URI da API REST do contêiner, use ' {ID} ' no lugar do nome/ID do contêiner. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome manifesto de serviço [Obrigatório] | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
| --contêiner-API-corpo | Corpo da solicitação HTTP para a API REST do contêiner. |
| --contêiner-API-tipo de conteúdo | Tipo de conteúdo para a API REST do contêiner, o padrão é ' Application/JSON '. |
| --container-api-http-verb | Verbo HTTP para API REST de contêiner, o padrão a ser obtido. |
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
| --application-id [Obrigatório] | A identidade do aplicativo. <br><br> Normalmente, é o nome completo do aplicativo sem o esquema de URI ' Fabric\:'. A partir da versão 6,0, os nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "Fabric\:/MyApp/App1", a identidade do aplicativo será "MyApp\~App1" no 6.0 + e "MyApp/App1" nas versões anteriores. |
| --code-package-instância-id [Obrigatório] | ID da instância do pacote de códigos, que pode ser recuperada por ' Service Code-Package-List '. |
| --código-pacote-nome [Obrigatório] | O nome do pacote de códigos especificado no manifesto do serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome manifesto de serviço [Obrigatório] | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um Cluster Service Fabric. |
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