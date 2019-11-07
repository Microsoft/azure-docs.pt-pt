---
title: Execute Azure Machine Learning pipelines em seus pipelines de Azure Data Factory
description: Saiba como executar seus pipelines de Azure Machine Learning em seus pipelines de Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: a257c7d3f288f9a2e1998bf0679b7146b089cb50
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683884"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Executar pipelines de Azure Machine Learning no Azure Data Factory

Execute seus pipelines de Azure Machine Learning como uma etapa em seus pipelines de Azure Data Factory. A atividade Machine Learning executar pipeline permite cenários de previsão de lote, como a identificação de possíveis padrões de empréstimo, a determinação de sentimentos e a análise de padrões de comportamento do cliente.

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

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome da atividade no pipeline | String | Sim
tipo | O tipo de atividade é ' AzureMLExecutePipeline ' | String | Sim
linkedServiceName | Serviço vinculado para Azure Machine Learning | Referência de serviço vinculado | Sim
mlPipelineId | ID do pipeline de Azure Machine Learning publicado | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres) | Sim
experimentoname | Nome de teste de execução do histórico da execução do pipeline de Machine Learning | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres) | Não
mlPipelineParameters | Pares de chave, valor a serem passados para o ponto de extremidade do pipeline de Azure Machine Learning publicado. As chaves devem corresponder aos nomes dos parâmetros de pipeline definidos no pipeline de Machine Learning publicado | Objeto com pares chave-valor (ou expressão com o objeto ResultType) | Não
mlParentRunId | A ID de execução do pipeline Azure Machine Learning pai | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres) | Não
continueOnStepFailure | Se deseja continuar a execução de outras etapas no pipeline de Machine Learning executar se uma etapa falhar | boolean | Não

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras:

* [Executar atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [Atividade de U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do hive](transform-data-using-hadoop-hive.md)
* [Atividade Pig](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
