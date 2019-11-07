---
title: 'Transformação de dados: processar & transformar dados '
description: Saiba como transformar dados ou processar dados em Azure Data Factory usando o Hadoop, Machine Learning ou Azure Data Lake Analytics.
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
ms.openlocfilehash: 3af01ef76421e95f74a65fe597cafff5fa9ef39e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682617"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformar dados em Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [Personalizado do .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Descrição geral
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [atividades de transformação de dados em data Factory](../transform-data.md).

Este artigo explica as atividades de transformação de dados no Azure Data Factory que você pode usar para transformar e processar seus dados brutos em previsões e ideias. Uma atividade de transformação é executada em um ambiente de computação, como o cluster do Azure HDInsight ou um lote do Azure. Ele fornece links para artigos com informações detalhadas sobre cada atividade de transformação.

O Data Factory dá suporte às seguintes atividades de transformação de dados que podem ser adicionadas aos [pipelines](data-factory-create-pipelines.md) individualmente ou encadeadas com outra atividade.

> [!NOTE]
> Para obter instruções passo a passo, confira o artigo [criar um pipeline com a transformação do hive](data-factory-build-your-first-pipeline.md) .  
> 
> 

## <a name="hdinsight-hive-activity"></a>Atividade do hive do HDInsight
A atividade do hive do HDInsight em um pipeline Data Factory executa consultas de Hive em seu próprio cluster do HDInsight baseado em Windows/Linux sob demanda. Consulte o artigo [atividade do hive](data-factory-hive-activity.md) para obter detalhes sobre essa atividade. 

## <a name="hdinsight-pig-activity"></a>Atividade de Pig do HDInsight
A atividade Pig do HDInsight em um pipeline Data Factory executa consultas Pig em seu próprio cluster do HDInsight baseado em Windows/Linux sob demanda. Consulte o artigo [atividade de Pig](data-factory-pig-activity.md) para obter detalhes sobre essa atividade. 

## <a name="hdinsight-mapreduce-activity"></a>Atividade de MapReduce do HDInsight
A atividade de MapReduce do HDInsight em um pipeline de Data Factory executa programas MapReduce em seu próprio cluster do HDInsight baseado em Windows/Linux sob demanda. Consulte o artigo [atividade do MapReduce](data-factory-map-reduce.md) para obter detalhes sobre essa atividade.

## <a name="hdinsight-streaming-activity"></a>Atividade de streaming do HDInsight
A atividade de streaming do HDInsight em um pipeline Data Factory executa programas de streaming do Hadoop em seu próprio cluster do HDInsight baseado em Windows/Linux sob demanda. Consulte a [atividade de streaming do HDInsight](data-factory-hadoop-streaming-activity.md) para obter detalhes sobre essa atividade.

## <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
A atividade do HDInsight Spark em um pipeline Data Factory executa programas do Spark em seu próprio cluster HDInsight. Para obter detalhes, consulte [invocar programas Spark de Azure data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Atividades de Machine Learning
Azure Data Factory permite que você crie facilmente pipelines que usam um serviço Web publicado Azure Machine Learning para análise preditiva. Usando a [atividade de execução em lote](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) em um pipeline Azure data Factory, você pode invocar um serviço Web Machine Learning para fazer previsões sobre os dados no lote.

Com o passar do tempo, os modelos de previsão no Machine Learning a Pontuação dos experimentos precisam ser retreinados usando novos conjuntos de dados de entrada. Depois de concluir o retreinamento, você deseja atualizar o serviço Web de pontuação com o modelo de Machine Learning retreinado. Você pode usar a [atividade atualizar recurso](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) para atualizar o serviço Web com o modelo treinado recentemente.  

Consulte [usar Machine Learning atividades](data-factory-azure-ml-batch-execution-activity.md) para obter detalhes sobre essas Machine Learning atividades. 

## <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Você pode usar a SQL Server atividade de procedimento armazenado em um pipeline de Data Factory para invocar um procedimento armazenado em um dos seguintes repositórios de dados: banco de dados SQL do Azure, Azure SQL Data Warehouse, banco de SQL Server em sua empresa ou uma VM do Azure. Consulte o artigo [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para obter detalhes.  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics atividade U-SQL
Data Lake Analytics atividade U-SQL executa um script U-SQL em um cluster Azure Data Lake Analytics. Consulte o artigo [atividade do U-SQL da análise de dados](data-factory-usql-activity.md) para obter detalhes. 

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Se você precisar transformar dados de uma maneira que não seja suportada pelo Data Factory, poderá criar uma atividade personalizada com sua própria lógica de processamento de dados e usar a atividade no pipeline. Você pode configurar a atividade personalizada do .NET para ser executada usando um serviço de lote do Azure ou um cluster do Azure HDInsight. Consulte o artigo [usar atividades personalizadas](data-factory-use-custom-activities.md) para obter detalhes. 

Pode criar uma atividade personalizada para executar scripts R no seu cluster do HDInsight com R instalado. Veja [Run R Script using Azure Data Factory (Executar Script R com o Azure Data Factory)](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Ambientes de computação
Você cria um serviço vinculado para o ambiente de computação e, em seguida, usa o serviço vinculado ao definir uma atividade de transformação. Há dois tipos de ambientes de computação com suporte pelo Data Factory. 

1. **Sob demanda**: nesse caso, o ambiente de computação é totalmente gerenciado pelo data Factory. Ele é criado automaticamente pelo serviço de Data Factory antes de um trabalho ser enviado para processar dados e removido quando o trabalho for concluído. Você pode configurar e controlar as configurações granulares do ambiente de computação sob demanda para execução de trabalho, gerenciamento de cluster e ações de inicialização. 
2. **Traga seu próprio**: nesse caso, você pode registrar seu próprio ambiente de computação (por exemplo, o cluster HDInsight) como um serviço vinculado no data Factory. O ambiente de computação é gerenciado por você e o serviço de Data Factory o utiliza para executar as atividades. 

Consulte o artigo [Serviços vinculados de computação](data-factory-compute-linked-services.md) para saber mais sobre os serviços de computação com suporte pelo data Factory. 

## <a name="summary"></a>Resumo
O Azure Data Factory dá suporte às seguintes atividades de transformação de dados e ambientes de computação para as atividades. As atividades de transformação podem ser adicionadas aos pipelines individualmente ou encadeadas com outra atividade.

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

