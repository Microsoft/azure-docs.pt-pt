---
title: 'Transformação de dados: Processo e transformar dados | Documentos da Microsoft'
description: Saiba como transformar dados ou processar os dados no Azure Data Factory com o Hadoop, o Machine Learning ou o Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 39786731-1e4b-40a4-81b7-d06e127427aa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b1a99f2872a69e01232c69a73f36319552429ca0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60486605"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformar dados no Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET personalizado](data-factory-use-custom-activities.md)

## <a name="overview"></a>Descrição geral
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [atividades de transformação de dados no Data Factory](../transform-data.md).

Este artigo explica as atividades de transformação de dados no Azure Data Factory, que pode utilizar para transformar e processa os dados não processados em predições e informações. Uma atividade de transformação seja executado num ambiente de computação, como o Azure HDInsight cluster ou um Azure Batch. Fornece links para artigos com informações detalhadas sobre cada atividade de transformação.

O Data Factory suporta as seguintes atividades de transformação de dados que podem ser adicionadas ao [pipelines](data-factory-create-pipelines.md) seja individualmente ou encadeadas com outra atividade.

> [!NOTE]
> Para obter instruções com instruções passo a passo, consulte [criar um pipeline com a transformação do Hive](data-factory-build-your-first-pipeline.md) artigo.  
> 
> 

## <a name="hdinsight-hive-activity"></a>Atividade do Hive do HDInsight
A atividade do HDInsight Hive no pipeline do Data Factory executa consultas do Hive por conta própria ou cluster do HDInsight baseado em Windows/Linux por demanda. Ver [atividade do Hive](data-factory-hive-activity.md) artigo para obter detalhes sobre esta atividade. 

## <a name="hdinsight-pig-activity"></a>Atividade Pig do HDInsight
A atividade Pig do HDInsight no pipeline do Data Factory executa consultas de Pig por conta própria ou cluster do HDInsight baseado em Windows/Linux por demanda. Ver [atividade Pig](data-factory-pig-activity.md) artigo para obter detalhes sobre esta atividade. 

## <a name="hdinsight-mapreduce-activity"></a>Atividade MapReduce do HDInsight
A atividade de MapReduce do HDInsight no pipeline do Data Factory executa programas MapReduce por conta própria ou cluster do HDInsight baseado em Windows/Linux por demanda. Ver [atividade MapReduce](data-factory-map-reduce.md) artigo para obter detalhes sobre esta atividade.

## <a name="hdinsight-streaming-activity"></a>Atividade Streaming do HDInsight
A atividade de transmissão em fluxo do HDInsight no pipeline do Data Factory executa programas de transmissão em fluxo do Hadoop de mensagens em fila por conta própria ou cluster do HDInsight baseado em Windows/Linux por demanda. Ver [atividade Streaming do HDInsight](data-factory-hadoop-streaming-activity.md) para obter detalhes sobre esta atividade.

## <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
A atividade do Spark do HDInsight no pipeline do Data Factory executa programas do Spark no seu próprio cluster do HDInsight. Para obter detalhes, consulte [programas do Spark de invocar do Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Atividades do Machine Learning
O Azure Data Factory permite-lhe facilmente criar pipelines que utilizem um serviço de web publicado do Azure Machine Learning para Análise Preditiva. Utilizar o [atividade de execução de Batch](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) num pipeline do Azure Data Factory, pode invocar um serviço web do Machine Learning para fazer previsões sobre os dados no batch.

Ao longo do tempo, os modelos preditivos da classificação experimentações do Machine Learning necessário reestruturar utilizando conjuntos de dados de entrada novo. Depois de terminar com reparametrização, pretende atualizar o serviço web de pontuação com o modelo de Machine Learning retrained. Pode utilizar o [atividade de recursos de atualização](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) para atualizar o serviço web com o modelo treinado recentemente.  

Ver [atividades de utilização Machine Learning](data-factory-azure-ml-batch-execution-activity.md) para obter detalhes sobre estas atividades de Machine Learning. 

## <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Pode utilizar a atividade Stored Procedure do SQL Server num pipeline do Data Factory para invocar um procedimento armazenado em um dos arquivos de dados seguintes: SQL Database do Azure, Azure SQL Data Warehouse, base de dados do SQL Server na sua empresa ou uma VM do Azure. Ver [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) artigo para obter detalhes.  

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade U-SQL do Data Lake Analytics
Atividade de U-SQL do Data Lake Analytics executa um script de U-SQL num cluster do Azure Data Lake Analytics. Ver [atividade de U-SQL de análise de dados](data-factory-usql-activity.md) artigo para obter detalhes. 

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Se precisar de transformar os dados de uma forma que não é suportado pelo Data Factory, pode criar uma atividade personalizada com a sua própria lógica de processamento de dados e utilize a atividade no pipeline. Pode configurar a atividade .NET personalizada a ser realizada usando um serviço do Azure Batch ou um cluster do HDInsight do Azure. Ver [utilizar atividades personalizadas](data-factory-use-custom-activities.md) artigo para obter detalhes. 

Pode criar uma atividade personalizada para executar scripts R no seu cluster do HDInsight com R instalado. Veja [Run R Script using Azure Data Factory (Executar Script R com o Azure Data Factory)](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Ambientes de computação
Criar um serviço ligado para o ambiente de computação e, em seguida, utilizar o serviço ligado ao definir uma atividade de transformação. Existem dois tipos de ambientes de computação suportados pelo Data Factory. 

1. **Sob demanda**:  Neste caso, o ambiente de computação totalmente gerido pela fábrica de dados. Ele é criado automaticamente pelo serviço Data Factory antes de uma tarefa é submetida para processar dados e removida quando a tarefa estiver concluída. Pode configurar e controlar as definições do granulares do ambiente de computação a pedido para execução da tarefa, gerenciamento de cluster e ações de inicialização. 
2. **Traga a sua própria**: Neste caso, pode registrar seu próprio ambiente de computação (por exemplo o cluster de HDInsight) como um serviço ligado no Data Factory. O ambiente de computação é gerenciado por si e o serviço Data Factory utiliza para executar as atividades. 

Ver [serviços ligados de computação](data-factory-compute-linked-services.md) artigo para saber mais sobre os serviços de computação suportados pelo Data Factory. 

## <a name="summary"></a>Resumo
O Azure Data Factory suporta as seguintes atividades de transformação de dados e os ambientes de computação para as atividades. As atividades de transformação podem ser adicionadas a pipelines individualmente ou encadeadas com outra atividade.

| Atividade de transformação de dados | Ambiente de computação |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Atividades do Machine Learning: Execução de lotes e atualizar recurso](data-factory-azure-ml-batch-execution-activity.md) |VM do Azure |
| [Procedimento Armazenado](data-factory-stored-proc-activity.md) |SQL Azure, Azure SQL Data Warehouse ou SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] ou Azure Batch |

