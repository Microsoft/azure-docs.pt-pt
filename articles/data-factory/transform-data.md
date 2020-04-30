---
title: Transformar dados
description: Saiba como transformar dados ou processar dados na Azure Data Factory utilizando Hadoop, Machine Learning ou Azure Data Lake Analytics.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: fdb10894ab9d1c6b805b8b43c90e54126d67b8dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606620"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformar dados no Azure Data Factory

> [!div class="op_single_selector"]
> * [Mapeando o fluxo de dados](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Aprendizagem automática](transform-data-using-machine-learning.md) 
> * [Procedimento Armazenado](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Caderno de tijolos de dados](transform-data-databricks-notebook.md)
> * [Frasco de tijolos de dados](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET costume](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Descrição geral
Este artigo explica as atividades de transformação de dados na Azure Data Factory que pode usar para transformar e processar os seus dados brutos em previsões e insights em escala. Uma atividade de transformação executa num ambiente de computação como O Azure Databricks ou Azure HDInsight. Fornece links para artigos com informações detalhadas sobre cada atividade de transformação.

Data Factory suporta as seguintes atividades de transformação de dados que podem ser adicionadas a [oleodutos](concepts-pipelines-activities.md) individualmente ou acorrentados a outra atividade.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Transforme de forma nativa na Fábrica de Dados Azure com fluxos de dados

### <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento

Os fluxos de dados de mapeamento são transformações de dados projetadas visualmente na Azure Data Factory. Os fluxos de dados permitem que os engenheiros de dados desenvolvam lógica de transformação gráfica de dados sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro dos oleodutos da Azure Data Factory que utilizam clusters de faíscas escaldados. As atividades de fluxo de dados podem ser operacionalizadas através das capacidades de agendamento, controlo, fluxo e monitorização existentes da Data Factory. Para mais informações, consulte os fluxos de [dados de mapeamento.](concepts-data-flow-overview.md)

### <a name="wrangling-data-flows"></a>Fluxos de dados de estrangulamento

Os fluxos de dados em Azure Data Factory permitem-lhe fazer a preparação de dados sem código sem código sem escala em escala de nuvem iterativamente. Os fluxos de dados de contorcer-se integram-se com a [Power Query Online](https://docs.microsoft.com/power-query/) e disponibiliza funções de Power Query M para a luta de dados à escala de nuvem através da execução de faíscas. Para obter mais informações, consulte os fluxos de [dados de sangling](wrangling-data-flow-overview.md).

## <a name="external-transformations"></a>Transformações externas

Opcionalmente, você pode fazer transformações de código manual e gerir o ambiente de computação externa por si mesmo.

### <a name="hdinsight-hive-activity"></a>Atividade da Colmeia HDInsight
A atividade da Hive HDInsight num pipeline data factory executa consultas de Hive por si só ou a pedido do cluster HDInsight baseado em HDInsight. Consulte o artigo de [atividade da Hive](transform-data-using-hadoop-hive.md) para mais detalhes sobre esta atividade. 

### <a name="hdinsight-pig-activity"></a>Atividade de porco HDInsight
A atividade do Suíno HDInsight num gasoduto data Factory executa consultas de porco por si só ou a pedido do cluster HDInsight baseado em Linux. Consulte o artigo de [atividade do Porco](transform-data-using-hadoop-pig.md) para mais detalhes sobre esta atividade. 

### <a name="hdinsight-mapreduce-activity"></a>Mapa HDInsightReduzir a atividade
O HDInsight MapReduce atividade num pipeline data factory executa programas MapReduce por si só ou a pedido do cluster HDInsight baseado em HDInsight. Consulte o artigo [de atividade MapReduce](transform-data-using-hadoop-map-reduce.md) para mais detalhes sobre esta atividade.

### <a name="hdinsight-streaming-activity"></a>Atividade de streaming HDInsight
A atividade de streaming HDInsight num pipeline data factory executa programas de Streaming Hadoop por conta própria ou a pedido do cluster HDInsight baseado em HDInsight baseado em HDInsight. Consulte a [atividade de Streaming HDInsight](transform-data-using-hadoop-streaming.md) para obter detalhes sobre esta atividade.

### <a name="hdinsight-spark-activity"></a>Atividade de Faísca HDInsight
A atividade HDInsight Spark num pipeline data Factory executa programas Spark no seu próprio cluster HDInsight. Para mais detalhes, consulte [os programas Invoke Spark da Azure Data Factory](transform-data-using-spark.md). 

### <a name="machine-learning-activities"></a>Atividades de Aprendizagem Automática
A Azure Data Factory permite-lhe criar facilmente oleodutos que utilizem um serviço web azure machine learning publicado para análise preditiva. Utilizando a atividade de [execução](transform-data-using-machine-learning.md) de lote num oleoduto Azure Data Factory, pode invocar um serviço web de Machine Learning para fazer previsões sobre os dados em lote.

Com o tempo, os modelos preditivos nas experiências de pontuação de Machine Learning precisam de ser retreinados usando novos conjuntos de dados de entrada. Depois de terminar a reconversão, pretende atualizar o serviço web de pontuação com o modelo de Machine Learning retreinado. Pode utilizar a [atividade do Recurso atualizar](update-machine-learning-models.md) o serviço web com o modelo recém-treinado.  

Consulte [as atividades de Aprendizagem automática](transform-data-using-machine-learning.md) para mais detalhes sobre estas atividades de Machine Learning. 

### <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Pode utilizar a atividade do Procedimento Armazenado do Servidor SQL num pipeline data factory para invocar um procedimento armazenado numa das seguintes lojas de dados: Base de Dados Azure SQL, Armazém de Dados Azure SQL, Base de Dados de ServidorEs SQL na sua empresa ou um VM Azure. Consulte o artigo de [atividade do Procedimento Armazenado](transform-data-using-stored-procedure.md) para obter mais detalhes.  

### <a name="data-lake-analytics-u-sql-activity"></a>Atividade u-SQL do Lago de Dados
Data Lake Analytics U-SQL atividade executa um script U-SQL em um cluster Azure Data Lake Analytics. Consulte o artigo de [atividade u-SQL de Análise](transform-data-using-data-lake-analytics.md) de Dados para obter mais detalhes. 

### <a name="databricks-notebook-activity"></a>Atividade do Caderno databricks

A Atividade do Caderno Azure Databricks num pipeline data factory executa um caderno databricks no seu espaço de trabalho Azure Databricks. A Azure Databricks é uma plataforma gerida para executar a Apache Spark. Ver [Transformar dados executando um caderno databricks](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Atividade de Databricks Jar

O Azure Databricks Jar Activity num oleoduto data factory executa um Jar De faísca no seu cluster Azure Databricks. A Azure Databricks é uma plataforma gerida para executar a Apache Spark. Ver [Dados Transform executando uma atividade de Jar em Tijolos de Dados Azure](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Atividade de Databricks Python

O Azure Databricks Python Activity num pipeline data factory executa um ficheiro Python no seu cluster Azure Databricks. A Azure Databricks é uma plataforma gerida para executar a Apache Spark. Ver [Dados Transform executando uma atividade python em Tijolos de Dados Azure](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Atividade personalizada
Se precisar de transformar dados de uma forma que não seja suportada pela Data Factory, pode criar uma atividade personalizada com a sua própria lógica de processamento de dados e utilizar a atividade no pipeline. Pode configurar a atividade personalizada .NET para funcionar utilizando um serviço De Lote Azure ou um cluster Azure HDInsight. Consulte o artigo [de atividades personalizadas](transform-data-using-dotnet-custom-activity.md) para mais detalhes. 

Pode criar uma atividade personalizada para executar scripts R no seu cluster do HDInsight com R instalado. Veja [Run R Script using Azure Data Factory (Executar Script R com o Azure Data Factory)](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Ambientes computacionais
Cria-se um serviço ligado ao ambiente de computação e depois utiliza-se o serviço ligado na definição de uma atividade de transformação. Existem dois tipos de ambientes computacionais suportados pela Data Factory. 

- **On-Demand**: Neste caso, o ambiente de computação é totalmente gerido pela Data Factory. É automaticamente criado pelo serviço Data Factory antes de um trabalho ser submetido para processar dados e removido quando o trabalho está concluído. Pode configurar e controlar as configurações granulares do ambiente de computação a pedido para execução de emprego, gestão de clusters e ações de saque. 
- **Bring Your Own**: Neste caso, pode registar o seu próprio ambiente de computação (por exemplo, cluster HDInsight) como um serviço ligado na Data Factory. O ambiente de computação é gerido por si e o serviço Data Factory utiliza-o para executar as atividades. 

Consulte o artigo [da Compute Linked Services](compute-linked-services.md) para conhecer os serviços de computação suportados pela Data Factory. 

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte tutorial para um exemplo de utilização de uma atividade de transformação: [Tutorial: transforme dados usando a Spark](tutorial-transform-data-spark-powershell.md)
