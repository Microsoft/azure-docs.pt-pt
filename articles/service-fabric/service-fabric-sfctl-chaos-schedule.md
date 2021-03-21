---
title: Azure Service Fabric CLI-sfctl calendário de caos
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para o agendamento do caos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: dd911574cd37c18e3043cfc2482bd1fcd5f3857b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260848"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Arranja e define o calendário do caos.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| get | Obtenha a Agenda do Caos definindo quando e como executar o Caos. |
| set | Desa parte da programação utilizada pelo Caos. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl calendário caos obter
Obtenha a Agenda do Caos definindo quando e como executar o Caos.

Obtém a versão da Agenda caos em uso e a Agenda do Caos que define quando e como executar o Caos.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl conjunto de horários caos
Desa parte da programação utilizada pelo Caos.

O caos irá programar automaticamente as suas execuções com base na Agenda do Caos. A Agenda caos será atualizada se a versão fornecida corresponder à versão do servidor. Ao atualizar a Agenda caos, a versão do servidor é incrementada por 1. A versão no servidor irá embrulhar de volta para 0 depois de alcançar um grande número. Se o Caos estiver a decorrer quando esta chamada for feita, a chamada falhará.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --caos-parâmetros-dicionário | Lista codificada JSON que representa um mapeamento de nomes de cordas para ChaosParameters para ser usado por Jobs. |
| --data-validade-utc | A data e a hora para quando parar de usar a Agenda para agendar o Caos.  Padrão \: 9999-12-31T23 \: 59 \: 59.999Z. |
| ...-empregos | JSON codificou a lista de ChaosScheduleJobs representando quando executar o Caos e com que parâmetros executar o Caos. |
| --data-início-utc | A data e a hora para quando começar a usar a Agenda para agendar o Caos.  Padrão \: 1601-01-01T00 \: 00 \: 00.000Z. |
| --timeout -t | Padrão \: 60. |
| -versão | O número da versão da Agenda. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

### <a name="examples"></a>Exemplos

O seguinte comando define um calendário (assumindo que o calendário atual tem a versão 0) que começa em 2016-01-01 e expira em 2038-01-01 que corre Caos 24 horas do dia, 7 dias por semana.
O caos será programado no aglomerado para esse momento.
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .
