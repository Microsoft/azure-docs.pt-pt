---
title: Transformar dados com o Azure Data Factory | Documentos da Microsoft
description: Saiba como transformar dados ou processar os dados no Azure Data Factory com o Hadoop, o Machine Learning ou o Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 2c674f77ef0f779c9764771e2e0ae7a4aea47548
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575227"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformar dados no Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight de transmissão em fluxo](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Procedimento Armazenado](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [O bloco de notas do Databricks](transform-data-databricks-notebook.md)
> * [Jar do Databricks](transform-data-databricks-jar.md)
> * [Python do Databricks](transform-data-databricks-python.md)
> * [.NET personalizado](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Descrição geral
Este artigo explica as atividades de transformação de dados no Azure Data Factory, que pode utilizar para transformar e processa os dados não processados em predições e informações. Uma atividade de transformação seja executado num ambiente de computação, como o Azure HDInsight cluster ou um Azure Batch. Fornece links para artigos com informações detalhadas sobre cada atividade de transformação.

O Data Factory suporta as seguintes atividades de transformação de dados que podem ser adicionadas ao [pipelines](concepts-pipelines-activities.md) seja individualmente ou encadeadas com outra atividade.

## <a name="hdinsight-hive-activity"></a>Atividade do Hive do HDInsight
A atividade do HDInsight Hive no pipeline do Data Factory executa consultas do Hive por conta própria ou cluster do HDInsight baseado em Windows/Linux por demanda. Ver [atividade do Hive](transform-data-using-hadoop-hive.md) artigo para obter detalhes sobre esta atividade. 

## <a name="hdinsight-pig-activity"></a>Atividade Pig do HDInsight
A atividade Pig do HDInsight no pipeline do Data Factory executa consultas de Pig por conta própria ou cluster do HDInsight baseado em Windows/Linux por demanda. Ver [Pig atividade](transform-data-using-hadoop-pig.md) artigo para obter detalhes sobre esta atividade. 

## <a name="hdinsight-mapreduce-activity"></a>Atividade MapReduce do HDInsight
A atividade de MapReduce do HDInsight no pipeline do Data Factory executa programas MapReduce por conta própria ou cluster do HDInsight baseado em Windows/Linux por demanda. Ver [atividade MapReduce](transform-data-using-hadoop-map-reduce.md) artigo para obter detalhes sobre esta atividade.

## <a name="hdinsight-streaming-activity"></a>Atividade Streaming do HDInsight
A atividade Streaming do HDInsight no pipeline do Data Factory executa programas de transmissão em fluxo do Hadoop de mensagens em fila por conta própria ou cluster do HDInsight baseado em Windows/Linux por demanda. Ver [atividade Streaming do HDInsight](transform-data-using-hadoop-streaming.md) para obter detalhes sobre esta atividade.

## <a name="hdinsight-spark-activity"></a>Atividade do Spark do HDInsight
A atividade do Spark do HDInsight no pipeline do Data Factory executa programas do Spark no seu próprio cluster do HDInsight. Para obter detalhes, consulte [programas do Spark de invocar do Azure Data Factory](transform-data-using-spark.md). 

## <a name="machine-learning-activities"></a>Atividades do Machine Learning
O Azure Data Factory permite-lhe facilmente criar pipelines que utilizem um serviço de web publicado do Azure Machine Learning para Análise Preditiva. Utilizar o [atividade de execução de lotes](transform-data-using-machine-learning.md) num pipeline do Azure Data Factory, pode invocar um serviço web do Machine Learning para fazer previsões sobre os dados no batch.

Ao longo do tempo, os modelos preditivos da classificação experimentações do Machine Learning necessário reestruturar utilizando conjuntos de dados de entrada novo. Depois de terminar com reparametrização, pretende atualizar o serviço web de pontuação com o modelo de Machine Learning retrained. Pode utilizar o [atividade de recursos de atualização](update-machine-learning-models.md) para atualizar o serviço web com o modelo treinado recentemente.  

Ver [atividades de utilização Machine Learning](transform-data-using-machine-learning.md) para obter detalhes sobre estas atividades de Machine Learning. 

## <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Pode utilizar a atividade Stored Procedure do SQL Server num pipeline do Data Factory para invocar um procedimento armazenado em um dos arquivos de dados seguintes: SQL Database do Azure, Azure SQL Data Warehouse, base de dados do SQL Server na sua empresa ou uma VM do Azure. Ver [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) artigo para obter detalhes.  

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade U-SQL do Data Lake Analytics
Atividade do Data Lake Analytics U-SQL executa um script de U-SQL num cluster do Azure Data Lake Analytics. Ver [atividade de U-SQL de análise de dados](transform-data-using-data-lake-analytics.md) artigo para obter detalhes. 

## <a name="databricks-notebook-activity"></a>Atividade Databricks Notebook

A atividade do Azure Databricks Notebook no pipeline do Data Factory executa um Databricks notebook na sua área de trabalho do Azure Databricks. O Azure Databricks é uma plataforma gerida para a execução do Apache Spark. Ver [transformar dados ao executar um Databricks notebook](transform-data-databricks-notebook.md).

## <a name="databricks-jar-activity"></a>Atividade do Databricks Jar

O Azure Databricks Jar atividade no pipeline do Data Factory é executado um Jar do Spark no seu cluster do Azure Databricks. O Azure Databricks é uma plataforma gerida para a execução do Apache Spark. Ver [transformar dados através da execução de uma atividade de Jar no Azure Databricks](transform-data-databricks-jar.md).

## <a name="databricks-python-activity"></a>Atividade do Databricks Python

A atividade de Python do Azure Databricks no pipeline do Data Factory executa um ficheiro de Python no seu cluster do Azure Databricks. O Azure Databricks é uma plataforma gerida para a execução do Apache Spark. Ver [transformar dados através da execução de uma atividade de Python no Azure Databricks](transform-data-databricks-python.md).

## <a name="custom-activity"></a>Atividade personalizada
Se precisar de transformar os dados de uma forma que não é suportado pelo Data Factory, pode criar uma atividade personalizada com a sua própria lógica de processamento de dados e utilize a atividade no pipeline. Pode configurar a atividade .NET personalizada a ser realizada usando um serviço do Azure Batch ou um cluster do HDInsight do Azure. Ver [utilizar atividades personalizadas](transform-data-using-dotnet-custom-activity.md) artigo para obter detalhes. 

Pode criar uma atividade personalizada para executar scripts R no seu cluster do HDInsight com R instalado. Veja [Run R Script using Azure Data Factory (Executar Script R com o Azure Data Factory)](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Ambientes de computação
Criar um serviço ligado para o ambiente de computação e, em seguida, utilizar o serviço ligado ao definir uma atividade de transformação. Existem dois tipos de ambientes de computação suportados pelo Data Factory. 

- **Sob demanda**:  Neste caso, o ambiente de computação totalmente gerido pela fábrica de dados. Ele é criado automaticamente pelo serviço Data Factory antes de uma tarefa é submetida para processar dados e removida quando a tarefa estiver concluída. Pode configurar e controlar as definições do granulares do ambiente de computação a pedido para execução da tarefa, gerenciamento de cluster e ações de inicialização. 
- **Traga a sua própria**: Neste caso, pode registrar seu próprio ambiente de computação (por exemplo o cluster de HDInsight) como um serviço ligado no Data Factory. O ambiente de computação é gerenciado por si e o serviço Data Factory utiliza para executar as atividades. 

Ver [serviços ligados de computação](compute-linked-services.md) artigo para saber mais sobre os serviços de computação suportados pelo Data Factory. 

## <a name="next-steps"></a>Passos Seguintes
Veja o tutorial seguinte para obter um exemplo da utilização de uma atividade de transformação: [Tutorial: transformar dados com o Spark](tutorial-transform-data-spark-powershell.md)
