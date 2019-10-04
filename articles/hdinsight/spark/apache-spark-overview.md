---
title: O que é Apache Spark-Azure HDInsight
description: Este artigo disponibiliza uma introdução ao Spark no HDInsight e os diferentes cenários em que pode utilizar o cluster do Spark no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 10/01/2019
ms.openlocfilehash: 923e5a961ee9b7bdea94cf7a3e6fc833f596fafd
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937611"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>O que é o Apache Spark no Azure HDInsight

Apache Spark é uma estrutura de processamento paralelo que dá suporte ao processamento na memória para melhorar o desempenho de aplicativos analíticos de Big Data. O Apache Spark no Azure HDInsight é a implementação da Microsoft do Apache Spark na cloud. O HDInsight permite criar e configurar clusters do Spark mais facilmente na cloud. Os clusters do Spark no HDInsight são compatíveis com o armazenamento do Azure e Azure Data Lake Storage. Por isso, pode utilizá-los para processar os dados armazenados no Azure. Para obter os componentes e as informações de controle de versão, consulte [Apache Hadoop componentes e versões no Azure HDInsight](../hdinsight-component-versioning.md).

![Spark: uma arquitetura unificada](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>O que é o Apache Spark?

O Spark fornece primitivos para a computação de cluster na memória. Os trabalhos do Spark podem carregar e colocar os dados em cache na memória e consultá-los repetidamente. A computação na memória é muito mais rápida do que aplicativos baseados em disco, como o Hadoop, que compartilha dados por meio do HDFS (sistema de arquivos distribuído Hadoop). O Spark também se integra na linguagem de programação Scala, o que lhe possibilita manipular conjuntos de dados distribuídos, como coleções locais. Não é necessário estruturar tudo como operações de mapa e redução.

![MapReduce tradicional vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark1.png)

Os clusters do Spark no HDInsight oferecem um serviço Spark completamente gerido. Os benefícios da criação de um cluster do Spark no HDInsight estão listados aqui.

| Funcionalidade | Descrição |
| --- | --- |
| Criação fácil |Pode criar um cluster do Spark novo no HDInsight em apenas alguns minutos com o portal do Azure, o Azure PowerShell ou o SDK .NET do HDInsight. Consulte Introdução [ao cluster apache Spark no HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Facilidade de utilização |O cluster Spark no HDInsight inclui notebooks Jupyter e Apache Zeppelin. Pode utilizar estes blocos de notas para o processamento e a visualização de dados interativos.|
| APIs REST |Os clusters do Spark no HDInsight incluem o [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), um servidor de trabalho do Spark baseado na API REST para enviar e monitorar trabalhos remotamente. Consulte [usar Apache Spark API REST para enviar trabalhos remotos para um cluster HDInsight Spark](apache-spark-livy-rest-interface.md).|
| Suporte para Azure Data Lake Storage | Os clusters do Spark no HDInsight podem usar Azure Data Lake Storage como armazenamento primário ou armazenamento adicional. Para obter mais informações sobre Data Lake Storage, consulte [visão geral do Azure data Lake Storage](../../data-lake-store/data-lake-store-overview.md). |
| Integração com os serviços do Azure |O cluster do Spark no HDInsight é fornecido com um conector para os Hubs de Eventos do Azure. Você pode criar aplicativos de streaming usando os hubs de eventos, além de [Apache Kafka](https://kafka.apache.org/), que já está disponível como parte do Spark. |
| Suporte do ML Server | O suporte do ML Server no HDInsight é prestado como o tipo de cluster dos **Serviços ML**. Pode configurar um cluster dos Serviços ML para executar cálculos R distribuídos com as velocidades prometidas com um cluster do Spark. Para obter mais informações, consulte [o que são os serviços do ml no Azure HDInsight](../r-server/r-server-overview.md). |
| Integração com IDEs de terceiros | O HDInsight proporciona vários plug-ins de IDE que são úteis para criar e submeter aplicações para um cluster do Spark no HDInsight. Para obter mais informações, consulte [usar Azure Toolkit for INTELLIJ ideia](apache-spark-intellij-tool-plugin.md), [usar as ferramentas do Spark & Hive para VSCode](../hdinsight-for-vscode.md)e [usar Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Consultas em Simultâneo |Os clusters do Spark no HDInsight suportam consultas em simultâneo. Esta capacidade permite que várias consultas de um utilizador ou várias consultas de vários utilizadores e aplicações partilhem os mesmos recursos de cluster. |
| Colocação em cache em SSDs |Pode optar por colocar os dados em cache na memória ou em SSDs ligados aos nós do cluster. A colocação em cache na memória oferece o melhor desempenho às consultas, mas pode ser dispendiosa. A colocação em cache em SSDs é uma excelente opção para melhorar o desempenho das consultas sem que seja necessário criar um cluster com tamanho suficiente para guardar todo o conjunto de dados na memória. |
| Integração com Ferramentas de BI |Os clusters do Spark no HDInsight fornecem conectores para ferramentas de BI, como o [Power BI](https://www.powerbi.com/), para análise de dados. |
| Bibliotecas Anaconda pré-carregadas |Os clusters do Spark no HDInsight incluem bibliotecas Anaconda pré-instaladas. O [Anaconda](https://docs.continuum.io/anaconda/) fornece bibliotecas próximas de 200 para aprendizado de máquina, análise de dados, visualização e assim por diante. |
| Escalabilidade | O HDInsight permite que você altere o número de nós de cluster. Além disso, os clusters Spark podem ser descartados sem perda de dados, já que todos os dados são armazenados no armazenamento do Azure ou Data Lake Storage. |
| SLA |Os clusters do Spark no HDInsight incluem suporte 24 horas por dia, 7 dias por semana e um SLA de 99,9% de tempo ativo. |

Os clusters Apache Spark no HDInsight incluem os seguintes componentes que estão disponíveis nos clusters por padrão.

* [Spark Core](https://spark.apache.org/docs/latest/). Inclui o Spark Core, o Spark SQL, APIs de transmissão em fluxo do Spark, o GraphX e o MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Bloco de notas do Jupyter](https://jupyter.org)
* [Notebook Apache Zeppelin](http://zeppelin-project.org/)

Os clusters do Spark no HDInsight também fornecem um [controlador ODBC](https://go.microsoft.com/fwlink/?LinkId=616229) para a conectividade com clusters do Spark no HDInsight a partir de ferramentas de BI, como o Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Arquitetura de cluster do Spark

![A arquitetura do Spark no HDInsight](./media/apache-spark-overview/hdi-spark-architecture.png)

É fácil entender os componentes do Spark, compreendendo como o Spark é executado em clusters HDInsight.

As aplicações do Spark são executadas como conjuntos independentes de processos num cluster, coordenados pelo objeto SparkContext no seu programa principal (denominado “programa de controladores”).

O SparkContext pode ligar-se a vários tipos de gestores de clusters, que alocam recursos transversalmente às aplicações. Esses gerenciadores de cluster incluem o [Apache mesos](https://mesos.apache.org/), o [Apache Hadoop yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)ou o Gerenciador de cluster do Spark. No HDInsight, o Spark é executado com o gestor de clusters YARN. Assim que estiver ligado, o Spark adquire executores nos nós de trabalhado do cluster, que são os processos que executam cálculos e armazenam os dados da sua aplicação. Em seguida, envia o código da aplicação (definido pelos ficheiros JAR ou Python transmitidos para o SparkContext) aos executores. Por fim, o SparkContext envia tarefas para os executores executarem.

O SparkContext executa a função principal do utilizador, bem como as diversas operações paralelas nos nós de trabalho. Em seguida, recolhe os resultados das operações. Os nós de trabalho lêem e gravam dados de e para o sistema de arquivos distribuído Hadoop. Também colocam em cache os dados transformados na memória como Conjuntos de Dados Distribuídos Resilientes (RDDs).

O SparkContext liga-se ao mestre do Spark e é responsável pela conversão das aplicações em gráficos dirigidos (DAG) de tarefas individuais que são executadas num processo do executor nos nós de trabalho. Cada aplicação tem os seus próprios processos de executor, que permanecem em funcionamento ao longo da duração de toda a aplicação e executam tarefas em múltiplos threads.

## <a name="spark-in-hdinsight-use-cases"></a>Casos de utilização do Spark no HDInsight

Os clusters do Spark no HDInsight permitem os cenários-chave seguintes:

* Análise de dados interativa e BI

    Apache Spark no HDInsight armazena dados no armazenamento do Azure ou Azure Data Lake Storage. Os especialistas em negócio e os decisores-chave podem analisar e criar relatórios com base nesses dados e utilizar o Microsoft Power BI para criar relatórios interativos a partir dos dados analisados. Os analistas podem partir de dados não estruturados/semiestruturados no armazenamento de clusters, definir um esquema para os dados com os blocos de notas e, em seguida, criar modelos de dados através do Microsoft Power BI. Os clusters do Spark no HDInsight também suportam várias ferramentas de BI de terceiros, como o Tableau, o que os torna ideais para analistas de dados, especialistas em negócios e decisores-chave.

    [Tutorial: Visualizar dados do Spark usando Power BI](apache-spark-use-bi-tools.md)

* Spark Machine Learning

    O Apache Spark inclui o [MLlib](https://spark.apache.org/mllib/), uma biblioteca de aprendizagem automática baseada no Spark, que pode utilizar a partir de um cluster do Spark no HDInsight. O cluster Spark no HDInsight também inclui o Anaconda, uma distribuição do Python com diferentes tipos de pacotes para aprendizado de máquina. Associe a isto o suporte incorporado para blocos de notas do Jupyter e do Zeppelin e obtém um ambiente para a criação de aplicações de machine learning.

    [Tutorial: Prever temperaturas de construção usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)  
    [Tutorial: Prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)

* Análise de dados de transmissão em fluxo e em tempo real do Spark

    Os clusters do Spark no HDInsight oferecem um suporte avançado para a criação de soluções de análise em tempo real. Embora o Spark já tenha conectores para ingerir dados de várias fontes, como o Kafka, o Flume, o Twitter, o ZeroMQ ou os sockets de TCP, o Spark no HDInsight acrescenta um suporte de primeira classe para a ingestão de dados a partir de Event Hubs do Azure. Os Hubs de Eventos são o serviço de colocação em fila mais utilizado no Azure. O facto de ter um suporte imediato para os Hubs de Eventos faz com que os clusters do Spark no HDInsight sejam uma plataforma ideal para a criação de pipelines de análise em tempo real.

## <a name="where-do-i-start"></a>Por onde devo começar?

Você pode usar os seguintes artigos para saber mais sobre Apache Spark no HDInsight:

* [Quickstart: Criar um cluster Apache Spark no HDInsight e executar a consulta interativa usando o Jupyter](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Tutorial: Executar um trabalho de Apache Spark usando o Jupyter](./apache-spark-load-data-run-query.md)
* [Tutorial: Analisar dados usando ferramentas de BI](./apache-spark-use-bi-tools.md)
* [Tutorial: Machine Learning usando Apache Spark](./apache-spark-ipython-notebook-machine-learning.md)
* [Tutorial: Criar um aplicativo de escala de aplicativos do Maven usando IntelliJ](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Próximos Passos

Nesta descrição geral, obteve algumas noções básicas do Apache Spark no Azure HDInsight. Avance para o próximo artigo para saber como utilizar um cluster do Spark no HDInsight e executar algumas consultas do Spark SQL:

* [Criar um cluster Apache Spark no HDInsight](./apache-spark-jupyter-spark-sql-use-portal.md)
