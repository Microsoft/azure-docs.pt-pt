---
title: Azure Service Fabric CLI- sfctl caos agenda
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para agendamento de caos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 29b365c48e405830e238945f1d94156f477c15b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906182"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Pegue e estabeleça o calendário do caos.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| get | Obtenha a Agenda do Caos definindo quando e como executar o Caos. |
| set | Detete o horário usado pelo Caos. |

## <a name="sfctl-chaos-schedule-get"></a>horário do caos sfctl obter
Obtenha a Agenda do Caos definindo quando e como executar o Caos.

Obtém a versão da Agenda do Caos em uso e a Agenda do Caos que define quando e como executar o Caos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-chaos-schedule-set"></a>conjunto de horários do caos sfctl
Detete o horário usado pelo Caos.

O caos marcará automaticamente as corridas com base na Agenda do Caos. O Calendário do Caos será atualizado se a versão fornecida corresponder à versão do servidor. Ao atualizar o Calendário do Caos, a versão no servidor é incrementada por 1. A versão do servidor volta a 0 depois de atingir um grande número. Se o Caos estiver a decorrer quando esta chamada for feita, a chamada falhará.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --caos-parâmetros-dicionário | JSON lista codificada representando um mapeamento de nomes de cordas para ChaosParameters a ser usado por Jobs. |
| -expiração-data-utc | A data e a hora para parar de usar a Agenda para agendar o Caos.  Padrão\: 9999-12-31T23\:\:59 59.999Z. |
| -empregos | JSON codificada lista de ChaosScheduleJobs representando quando executar o Caos e com que parâmetros executar o Caos. |
| --início-data-utc | A data e a hora para quando começar a usar a Agenda para agendar o Caos.  Padrão\: 1601-01-01t00\:\:00 00.000Z. |
| --timeout -t | Padrão\: 60. |
| -versão | O número da versão da Agenda. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

### <a name="examples"></a>Exemplos

O comando seguinte define um horário (assumindo que o calendário atual tem a versão 0) que começa em 2016-01-01 e expira em 2038-01-01 que corre o Caos 24 horas do dia, 7 dias por semana.
O caos será programado no aglomerado para esse tempo.
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
- [Instale](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).
