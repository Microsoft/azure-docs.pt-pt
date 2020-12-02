---
title: 'Transformação de Dados: Processo & transformar dados '
description: Saiba como transformar dados ou processar dados na Azure Data Factory utilizando o Hadoop, o Azure Machine Learning Studio (clássico) ou o Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: dfe8863f38da0aeb701b063f051a199e27745eeb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453354"
---
# <a name="transform-data-in-azure-data-factory-version-1"></a>Transformar dados na versão 1 da Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Azure Machine Learning Studio (clássico)](data-factory-azure-ml-batch-execution-activity.md) 
> * [Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET personalizado](data-factory-use-custom-activities.md)

## <a name="overview"></a>Descrição geral
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte as atividades de transformação de [dados na Data Factory.](../transform-data.md)

Este artigo explica as atividades de transformação de dados na Azure Data Factory que pode usar para transformar e processar os seus dados brutos em previsões e insights. Uma atividade de transformação executa em um ambiente de computação como o cluster Azure HDInsight ou um Azure Batch. Fornece ligações a artigos com informações detalhadas sobre cada atividade de transformação.

A Data Factory suporta as seguintes atividades de transformação de dados que podem ser adicionadas aos [oleodutos](data-factory-create-pipelines.md) individualmente ou acorrentados com outra atividade.

> [!NOTE]
> Para uma passagem de passo a passo com instruções passo a passo, consulte Criar um oleoduto com artigo de [transformação da Colmeia.](data-factory-build-your-first-pipeline.md)  
> 
> 

## <a name="hdinsight-hive-activity"></a>Atividade da Colmeia HDInsight
A atividade de Hive HDInsight num oleoduto da Data Factory executa consultas de Hive no seu próprio ou a pedido do grupo HDInsight baseado em Windows/Linux. Consulte o artigo [da Hive Activity](data-factory-hive-activity.md) para mais detalhes sobre esta atividade. 

## <a name="hdinsight-pig-activity"></a>Atividade do Porco HDInsight
A atividade do Porco HDInsight num oleoduto da Data Factory executa consultas de porco no seu próprio conjunto de Windows/HdInsight baseado em Windows/Linux. Consulte o artigo [da Atividade do Porco](data-factory-pig-activity.md) para mais detalhes sobre esta atividade. 

## <a name="hdinsight-mapreduce-activity"></a>HdInsight MapReduce atividade
A atividade HDInsight MapReduce num oleoduto data factory executa programas MapReduce por conta própria ou a pedido do grupo HDInsight baseado em Windows/Linux. Consulte o artigo [de Atividade mapReduce](data-factory-map-reduce.md) para mais detalhes sobre esta atividade.

## <a name="hdinsight-streaming-activity"></a>Atividade de streaming HDInsight
A Atividade de Streaming HDInsight num oleoduto de Data Factory executa programas de streaming Hadoop no seu próprio ou a pedido do grupo HDInsight baseado em Windows/Linux. Consulte [a atividade de Streaming HDInsight](data-factory-hadoop-streaming-activity.md) para obter detalhes sobre esta atividade.

## <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
A atividade HDInsight Spark num oleoduto data factory executa programas Spark no seu próprio cluster HDInsight. Para mais detalhes, consulte [os programas Invoke Spark da Azure Data Factory](data-factory-spark.md). 

## <a name="azure-machine-learning-studio-classic-activities"></a>Atividades do Azure Machine Learning Studio (clássico)
A Azure Data Factory permite-lhe criar facilmente oleodutos que utilizem um serviço web publicado do Azure Machine Learning Studio (clássico) para análise preditiva. Utilizando a [Atividade de Execução](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) de Lotes num oleoduto Azure Data Factory, pode invocar um serviço web Studio (clássico) para fazer previsões sobre os dados em lote.

Com o tempo, os modelos preditivos no Estúdio (clássico) experiências de pontuação precisam de ser retreinados usando novos conjuntos de dados de entrada. Depois de terminar a reconversão, pretende atualizar o serviço web de pontuação com o modelo de aprendizagem automática retreinado. Pode utilizar a [Atividade de Recursos de Atualização](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) para atualizar o serviço web com o modelo recém-treinado.  

Consulte [as atividades do Use Azure Machine Learning Studio (clássico)](data-factory-azure-ml-batch-execution-activity.md) para mais detalhes sobre estas atividades do Studio (clássico). 

## <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Pode utilizar a atividade do Procedimento Armazenado do SQL server num oleoduto da Data Factory para invocar um procedimento armazenado numa das seguintes lojas de dados: Azure SQL Database, Azure Synapse Analytics, SQL Server Database na sua empresa ou um Azure VM. Consulte o artigo [de atividades do procedimento](data-factory-stored-proc-activity.md) armazenado para mais detalhes.  

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade U-SQL do Data Lake Analytics
Data Lake Analytics U-SQL Activity executa um script U-SQL em um cluster Azure Data Lake Analytics. Consulte o artigo [de atividade u-SQL da Data Analytics](data-factory-usql-activity.md) para obter mais detalhes. 

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Se precisar de transformar dados de uma forma que não seja suportada pela Data Factory, pode criar uma atividade personalizada com a sua própria lógica de processamento de dados e utilizar a atividade no pipeline. Pode configurar a atividade personalizada .NET para executar utilizando um serviço Azure Batch ou um cluster Azure HDInsight. Consulte [o artigo de atividades personalizadas](data-factory-use-custom-activities.md) para obter mais detalhes. 

Pode criar uma atividade personalizada para executar scripts R no seu cluster do HDInsight com R instalado. Veja [Run R Script using Azure Data Factory (Executar Script R com o Azure Data Factory)](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Ambientes computacional
Cria um serviço ligado para o ambiente computacional e, em seguida, utiliza o serviço ligado ao definir uma atividade de transformação. Existem dois tipos de ambientes computativos suportados pela Data Factory. 

1. **A pedido**: Neste caso, o ambiente de computação é totalmente gerido pela Data Factory. É automaticamente criado pelo serviço Data Factory antes de um trabalho ser submetido para processar dados e removido quando o trabalho estiver concluído. Você pode configurar e controlar configurações granulares do ambiente de computação a pedido para execução de emprego, gestão de clusters e ações de bootstrapping. 
2. **Bring Your Own**: Neste caso, pode registar o seu próprio ambiente de computação (por exemplo, cluster HDInsight) como um serviço ligado na Data Factory. O ambiente de computação é gerido por si e o serviço Data Factory utiliza-o para executar as atividades. 

Consulte o artigo [da Compute Linked Services](data-factory-compute-linked-services.md) para saber mais sobre os serviços de computação suportados pela Data Factory. 

## <a name="summary"></a>Resumo
A Azure Data Factory apoia as seguintes atividades de transformação de dados e os ambientes computacionais para as atividades. As atividades de transformação podem ser adicionadas aos gasodutos individualmente ou acorrentados com outra atividade.

| Atividade de transformação de dados | Ambiente de computação |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Atividades do Azure Machine Learning Studio (clássico): Execução de Lote e Recurso de Atualização](data-factory-azure-ml-batch-execution-activity.md) |VM do Azure |
| [Procedimento Armazenado](data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics ou SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] ou Azure Batch |

