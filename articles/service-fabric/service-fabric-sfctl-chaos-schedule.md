---
title: CLI do Azure Service Fabric – agendamento de caos sfctl | Microsoft Docs
description: Descreve os comandos de agendamento de sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: 443a8186ac6e57360105e59e30f84db997cd2251
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72897547"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Obter e definir o cronograma de caos.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| Obter | Obtenha o cronograma de caos definindo quando e como executar o caos. |
| set | Defina o agendamento usado pelo caos. |

## <a name="sfctl-chaos-schedule-get"></a>Get da agenda de caos sfctl
Obtenha o cronograma de caos definindo quando e como executar o caos.

Obtém a versão do cronograma de caos em uso e o cronograma de caos que define quando e como executar o caos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --Timeout-t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
| --consulta | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/para obter mais informações e exemplos. |
| --Detalhado | Aumentar o detalhamento de log. Use--debug para logs de depuração completos. |

## <a name="sfctl-chaos-schedule-set"></a>conjunto de agendamento de caos sfctl
Defina o agendamento usado pelo caos.

O caos agendará automaticamente as execuções com base na programação de caos. O agendamento de caos será atualizado se a versão fornecida corresponder à versão no servidor. Ao atualizar o cronograma de caos, a versão no servidor é incrementada em 1. A versão no servidor voltará para 0 depois de atingir um grande número. Se o caos estiver em execução quando essa chamada for feita, a chamada falhará.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --caos-Parameters-Dictionary | Lista codificada JSON que representa um mapeamento de nomes de cadeia de caracteres para os Caosparameters a serem usados pelos trabalhos. |
| --Data de expiração-UTC | A data e a hora de quando parar de usar a agenda para agendar caos.  Padrão\: 9999-12-31T23\:59\:59.999 Z. |
| --trabalhos | Lista codificada em JSON de ChaosScheduleJobs que representa quando executar o caos e com com quais parâmetros executar o caos. |
| --Data de início-UTC | A data e a hora de quando começar a usar a agenda para agendar caos.  Padrão\: 1601-01-01T00\:00\:00.000 Z. |
| --Timeout-t | Padrão\: 60. |
| --versão | O número de versão do agendamento. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --Depurar | Aumente o detalhamento de log para mostrar todos os logs de depuração. |
| --ajuda-h | Mostrar esta mensagem de ajuda e sair. |
| --saída-o | Formato de saída.  Valores permitidos\: JSON, jsonc, Table, TSV.  Padrão\: JSON. |
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


## <a name="next-steps"></a>Passos seguintes
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
