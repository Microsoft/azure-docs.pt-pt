---
title: Azure Service Fabric CLI-sfctl malha código-pacote-log
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para a obtenção de registos para um pacote de código especificado.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906042"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Obtenha os registos do recipiente do pacote de código especificado para a réplica de serviço dada.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| get | Recebe os troncos do contentor. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl malha código-pacote-log obter
Recebe os troncos do contentor.

Obtém os registos do recipiente do pacote de código especificado da réplica de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --- nome de aplicação --nome de aplicação [Obrigatório] | O nome da aplicação. |
| --código-pacote-nome [Obrigatório] | O nome do código do serviço. |
| --réplica-nome [Obrigatório] | Nome da réplica do tecido de serviço. |
| --nome de serviço [Obrigatório] | O nome do serviço. |
| --cauda | Número de linhas para mostrar a partir do final dos registos. O padrão é 100. 'todos' para mostrar os registos completos. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Próximos passos
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](/azure/service-fabric/scripts/sfctl-upgrade-application)da amostra .