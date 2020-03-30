---
title: O que é Apache Spark - Azure HDInsight
description: Este artigo disponibiliza uma introdução ao Spark no HDInsight e os diferentes cenários em que pode utilizar o cluster do Spark no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 02/25/2020
ms.openlocfilehash: 83bfeb85d110f7a84720e943e28f5e014e4c3888
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605042"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>O que é o Apache Spark no Azure HDInsight

Apache Spark é um quadro de processamento paralelo que suporta o processamento na memória para aumentar o desempenho de aplicações analíticas de big data. O Apache Spark no Azure HDInsight é a implementação da Microsoft do Apache Spark na cloud. O HDInsight permite criar e configurar clusters do Spark mais facilmente na cloud. Os clusters de faíscas no HDInsight são compatíveis com o Armazenamento de Dados Azure e o Armazenamento de Lagos De Dados Azure. Por isso, pode utilizá-los para processar os dados armazenados no Azure. Para os componentes e informações de versão, consulte [componentes e versões Apache Hadoop no Azure HDInsight](../hdinsight-component-versioning.md).

![Spark: uma arquitetura unificada](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>O que é o Apache Spark?

O Spark fornece primitivos para a computação de cluster na memória. Os trabalhos do Spark podem carregar e colocar os dados em cache na memória e consultá-los repetidamente. A computação na memória é muito mais rápida do que aplicações baseadas em discos, como hadoop, que partilha dados através do sistema de ficheiros distribuídos hadoop (HDFS). O Spark também se integra na linguagem de programação Scala, o que lhe possibilita manipular conjuntos de dados distribuídos, como coleções locais. Não é necessário estruturar tudo como operações de mapa e redução.

![Mapa tradicionalReduzir vs. Faísca](./media/apache-spark-overview/map-reduce-vs-spark1.png)

Os clusters do Spark no HDInsight oferecem um serviço Spark completamente gerido. Os benefícios da criação de um cluster do Spark no HDInsight estão listados aqui.

| Funcionalidade | Descrição |
| --- | --- |
| Criação fácil |Pode criar um cluster do Spark novo no HDInsight em apenas alguns minutos com o portal do Azure, o Azure PowerShell ou o SDK .NET do HDInsight. Ver [Começar com o cluster Apache Spark em HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Facilidade de utilização |O cluster de faíscas no HDInsight inclui os cadernos Jupyter e Apache Zeppelin. Pode utilizar estes blocos de notas para o processamento e a visualização de dados interativos. Consulte [os cadernos Apache Zeppelin com](apache-spark-zeppelin-notebook.md) dados de Apache Spark e Load e [execute consultas num cluster Apache Spark](apache-spark-load-data-run-query.md).|
| APIs REST |Os clusters de faíscas no HDInsight incluem [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), um servidor de trabalho spark baseado em REST API para submeter e monitorizar remotamente trabalhos. Consulte [API De repouso Apache Spark PARA submeter trabalhos remotos a um cluster hDInsight Spark](apache-spark-livy-rest-interface.md).|
| Suporte para armazenamento de lagos de dados azure | Os clusters de faíscas no HDInsight podem usar o Armazenamento do Lago De Dados Azure como armazenamento primário ou armazenamento adicional. Para mais informações sobre o Armazenamento de Data Lake, consulte [a visão geral do armazenamento do Lago De Dados Azure](../../data-lake-store/data-lake-store-overview.md). |
| Integração com os serviços do Azure |O cluster do Spark no HDInsight é fornecido com um conector para os Hubs de Eventos do Azure. Pode construir aplicações de streaming utilizando os Hubs de Eventos, além de [Apache Kafka,](https://kafka.apache.org/)que já está disponível como parte da Spark. |
| Suporte do ML Server | O suporte do ML Server no HDInsight é prestado como o tipo de cluster dos **Serviços ML**. Pode configurar um cluster dos Serviços ML para executar cálculos R distribuídos com as velocidades prometidas com um cluster do Spark. Para mais informações, consulte [o que é ml Services no Azure HDInsight](../r-server/r-server-overview.md). |
| Integração com IDEs de terceiros | O HDInsight proporciona vários plug-ins de IDE que são úteis para criar e submeter aplicações para um cluster do Spark no HDInsight. Para mais informações, consulte [Use Azure Toolkit para IntelliJ IDEA](apache-spark-intellij-tool-plugin.md), [Use Spark & Hive Tools para VSCode](../hdinsight-for-vscode.md), e use o kit de [ferramentas Azure para Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Consultas em Simultâneo |Os clusters do Spark no HDInsight suportam consultas em simultâneo. Esta capacidade permite que várias consultas de um utilizador ou várias consultas de vários utilizadores e aplicações partilhem os mesmos recursos de cluster. |
| Colocação em cache em SSDs |Pode optar por colocar os dados em cache na memória ou em SSDs ligados aos nós do cluster. A colocação em cache na memória oferece o melhor desempenho às consultas, mas pode ser dispendiosa. A colocação em cache em SSDs é uma excelente opção para melhorar o desempenho das consultas sem que seja necessário criar um cluster com tamanho suficiente para guardar todo o conjunto de dados na memória. Ver [Melhorar o desempenho das cargas de trabalho da Apache Spark utilizando o Azure HDInsight IO Cache](apache-spark-improve-performance-iocache.md). |
| Integração com Ferramentas de BI |Os clusters do Spark no HDInsight fornecem conectores para ferramentas de BI, como o [Power BI](https://www.powerbi.com/), para análise de dados. |
| Bibliotecas Anaconda pré-carregadas |Os clusters do Spark no HDInsight incluem bibliotecas Anaconda pré-instaladas. [A Anaconda](https://docs.continuum.io/anaconda/) fornece cerca de 200 bibliotecas para machine learning, análise de dados, visualização, e assim por diante. |
| Escalabilidade | O HDInsight permite alterar o número de nós de cluster de forma dinâmica com a função De escala Automática. Consulte automaticamente a escala dos [clusters Azure HDInsight](../hdinsight-autoscale-clusters.md). Além disso, os clusters Spark podem ser eliminados sem perda de dados, uma vez que todos os dados são armazenados em Armazenamento azure ou armazenamento de data lake. |
| SLA |Os clusters do Spark no HDInsight incluem suporte 24 horas por dia, 7 dias por semana e um SLA de 99,9% de tempo ativo. |

Os clusters Apache Spark no HDInsight incluem os seguintes componentes disponíveis nos clusters por padrão.

* [Spark Core](https://spark.apache.org/docs/latest/). Inclui o Spark Core, o Spark SQL, APIs de transmissão em fluxo do Spark, o GraphX e o MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Bloco de notas do Jupyter](https://jupyter.org)
* [Caderno Apache Zeppelin](http://zeppelin-project.org/)

Os clusters do Spark no HDInsight também fornecem um [controlador ODBC](https://go.microsoft.com/fwlink/?LinkId=616229) para a conectividade com clusters do Spark no HDInsight a partir de ferramentas de BI, como o Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Arquitetura de cluster do Spark

![A arquitetura do Spark no HDInsight](./media/apache-spark-overview/hdi-spark-architecture.png)

É fácil entender os componentes da Spark compreendendo como a Spark funciona em clusters HDInsight.

As aplicações do Spark são executadas como conjuntos independentes de processos num cluster, coordenados pelo objeto SparkContext no seu programa principal (denominado “programa de controladores”).

O SparkContext pode ligar-se a vários tipos de gestores de clusters, que alocam recursos transversalmente às aplicações. Estes gestores de cluster incluem [Apache Mesos,](https://mesos.apache.org/) [Apache Hadoop YARN,](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)ou o gestor de cluster Spark. No HDInsight, o Spark é executado com o gestor de clusters YARN. Assim que estiver ligado, o Spark adquire executores nos nós de trabalhado do cluster, que são os processos que executam cálculos e armazenam os dados da sua aplicação. Em seguida, envia o código da aplicação (definido pelos ficheiros JAR ou Python transmitidos para o SparkContext) aos executores. Por fim, o SparkContext envia tarefas para os executores executarem.

O SparkContext executa a função principal do utilizador, bem como as diversas operações paralelas nos nós de trabalho. Em seguida, recolhe os resultados das operações. Os nódosos dos trabalhadores lêem e escrevem dados de e para o sistema de ficheiros distribuídos hadoop. Também colocam em cache os dados transformados na memória como Conjuntos de Dados Distribuídos Resilientes (RDDs).

O SparkContext liga-se ao mestre do Spark e é responsável pela conversão das aplicações em gráficos dirigidos (DAG) de tarefas individuais que são executadas num processo do executor nos nós de trabalho. Cada aplicação tem os seus próprios processos de executor, que permanecem em funcionamento ao longo da duração de toda a aplicação e executam tarefas em múltiplos threads.

## <a name="spark-in-hdinsight-use-cases"></a>Casos de utilização do Spark no HDInsight

Os clusters do Spark no HDInsight permitem os cenários-chave seguintes:

### <a name="interactive-data-analysis-and-bi"></a>Análise de dados interativa e BI

A Apache Spark em HDInsight armazena dados no Armazenamento Azure ou no Armazenamento do Lago Azure Data. Os especialistas em negócio e os decisores-chave podem analisar e criar relatórios com base nesses dados e utilizar o Microsoft Power BI para criar relatórios interativos a partir dos dados analisados. Os analistas podem partir de dados não estruturados/semiestruturados no armazenamento de clusters, definir um esquema para os dados com os blocos de notas e, em seguida, criar modelos de dados através do Microsoft Power BI. Os clusters do Spark no HDInsight também suportam várias ferramentas de BI de terceiros, como o Tableau, o que os torna ideais para analistas de dados, especialistas em negócios e decisores-chave.

* [Tutorial: Visualize Spark data using Power BI](apache-spark-use-bi-tools.md) (Tutorial: Utilizar o Power BI para ver dados do Spark)

### <a name="spark-machine-learning"></a>Spark Machine Learning

O Apache Spark inclui o [MLlib](https://spark.apache.org/mllib/), uma biblioteca de aprendizagem automática baseada no Spark, que pode utilizar a partir de um cluster do Spark no HDInsight. O cluster de faíscas no HDInsight também inclui a Anaconda, uma distribuição Python com diferentes tipos de pacotes para aprendizagem automática. Associe a isto o suporte incorporado para blocos de notas do Jupyter e do Zeppelin e obtém um ambiente para a criação de aplicações de machine learning.

* [Tutorial: Prever temperaturas de construção usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)  
* [Tutorial: Prever os resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Análise de dados de transmissão em fluxo e em tempo real do Spark

Os clusters do Spark no HDInsight oferecem um suporte avançado para a criação de soluções de análise em tempo real. Embora o Spark já tenha conectores para ingerir dados de várias fontes, como o Kafka, o Flume, o Twitter, o ZeroMQ ou os sockets de TCP, o Spark no HDInsight acrescenta um suporte de primeira classe para a ingestão de dados a partir de Event Hubs do Azure. Os Hubs de Eventos são o serviço de colocação em fila mais utilizado no Azure. O facto de ter um suporte imediato para os Hubs de Eventos faz com que os clusters do Spark no HDInsight sejam uma plataforma ideal para a criação de pipelines de análise em tempo real.

* [Visão geral do Apache Spark Streaming](apache-spark-streaming-overview.md)
* [Visão geral do streaming estruturado de faíscas Apache](apache-spark-structured-streaming-overview.md)

## <a name="where-do-i-start"></a>Por onde devo começar?

Pode utilizar os seguintes artigos para saber mais sobre apache Spark no HDInsight:

* [Quickstart: Crie um cluster Apache Spark em HDInsight e execute consulta interativa usando Jupyter](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Tutorial: Executar um trabalho apache spark usando Jupyter](./apache-spark-load-data-run-query.md)
* [Tutorial: Analisar dados usando ferramentas BI](./apache-spark-use-bi-tools.md)
* [Tutorial: Machine learning usando Apache Spark](./apache-spark-ipython-notebook-machine-learning.md)
* [Tutorial: Criar uma aplicação Scala Maven usando o IntelliJ](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Passos Seguintes

Nesta descrição geral, obteve algumas noções básicas do Apache Spark no Azure HDInsight. Avance para o próximo artigo para saber como utilizar um cluster do Spark no HDInsight e executar algumas consultas do Spark SQL:

* [Criar um cluster Apache Spark no HDInsight](./apache-spark-jupyter-spark-sql-use-portal.md)
