---
title: Executar gasodutos de aprendizagem automática Azure
description: Aprenda a executar os seus oleodutos Azure Machine Learning nos seus oleodutos Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
ms.date: 07/16/2020
ms.openlocfilehash: db1816baf0ebc476a132b344d8db3cdbdd170e50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373934"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Execute os oleodutos Azure Machine Learning na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Executar os seus oleodutos Azure Machine Learning como um passo nos seus oleodutos Azure Data Factory. A atividade de Machine Learning Execut Pipeline permite cenários de previsão de lotes tais como identificar possíveis incumprimentos de empréstimos, determinar o sentimento e analisar padrões de comportamento do cliente.

O vídeo abaixo apresenta uma introdução de seis minutos e demonstração desta funcionalidade.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Syntax

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

## <a name="type-properties"></a>Tipo de propriedades

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
name | Nome da atividade no oleoduto | String | Yes
tipo | Tipo de atividade é 'AzureMLExecutePipeline' | String | Yes
linkedServiceName | Serviço ligado à aprendizagem automática Azure | Referência de serviço ligada | Yes
mlPipelineId | ID do oleoduto de aprendizagem automática Azure publicado | Corda (ou expressão com resultadoType de corda) | Yes
experimentName | Executar o nome da experiência de história do gasoduto Machine Learning | Corda (ou expressão com resultadoType de corda) | No
mlPipelineParametros | Chave, pares de valor a serem passados para o ponto final do pipeline Azure Machine Learning publicado. As teclas devem corresponder aos nomes dos parâmetros do gasoduto definidos no pipeline de Machine Learning publicado | Objeto com pares de valor chave (ou Expressão com objeto de tipo resultado) | No
mlParentRunId | O oleoduto de aprendizagem da máquina de azure pai executar iD | Corda (ou expressão com resultadoType de corda) | No
continueOnStepFailure | Se continuar a execução de outros passos no gasoduto machine learning executado se um passo falhar | boolean | No

> [!NOTE]
> Para preencher os itens dropdown no nome do gasoduto Machine Learning e ID, o utilizador precisa de ter permissão para listar os gasodutos ML. A ADF UX chama APIs AzureMLService diretamente usando o registado nas credenciais do utilizador.  

## <a name="next-steps"></a>Passos seguintes
Veja os seguintes artigos que explicam como transformar dados de outras formas:

* [Executar atividade de Fluxo de Dados](control-flow-execute-data-flow-activity.md)
* [Atividade u-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade do porco](transform-data-using-hadoop-pig.md)
* [Atividade mapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Apache Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
