---
title: Atividade de espera na Fábrica de Dados Azure
description: A atividade de espera interrompe a execução do gasoduto durante o período especificado.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: e6158938d01b6e5da74ed046d2a74e0dfd827f47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417917"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Executar atividade de espera na Fábrica de Dados Azure
Quando utiliza uma atividade Aguardar num pipeline, este aguarda o período de tempo especificado antes de continuar a execução das atividades subsequentes. 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Sintaxe

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome da `Wait` atividade. | String | Sim
tipo | Deve ser definido para **esperar.** | String | Sim
waitTimeInSeconds | O número de segundos que o gasoduto espera antes de continuar com o processamento. | Número inteiro | Sim

## <a name="example"></a>Exemplo

> [!NOTE]
> Esta secção fornece definições JSON e amostra de comandos PowerShell para executar o gasoduto. Para um passeio com instruções passo a passo para criar um pipeline data Factory utilizando definições Azure PowerShell e JSON, consulte [tutorial: crie uma fábrica de dados utilizando a Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Pipeline com atividade de espera
Neste exemplo, o gasoduto tem duas atividades: **Até** e **esperar**. A atividade de espera está configurada para esperar um segundo. O gasoduto executa a atividade web em ciclo com um segundo de tempo de espera entre cada corrida. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Passos seguintes
Consulte outras atividades de fluxo de controlo suportadas pela Data Factory: 

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Executar a Atividade do Pipeline](control-flow-execute-pipeline-activity.md)
- [Para Cada Atividade](control-flow-for-each-activity.md)
- [Obter Atividade de Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
- [Até a Atividade](control-flow-until-activity.md)
