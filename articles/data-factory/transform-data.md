---
title: Transformar dados
description: Saiba como transformar dados ou processar dados em Azure Data Factory usando o Hadoop, Machine Learning ou Azure Data Lake Analytics.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2018
author: nabhishek
ms.author: abnarain
manager: shwang
ms.openlocfilehash: dbca107fc35a297c2a0b44b85a1cbb48865c3ab0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893744"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformar dados em Azure Data Factory

> [!div class="op_single_selector"]
> * [Mapeando fluxo de dados](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Streaming do HDInsight](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Procedimento Armazenado](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Bloco de anotações do databricks](transform-data-databricks-notebook.md)
> * [Jar do databricks](transform-data-databricks-jar.md)
> * [Python do databricks](transform-data-databricks-python.md)
> * [Personalizado do .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Visão geral
Este artigo explica as atividades de transformação de dados no Azure Data Factory que você pode usar para transformar e processar seus dados brutos em previsões e ideias em escala. Uma atividade de transformação é executada em um ambiente de computação, como o Azure Databricks ou o Azure HDInsight. Ele fornece links para artigos com informações detalhadas sobre cada atividade de transformação.

O Data Factory dá suporte às seguintes atividades de transformação de dados que podem ser adicionadas aos [pipelines](concepts-pipelines-activities.md) individualmente ou encadeadas com outra atividade.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Transformar nativamente em Azure Data Factory com fluxos de dados

### <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento

O mapeamento de fluxos de dados são transformações de dados visualmente projetadas em Azure Data Factory. Os fluxos de dados permitem que os engenheiros de dados desenvolvam a lógica de transformação de dados gráficos sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro de Azure Data Factory pipelines que usam clusters Spark expandidos. As atividades de fluxo de dados podem ser operadas por meio de recursos de agendamento, controle, fluxo e monitoramento existentes de Data Factory. Para obter mais informações, consulte [mapeando fluxos de dados](concepts-data-flow-overview.md).

### <a name="wrangling-data-flows"></a>Fluxos de dados Wrangling

Os fluxos de dados do Wrangling no Azure Data Factory permitem que você faça uma preparação de dados sem código em escala de nuvem iterativamente. Os fluxos de dados do Wrangling integram-se ao [Power Query online](https://docs.microsoft.com/power-query/) e fazem Power Query funções M disponíveis para os dados Wrangling em escala de nuvem por meio da execução do Spark. Para obter mais informações, consulte [fluxos de dados do Wrangling](wrangling-data-flow-overview.md).

## <a name="external-transformations"></a>Transformações externas

Opcionalmente, você pode codificar manualmente as transformações e gerenciar o ambiente de computação externo.

### <a name="hdinsight-hive-activity"></a>Atividade do hive do HDInsight
A atividade do hive do HDInsight em um pipeline Data Factory executa consultas de Hive em seu próprio cluster do HDInsight baseado em Windows/Linux sob demanda. Consulte o artigo [atividade do hive](transform-data-using-hadoop-hive.md) para obter detalhes sobre essa atividade. 

### <a name="hdinsight-pig-activity"></a>Atividade de Pig do HDInsight
A atividade Pig do HDInsight em um pipeline Data Factory executa consultas Pig em seu próprio cluster do HDInsight baseado em Windows/Linux sob demanda. Consulte o artigo [atividade de Pig](transform-data-using-hadoop-pig.md) para obter detalhes sobre essa atividade. 

### <a name="hdinsight-mapreduce-activity"></a>Atividade de MapReduce do HDInsight
A atividade de MapReduce do HDInsight em um pipeline de Data Factory executa programas MapReduce em seu próprio cluster do HDInsight baseado em Windows/Linux sob demanda. Consulte o artigo [atividade do MapReduce](transform-data-using-hadoop-map-reduce.md) para obter detalhes sobre essa atividade.

### <a name="hdinsight-streaming-activity"></a>Atividade de streaming do HDInsight
A atividade de streaming do HDInsight em um pipeline Data Factory executa programas de streaming do Hadoop em seu próprio cluster do HDInsight baseado em Windows/Linux sob demanda. Consulte a [atividade de streaming do HDInsight](transform-data-using-hadoop-streaming.md) para obter detalhes sobre essa atividade.

### <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
A atividade do HDInsight Spark em um pipeline Data Factory executa programas do Spark em seu próprio cluster HDInsight. Para obter detalhes, consulte [invocar programas Spark de Azure data Factory](transform-data-using-spark.md). 

### <a name="machine-learning-activities"></a>Atividades de Machine Learning
Azure Data Factory permite que você crie facilmente pipelines que usam um serviço Web publicado Azure Machine Learning para análise preditiva. Usando a [atividade de execução em lote](transform-data-using-machine-learning.md) em um pipeline Azure data Factory, você pode invocar um serviço Web Machine Learning para fazer previsões sobre os dados no lote.

Com o passar do tempo, os modelos de previsão no Machine Learning a Pontuação dos experimentos precisam ser retreinados usando novos conjuntos de dados de entrada. Depois de concluir o retreinamento, você deseja atualizar o serviço Web de pontuação com o modelo de Machine Learning retreinado. Você pode usar a [atividade atualizar recurso](update-machine-learning-models.md) para atualizar o serviço Web com o modelo treinado recentemente.  

Consulte [usar Machine Learning atividades](transform-data-using-machine-learning.md) para obter detalhes sobre essas Machine Learning atividades. 

### <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Você pode usar a SQL Server atividade de procedimento armazenado em um pipeline de Data Factory para invocar um procedimento armazenado em um dos seguintes repositórios de dados: banco de dados SQL do Azure, Azure SQL Data Warehouse, banco de SQL Server em sua empresa ou uma VM do Azure. Consulte o artigo [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) para obter detalhes.  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics atividade U-SQL
Data Lake Analytics atividade U-SQL executa um script U-SQL em um cluster Azure Data Lake Analytics. Consulte o artigo [atividade do U-SQL da análise de dados](transform-data-using-data-lake-analytics.md) para obter detalhes. 

### <a name="databricks-notebook-activity"></a>Atividade do databricks Notebook

A atividade de Azure Databricks notebook em um pipeline de Data Factory executa um notebook do databricks em seu espaço de trabalho do Azure Databricks. Azure Databricks é uma plataforma gerenciada para executar o Apache Spark. Consulte [transformar dados executando um notebook do databricks](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Atividade de jar do databricks

A atividade de Azure Databricks jar em um pipeline de Data Factory executa um jar do Spark em seu cluster de Azure Databricks. Azure Databricks é uma plataforma gerenciada para executar o Apache Spark. Consulte [transformar dados executando uma atividade jar em Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Atividade de Python do databricks

A atividade Azure Databricks Python em um pipeline Data Factory executa um arquivo Python em seu cluster de Azure Databricks. Azure Databricks é uma plataforma gerenciada para executar o Apache Spark. Consulte [transformar dados executando uma atividade do Python no Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Atividade personalizada
Se você precisar transformar dados de uma maneira que não seja suportada pelo Data Factory, poderá criar uma atividade personalizada com sua própria lógica de processamento de dados e usar a atividade no pipeline. Você pode configurar a atividade personalizada do .NET para ser executada usando um serviço de lote do Azure ou um cluster do Azure HDInsight. Consulte o artigo [usar atividades personalizadas](transform-data-using-dotnet-custom-activity.md) para obter detalhes. 

Pode criar uma atividade personalizada para executar scripts R no seu cluster do HDInsight com R instalado. Veja [Run R Script using Azure Data Factory (Executar Script R com o Azure Data Factory)](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Ambientes de computação
Você cria um serviço vinculado para o ambiente de computação e, em seguida, usa o serviço vinculado ao definir uma atividade de transformação. Há dois tipos de ambientes de computação com suporte pelo Data Factory. 

- **Sob demanda**: nesse caso, o ambiente de computação é totalmente gerenciado pelo data Factory. Ele é criado automaticamente pelo serviço de Data Factory antes de um trabalho ser enviado para processar dados e removido quando o trabalho for concluído. Você pode configurar e controlar as configurações granulares do ambiente de computação sob demanda para execução de trabalho, gerenciamento de cluster e ações de inicialização. 
- **Traga seu próprio**: nesse caso, você pode registrar seu próprio ambiente de computação (por exemplo, o cluster HDInsight) como um serviço vinculado no data Factory. O ambiente de computação é gerenciado por você e o serviço de Data Factory o utiliza para executar as atividades. 

Consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) para saber mais sobre os serviços de computação com suporte pelo data Factory. 

## <a name="next-steps"></a>Passos seguintes
Consulte o tutorial a seguir para obter um exemplo de como usar uma atividade de transformação: [tutorial: transformar dados usando o Spark](tutorial-transform-data-spark-powershell.md)
