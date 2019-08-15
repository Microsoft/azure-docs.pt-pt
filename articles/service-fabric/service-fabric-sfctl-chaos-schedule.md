---
title: CLI do Azure Service Fabric – agendamento de caos sfctl | Microsoft Docs
description: Descreve os comandos de agendamento de sfctl da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 1664978110b7c700906cbf4e6c80806ac70f1f05
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036521"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Obter e definir o cronograma de caos.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| Get | Obtenha o cronograma de caos definindo quando e como executar o caos. |
| set | Defina o agendamento usado pelo caos. |

## <a name="sfctl-chaos-schedule-get"></a>Get da agenda de caos sfctl
Obtenha o cronograma de caos definindo quando e como executar o caos.

Obtém a versão do cronograma de caos em uso e o cronograma de caos que define quando e como executar o caos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-chaos-schedule-set"></a>conjunto de agendamento de caos sfctl
Defina o agendamento usado pelo caos.

O caos agendará automaticamente as execuções com base na programação de caos. A versão na agenda de entrada fornecida deve corresponder à versão do cronograma de caos no servidor. Se a versão fornecida não corresponder à versão no servidor, o agendamento de caos não será atualizado. Se a versão fornecida corresponder à versão no servidor, o agendamento de caos será atualizado e a versão do cronograma de caos no servidor será incrementada em um e voltará para 0 após 2.147.483.647. Se o caos estiver em execução quando essa chamada for feita, a chamada falhará.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --caos-Parameters-Dictionary | Lista codificada JSON que representa um mapeamento de nomes de cadeia de caracteres para os Caosparameters a serem usados pelos trabalhos. |
| --expiry-date-utc | A data e a hora de quando parar de usar a agenda para agendar caos.  Default\: 9999-12-31T23\:59\:59.999Z. |
| --trabalhos | Lista codificada em JSON de ChaosScheduleJobs que representa quando executar o caos e com com quais parâmetros executar o caos. |
| --start-date-utc | A data e a hora de quando começar a usar a agenda para agendar caos.  Default\: 1601-01-01T00\:00\:00.000 z. |
| --Timeout-t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --versão | O número de versão do agendamento. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores\: permitidos JSON, jsonc, Table, TSV.  JSON\: padrão. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

### <a name="examples"></a>Exemplos

O comando a seguir define um agendamento (supondo que a agenda atual tem a versão 0) que começa em 2016-01-01 e expira em 2038-01-01 que executa o caos 24 horas do dia, 7 dias por semana. O caos será agendado no cluster para esse período.

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


## <a name="next-steps"></a>Passos Seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
