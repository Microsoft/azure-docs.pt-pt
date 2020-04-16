---
title: Executar oleodutos de aprendizagem automática Azure
description: Aprenda a executar os seus oleodutos Azure Machine Learning nos seus oleodutos Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: f033651eb7e52ba60cce9b74941a4ef0eb376d2b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419005"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Executar oleodutos de aprendizagem automática azure em Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Execute os seus oleodutos Azure Machine Learning como um passo nos seus oleodutos Azure Data Factory. A atividade do Pipeline de Execução de Machine Learning permite cenários de previsão de lotes, tais como identificar possíveis incumprimentos de empréstimos, determinar o sentimento e analisar padrões de comportamento do cliente.

O vídeo abaixo apresenta uma introdução de seis minutos e demonstração desta funcionalidade.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome da atividade no oleoduto | String | Sim
tipo | Tipo de atividade é 'AzureMLExecutePipeline' | String | Sim
linkedServiceName | Serviço Ligado à Aprendizagem automática Azure | Referência de serviço ligada | Sim
mlPipelineId | ID do oleoduto azure machine learning publicado | Corda (ou expressão com resultadoTipo de corda) | Sim
experimentoNome | Executar nome da experiência de história da corrida do pipeline machine learning | Corda (ou expressão com resultadoTipo de corda) | Não
mlPipelineParameters | Chave, pares de valor a serem passados para o ponto final do pipeline Azure Machine Learning publicado. As teclas devem coincidir com os nomes dos parâmetros do gasoduto definidos no gasoduto de Aprendizagem automática publicado | Objeto com pares de valor chave (ou Expressão com objeto resultaType) | Não
mlParentRunId | O pipeline de machine learning azure-mãe executar ID | Corda (ou expressão com resultadoTipo de corda) | Não
continuarOnStepFailure | Se continuar a execução de outros passos no gasoduto machine learning executado se um passo falhar | boolean | Não

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas:

* [Executar atividade de Fluxo de Dados](control-flow-execute-data-flow-activity.md)
* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade de porco](transform-data-using-hadoop-pig.md)
* [MapReduzir a atividade](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
