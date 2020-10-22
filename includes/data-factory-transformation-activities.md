---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 19927fd274cbb7337248fb2ae8cf9d882612d570
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369250"
---
O Azure Data Factory suporta as seguintes atividades de transformação, que podem ser adicionadas a pipelines individualmente ou encadeadas com outra atividade.

| Atividade de transformação de dados | Ambiente de computação |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Atividades do Azure Machine Learning Studio (clássico): Execução de Lote e Recurso de Atualização](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |VM do Azure |
| [Procedimento armazenado](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics ou SQL Server |
| [Data Lake Analytics U-SQL](../articles/data-factory/v1/data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |HDInsight [Hadoop] ou Azure Batch |

> [!NOTE]
> Pode utilizar a atividade MapReduce para executar programas do Spark no seu cluster do HDInsight Spark. Veja [Invoke Spark programs from Azure Data Factory (Invocar programas do Spark a partir do Azure Data Factory)](../articles/data-factory/v1/data-factory-spark.md) para obter detalhes
> Pode criar uma atividade personalizada para executar scripts R no seu cluster do HDInsight com R instalado. Veja [Run R Script using Azure Data Factory (Executar Script R com o Azure Data Factory)](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample).
> 
> 

