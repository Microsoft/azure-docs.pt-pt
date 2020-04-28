---
title: 'Transformação de Dados: Processar & transformar dados '
description: Saiba como transformar dados ou processar dados na Azure Data Factory utilizando Hadoop, Machine Learning ou Azure Data Lake Analytics.
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
ms.openlocfilehash: 5b3e2db9b9769dee7599a2446b272e04cc0bedf7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74703381"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformar dados no Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Aprendizagem automática](data-factory-azure-ml-batch-execution-activity.md) 
> * [Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET costume](data-factory-use-custom-activities.md)

## <a name="overview"></a>Descrição geral
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte as atividades de transformação de [dados na Data Factory.](../transform-data.md)

Este artigo explica as atividades de transformação de dados na Azure Data Factory que pode usar para transformar e processar os seus dados brutos em previsões e insights. Uma atividade de transformação executa num ambiente de computação como o cluster Azure HDInsight ou um Lote Azure. Fornece links para artigos com informações detalhadas sobre cada atividade de transformação.

Data Factory suporta as seguintes atividades de transformação de dados que podem ser adicionadas a [oleodutos](data-factory-create-pipelines.md) individualmente ou acorrentados a outra atividade.

> [!NOTE]
> Para um passeio com instruções passo a passo, consulte Criar um oleoduto com artigo de transformação da [Hive.](data-factory-build-your-first-pipeline.md)  
> 
> 

## <a name="hdinsight-hive-activity"></a>Atividade da Colmeia HDInsight
A atividade da Hive HDInsight num pipeline data factory executa consultas de Hive por si só ou a pedido do cluster HDInsight baseado em HDInsight. Consulte o artigo da [Atividade da Colmeia](data-factory-hive-activity.md) para mais detalhes sobre esta atividade. 

## <a name="hdinsight-pig-activity"></a>Atividade de porco HDInsight
A atividade do Suíno HDInsight num gasoduto data Factory executa consultas de porco por si só ou a pedido do cluster HDInsight baseado em Linux. Consulte o artigo [da Atividade do Porco](data-factory-pig-activity.md) para mais detalhes sobre esta atividade. 

## <a name="hdinsight-mapreduce-activity"></a>Mapa HDInsightReduzir a atividade
O HDInsight MapReduce atividade num pipeline data factory executa programas MapReduce por si só ou a pedido do cluster HDInsight baseado em HDInsight. Consulte o artigo [mapReduce Activity](data-factory-map-reduce.md) para mais detalhes sobre esta atividade.

## <a name="hdinsight-streaming-activity"></a>Atividade de streaming HDInsight
A Atividade de Streaming HDInsight num pipeline data Factory executa programas de Streaming Hadoop por conta própria ou a pedido do cluster HDInsight baseado em HDInsight baseado em HDInsight. Consulte a [atividade de Streaming HDInsight](data-factory-hadoop-streaming-activity.md) para obter detalhes sobre esta atividade.

## <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
A atividade HDInsight Spark num pipeline data Factory executa programas Spark no seu próprio cluster HDInsight. Para mais detalhes, consulte [os programas Invoke Spark da Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Atividades de Aprendizagem Automática
A Azure Data Factory permite-lhe criar facilmente oleodutos que utilizem um serviço web azure machine learning publicado para análise preditiva. Utilizando a Atividade de Execução de [Lotes](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) num oleoduto Azure Data Factory, pode invocar um serviço web de Aprendizagem automática para fazer previsões sobre os dados em lote.

Com o tempo, os modelos preditivos nas experiências de pontuação de Machine Learning precisam de ser retreinados usando novos conjuntos de dados de entrada. Depois de terminar a reconversão, pretende atualizar o serviço web de pontuação com o modelo de Machine Learning retreinado. Pode utilizar a Atividade de [Recursos atualizados](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) para atualizar o serviço web com o modelo recém-treinado.  

Consulte [as atividades de Aprendizagem automática](data-factory-azure-ml-batch-execution-activity.md) para mais detalhes sobre estas atividades de Machine Learning. 

## <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Pode utilizar a atividade do Procedimento Armazenado do Servidor SQL num pipeline data factory para invocar um procedimento armazenado numa das seguintes lojas de dados: Base de Dados Azure SQL, Armazém de Dados Azure SQL, Base de Dados de ServidorEs SQL na sua empresa ou um VM Azure. Consulte o artigo [atividade do procedimento armazenado](data-factory-stored-proc-activity.md) para mais detalhes.  

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade u-SQL do Lago de Dados
Data Lake Analytics U-SQL Activity executa um script U-SQL em um cluster Azure Data Lake Analytics. Consulte o artigo [de Atividade U-SQL](data-factory-usql-activity.md) de Análise de Dados para mais detalhes. 

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Se precisar de transformar dados de uma forma que não seja suportada pela Data Factory, pode criar uma atividade personalizada com a sua própria lógica de processamento de dados e utilizar a atividade no pipeline. Pode configurar a atividade personalizada .NET para funcionar utilizando um serviço De Lote Azure ou um cluster Azure HDInsight. Consulte o artigo [de atividades personalizadas](data-factory-use-custom-activities.md) para mais detalhes. 

Pode criar uma atividade personalizada para executar scripts R no seu cluster do HDInsight com R instalado. Veja [Run R Script using Azure Data Factory (Executar Script R com o Azure Data Factory)](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Ambientes computacionais
Cria-se um serviço ligado ao ambiente de computação e depois utiliza-se o serviço ligado na definição de uma atividade de transformação. Existem dois tipos de ambientes computacionais suportados pela Data Factory. 

1. **On-Demand**: Neste caso, o ambiente de computação é totalmente gerido pela Data Factory. É automaticamente criado pelo serviço Data Factory antes de um trabalho ser submetido para processar dados e removido quando o trabalho está concluído. Pode configurar e controlar as configurações granulares do ambiente de computação a pedido para execução de emprego, gestão de clusters e ações de saque. 
2. **Bring Your Own**: Neste caso, pode registar o seu próprio ambiente de computação (por exemplo, cluster HDInsight) como um serviço ligado na Data Factory. O ambiente de computação é gerido por si e o serviço Data Factory utiliza-o para executar as atividades. 

Consulte o artigo [da Compute Linked Services](data-factory-compute-linked-services.md) para conhecer os serviços de computação suportados pela Data Factory. 

## <a name="summary"></a>Resumo
A Azure Data Factory apoia as seguintes atividades de transformação de dados e os ambientes computacionais para as atividades. As atividades de transformação podem ser adicionadas aos gasodutos individualmente ou acorrentados a outra atividade.

| Atividade de transformação de dados | Ambiente de computação |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Atividades de Machine Learning: Execução de Lotes e Atualizar Recurso](data-factory-azure-ml-batch-execution-activity.md) |VM do Azure |
| [Procedimento Armazenado](data-factory-stored-proc-activity.md) |SQL Azure, Azure SQL Data Warehouse ou SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] ou Azure Batch |

