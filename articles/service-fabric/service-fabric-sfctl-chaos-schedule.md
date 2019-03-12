---
title: O Azure Service Fabric CLI - sfctl chaos schedule | Documentos da Microsoft
description: Descreve os comandos de agendamento do CLI do Service Fabric sfctl caos.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: f955ed63af221a08313042fcc8373b179ecbc120
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569387"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Obter e definir a agenda de caos.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| Get | Obtenha o agendamento de Chaos definir quando e como executar o caos. |
| set | Defina a agenda utilizada pelo caos. |

## <a name="sfctl-chaos-schedule-get"></a>obter agenda de chaos sfctl
Obtenha o agendamento de Chaos definir quando e como executar o caos.

Obtém a versão da agenda de Chaos em utilização e a agenda de Chaos que define quando e como executar o caos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

## <a name="sfctl-chaos-schedule-set"></a>conjunto de agenda de chaos sfctl
Defina a agenda utilizada pelo caos.

Chaos será automaticamente agendar execuções com base na agenda caos. A versão na programação de entrada fornecida tem de corresponder à versão da agenda caos no servidor. Se a versão fornecida não corresponde a versão no servidor, a agenda de Chaos não é atualizada. Se a versão fornecida corresponde à versão no servidor, em seguida, a agenda de Chaos é atualizada e a versão da agenda caos no servidor é incrementada num deles e inclui retornará ao 0 se depois de 2 147 483 647. Se estiver a executar Chaos quando esta chamada é feita, a chamada falhará.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --chaos-parameters-dictionary | JSON codificado lista que representa um mapeamento de nomes de cadeia de caracteres para ChaosParameters a ser utilizado pelas tarefas. |
| --expiry-date-utc | A data e hora parar de utilizar a agenda para agendar o caos.  Default\: 9999-12-31T23\:59\:59.999Z. |
| --jobs | Lista JSON com codificação de ChaosScheduleJobs representando quando executar o Chaos e com os quais os parâmetros para executar o Chaos com. |
| --start-date-utc | A data e hora começar a utilizar a agenda para agendar o caos.  Default\: 1601-01-01T00\:00\:00.000Z. |
| – tempo limite -t | Tempo limite do servidor em segundos.  Predefinido\: 60. |
| --version | O número de versão da agenda. |

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| – depuração | Aumenta a verbosidade de registo para mostrar que todos os registos de depuração. |
| – ajudar -h | Mostre esta mensagem de ajuda e saída. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Predefinido\: json. |
| --query | Cadeia de consulta do JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumenta a verbosidade do registo. Utilize--debug para os registos de depuração completa. |

### <a name="examples"></a>Exemplos

O comando a seguir define uma agenda (supondo que a agenda atual tem uma versão 0), que é iniciada no 2016-01-01 e expira em 2038-01-01, que executa o Chaos 24 horas do dia, 7 dias por semana. Chaos será agendada no cluster para essa hora.

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
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como utilizar a CLI do Service Fabric utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
