---
title: Passe a informação do gatilho para o oleoduto
description: Saiba como fazer referência aos metadados do gatilho no pipeline
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 57024d6cb23ab273aa69bf59f4ec436a6f873a05
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193552"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>Metadados de gatilho de referência em cursos de pipeline

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como os metadados de desencadeamento, como a hora de início do gatilho, podem ser usados no funcionaamento do gasoduto.

O pipeline às vezes precisa de compreender e lê metadados do gatilho que o invoca. Por exemplo, com o funcionamento do Gatilho da janela Tumbling, baseado no início e no fim da janela, o pipeline processará diferentes fatias de dados ou pastas. Na Azure Data Factory, utilizamos a Parametrização e [a Variável do Sistema](control-flow-system-variables.md) para passar metadados do gatilho para o pipeline.

Este padrão é especialmente útil para [o Gatilho da janela Tumbling](how-to-create-tumbling-window-trigger.md), onde o gatilho proporciona o início e o fim da janela.

> [!NOTE]
> O tipo de gatilho diferente fornece diferentes informações de meta dados. Para mais informações, consulte [a Variável do Sistema](control-flow-system-variables.md)

## <a name="data-factory-ui"></a>IU do Data Factory

Esta secção mostra como passar informações de meta dados do gatilho para o pipeline, dentro da Interface de Utilizador da Fábrica de Dados Azure.

1. Vá à **Tela de Autoria** e edite um oleoduto

1. Clique na tela em branco para apresentar as definições do gasoduto. Não selecione nenhuma atividade. Pode ser necessário puxar o painel de fixação do fundo da tela, pois pode ter sido colapsado.

1. Selecione **secção de parâmetros** e selecione **+ Novos** para adicionar parâmetros

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="Imagem de tela da definição do gasoduto mostrando como definir parâmetros no oleoduto.":::

1. Adicione gatilhos ao pipeline, clicando em **+ Gatilho**.

1. Criar ou prender um gatilho ao oleoduto e clicar **em OK**

1. Na página seguinte, preencha os dados de meta do gatilho para cada parâmetro. Utilize o formato definido na [Variável do Sistema](control-flow-system-variables.md) para recuperar informações do gatilho. Não é necessário preencher a informação para todos os parâmetros, apenas os que assumirão valores de metadados desencadeados. Por exemplo, aqui atribuímos a hora de início do gatilho para *parameter_1*.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="Screenshot da página de definição do gatilho mostrando como passar informações de gatilho para parâmetros de pipeline.":::

1. Para utilizar os valores em pipeline, utilize parâmetros _@pipeline ().parâmetros.parâmetroName,_ não variável __do__ sistema, em definições de pipeline. Por exemplo, no nosso caso, para ler a hora de início do gatilho, vamos referenciar @pipeline ().parameters.parameter_1.

## <a name="json-schema"></a>Esquema JSON

Para passar informações do gatilho para o gasoduto, tanto o gatilho como o json do gasoduto precisam de ser atualizados com a secção _de parâmetros._

### <a name="pipeline-definition"></a>Definição de gasoduto

Na secção **de propriedades,** adicione definições de parâmetros à secção **de parâmetros**

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>Definição de gatilho

Na secção **de gasodutos,** atribua os valores dos parâmetros na secção **de parâmetros.** Não é necessário preencher a informação para todos os parâmetros, apenas os que assumirão valores de metadados desencadeados.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>Utilize informações de gatilho no oleoduto

Para utilizar os valores em pipeline, utilize parâmetros _@pipeline ().parâmetros.parâmetroName,_ não variável __do__ sistema, em definições de pipeline.

## <a name="next-steps"></a>Passos seguintes

Para obter informações detalhadas sobre os gatilhos, consulte [a execução do Pipeline e os gatilhos](concepts-pipeline-execution-triggers.md#trigger-execution).
