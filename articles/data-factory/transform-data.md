---
title: Transformar dados
description: Transforme dados ou processos na Azure Data Factory usando Hadoop, Azure Machine Learning Studio (clássico) ou Azure Data Lake Analytics.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 0a1eb593e9f9f15f88aefb2fe06706153a4b74a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361405"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformar dados no Azure Data Factory

> [!div class="op_single_selector"]
> * [Fluxo de dados de mapeamento](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Azure Machine Learning Studio (clássico)](transform-data-using-machine-learning.md) 
> * [Procedimento Armazenado](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Caderno databricks](transform-data-databricks-notebook.md)
> * [Frasco de dados](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET personalizado](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Descrição geral
Este artigo explica as atividades de transformação de dados na Azure Data Factory que pode utilizar para transformar e processar os seus dados brutos em previsões e insights em escala. Uma atividade de transformação executa em um ambiente de computação como Azure Databricks ou Azure HDInsight. Fornece ligações a artigos com informações detalhadas sobre cada atividade de transformação.

A Data Factory suporta as seguintes atividades de transformação de dados que podem ser adicionadas aos [oleodutos](concepts-pipelines-activities.md) individualmente ou acorrentados com outra atividade.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Transforme-se na Fábrica de Dados Azure com fluxos de dados

### <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento

Os fluxos de dados de mapeamento são transformações de dados visualmente projetadas na Azure Data Factory. Os fluxos de dados permitem que os engenheiros de dados desenvolvam lógicas de transformação de dados gráficos sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro dos oleodutos da Azure Data Factory que utilizam clusters de faíscas em escala. As atividades de fluxo de dados podem ser operacionalizadas através do agendamento, controlo, fluxo e capacidades de monitorização existentes da Data Factory. Para obter mais informações, consulte [os fluxos de dados de mapeamento.](concepts-data-flow-overview.md)

### <a name="data-wrangling"></a>Conflito de dados

A consulta de energia na Azure Data Factory permite a criação de dados em escala de nuvem, o que permite fazer a preparação de dados sem código em iterativas em escala de nuvem. A disputa de dados integra-se com [a Power Query Online](/power-query/) e disponibiliza funções de Power Query M para a luta de dados à escala de nuvem através da execução de faíscas. Para obter mais informações, consulte [os dados em que estão a ser discutidos na ADF.](wrangling-overview.md)

## <a name="external-transformations"></a>Transformações externas

Opcionalmente, você pode fazer transformações de código manual e gerir o ambiente computacional externo.

### <a name="hdinsight-hive-activity"></a>Atividade da Colmeia HDInsight
A atividade de Hive HDInsight num oleoduto da Data Factory executa consultas de Hive no seu próprio ou a pedido do grupo HDInsight baseado em Windows/Linux. Consulte o artigo [de atividade da Hive](transform-data-using-hadoop-hive.md) para obter mais detalhes sobre esta atividade. 

### <a name="hdinsight-pig-activity"></a>Atividade do Porco HDInsight
A atividade do Porco HDInsight num oleoduto da Data Factory executa consultas de porco no seu próprio conjunto de Windows/HdInsight baseado em Windows/Linux. Consulte o artigo [de atividade do Porco](transform-data-using-hadoop-pig.md) para mais detalhes sobre esta atividade. 

### <a name="hdinsight-mapreduce-activity"></a>HdInsight MapReduce atividade
A atividade HDInsight MapReduce num oleoduto data factory executa programas MapReduce por conta própria ou a pedido do grupo HDInsight baseado em Windows/Linux. Consulte o artigo [de atividade mapReduce](transform-data-using-hadoop-map-reduce.md) para mais detalhes sobre esta atividade.

### <a name="hdinsight-streaming-activity"></a>Atividade de streaming HDInsight
A atividade de streaming HDInsight num oleoduto da Data Factory executa programas de streaming Hadoop no seu próprio ou a pedido do grupo HDInsight baseado em Windows/Linux. Consulte [a atividade de Streaming HDInsight](transform-data-using-hadoop-streaming.md) para obter detalhes sobre esta atividade.

### <a name="hdinsight-spark-activity"></a>Atividade de HdInsight Spark
A atividade HDInsight Spark num oleoduto data factory executa programas Spark no seu próprio cluster HDInsight. Para mais detalhes, consulte [os programas Invoke Spark da Azure Data Factory](transform-data-using-spark.md). 

### <a name="azure-machine-learning-studio-classic-activities"></a>Atividades do Azure Machine Learning Studio (clássico)
A Azure Data Factory permite-lhe criar facilmente oleodutos que utilizem um serviço web publicado do Azure Machine Learning Studio (clássico) para análise preditiva. Utilizando a [atividade de execução de lotes](transform-data-using-machine-learning.md) num oleoduto Azure Data Factory, pode invocar um serviço web Studio (clássico) para fazer previsões sobre os dados em lote.

Com o tempo, os modelos preditivos no Estúdio (clássico) experiências de pontuação precisam de ser retreinados usando novos conjuntos de dados de entrada. Depois de terminar a reconversão, pretende atualizar o serviço web de pontuação com o modelo de aprendizagem automática retreinado. Pode utilizar a [atividade de Update Resource](update-machine-learning-models.md) para atualizar o serviço web com o modelo recém-treinado.  

Consulte [as atividades do Use Azure Machine Learning Studio (clássico)](transform-data-using-machine-learning.md) para mais detalhes sobre estas atividades do Studio (clássico). 

### <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Pode utilizar a atividade do Procedimento Armazenado do SQL server num oleoduto da Data Factory para invocar um procedimento armazenado numa das seguintes lojas de dados: Azure SQL Database, Azure Synapse Analytics, SQL Server Database na sua empresa ou um Azure VM. Consulte o artigo [de atividade do Procedimento Armazenado](transform-data-using-stored-procedure.md) para obter mais informações.  

### <a name="data-lake-analytics-u-sql-activity"></a>Atividade U-SQL do Data Lake Analytics
Data Lake Analytics U-SQL atividade executa um script U-SQL em um cluster Azure Data Lake Analytics. Consulte o artigo [de atividade u-SQL da Data Analytics](transform-data-using-data-lake-analytics.md) para obter mais detalhes. 

### <a name="databricks-notebook-activity"></a>Atividade do caderno databricks

A Azure Databricks Notebook Activity in a Data Factory pipeline gere um caderno Databricks no seu espaço de trabalho Azure Databricks. Azure Databricks é uma plataforma gerida para executar Apache Spark. Consulte [os dados da Transforme-os executando um caderno Databricks](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Atividade do Jar databricks

A atividade do frasco de dados Azure num oleoduto da Data Factory executa um jarro de faísca no seu cluster Azure Databricks. Azure Databricks é uma plataforma gerida para executar Apache Spark. Consulte [os dados da Transform executando uma atividade do Jar em Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Atividade de Databricks Python

A atividade python da Azure Databricks num oleoduto da Data Factory executa um ficheiro Python no seu cluster Azure Databricks. Azure Databricks é uma plataforma gerida para executar Apache Spark. Consulte [os dados da Transform executando uma atividade Python em Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Atividade personalizada
Se precisar de transformar dados de uma forma que não seja suportada pela Data Factory, pode criar uma atividade personalizada com a sua própria lógica de processamento de dados e utilizar a atividade no pipeline. Pode configurar a atividade personalizada .NET para executar utilizando um serviço Azure Batch ou um cluster Azure HDInsight. Consulte [o artigo de atividades personalizadas](transform-data-using-dotnet-custom-activity.md) para obter mais detalhes. 

Pode criar uma atividade personalizada para executar scripts R no seu cluster do HDInsight com R instalado. Veja [Run R Script using Azure Data Factory (Executar Script R com o Azure Data Factory)](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Ambientes computacional
Cria um serviço ligado para o ambiente computacional e, em seguida, utiliza o serviço ligado ao definir uma atividade de transformação. Existem dois tipos de ambientes computativos suportados pela Data Factory. 

- **A pedido**: Neste caso, o ambiente de computação é totalmente gerido pela Data Factory. É automaticamente criado pelo serviço Data Factory antes de um trabalho ser submetido para processar dados e removido quando o trabalho estiver concluído. Você pode configurar e controlar configurações granulares do ambiente de computação a pedido para execução de emprego, gestão de clusters e ações de bootstrapping. 
- **Bring Your Own**: Neste caso, pode registar o seu próprio ambiente de computação (por exemplo, cluster HDInsight) como um serviço ligado na Data Factory. O ambiente de computação é gerido por si e o serviço Data Factory utiliza-o para executar as atividades. 

Consulte o artigo [da Compute Linked Services](compute-linked-services.md) para saber mais sobre os serviços de computação suportados pela Data Factory. 

## <a name="next-steps"></a>Passos seguintes
Veja o seguinte tutorial para um exemplo de utilização de uma atividade de transformação: [Tutorial: transformar dados usando Faísca](tutorial-transform-data-spark-powershell.md)
