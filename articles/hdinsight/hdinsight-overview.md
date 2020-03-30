---
title: O que é Azure HDInsight
description: Uma introdução ao HDInsight, e à pilha e componentes da tecnologia Apache Hadoop e Apache Spark, incluindo Kafka, Hive, Storm e HBase para análise de big data.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 31e8506067133304144614ff58974ee21e9680be
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241221"
---
# <a name="what-is-azure-hdinsight"></a>O que é o Azure HDInsight?

O Azure HDInsight é um serviço de análise gerido, de todo o espectro e de código aberto na nuvem para as empresas. Você pode usar quadros de código aberto como Hadoop, Apache Spark, Apache Hive, LLAP, Apache Kafka, Apache Storm, R, e muito mais.

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>O que é o HDInsight e a pilha de tecnologia do Hadoop?

Azure HDInsight é uma distribuição em nuvem de componentes Hadoop. O Azure HDInsight torna mais fácil, rápido e rentável processar quantidades enormes de dados. Pode utilizar as estruturas de código aberto mais populares, como o Hadoop, o Spark, o Hive, o LLAP, o Kafka, o Storm, o R, entre outras. Com estas estruturas, pode permitir um vasto leque de cenários como extrair, transformar e carregar (ETL), armazenamento de dados, machine learning e IoT.

Para ver os componentes da pilha tecnológica do Hadoop disponíveis no HDInsight, veja [Componentes e versões disponíveis com o HDInsight](./hdinsight-component-versioning.md). Para ler mais sobre o Hadoop no HDInsight, veja a [página de funcionalidades do Azure para o HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-big-data"></a>O que são macrodados?

Os macrodados são recolhidos em volumes cada vez maiores, a velocidades mais rápidas e numa variedade de formatos mais ampla do que nunca. Podem ser históricos (isto é, armazenados) ou em tempo real (isto é, transmitidos em fluxo a partir da origem). Veja [Scenarios for using HDInsight (Cenários para utilizar o HDInsight)](#scenarios-for-using-hdinsight) para saber mais sobre os casos de utilização mais comuns de macrodados.

## <a name="why-should-i-use-azure-hdinsight"></a>Por que devo usar Azure HDInsight?

Esta secção lista as capacidades do Azure HDInsight.

|Capacidade  |Descrição  |
|---------|---------|
|Cloud nativa     |     O Azure HDInsight permite-lhe criar clusters otimizados para o [Hadoop](./hadoop/apache-hadoop-linux-tutorial-get-started.md), o  [Spark](./spark/apache-spark-jupyter-spark-sql.md), a  [Consulta interativa (LLAP)](./interactive-query/apache-interactive-query-get-started.md), o  [Kafka](./kafka/apache-kafka-get-started.md), o  [Storm](./storm/apache-storm-tutorial-get-started-linux.md), o  [HBase](./hbase/apache-hbase-tutorial-get-started-linux.md) e os  [Serviços ML](./r-server/r-server-overview.md) no Azure. O HDInsight também oferece um SLA de ponto a ponto em todas as suas cargas de trabalho de produção.  |
|Baixo custo e dimensionável     | O HDInsight permite-lhe [escalar](./hdinsight-administer-use-portal-linux.md#scale-clusters) as cargas de trabalho para cima ou para baixo.Pode reduzir os [custos criando clusters a pedido](./hdinsight-hadoop-create-linux-clusters-adf.md) e pagando apenas pelo que utiliza. Também pode criar pipelines de dados para operacionalizar as tarefas. A computação e o armazenamento desassociados oferecem um melhor desempenho e flexibilidade. |
|Seguro e conforme    | O HDInsight permite-lhe proteger os seus ativos de dados empresariais com a [Rede Virtual do Azure](./hdinsight-plan-virtual-network-deployment.md), a [encriptação](./hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) e a integração no [Azure Active Directory](./domain-joined/hdinsight-security-overview.md). O HDInsight também cumpre os [padrões de conformidade](https://azure.microsoft.com/overview/trusted-cloud) mais populares da indústria e do governo.        |
|Monitorização    | O Azure HDInsight integra-se com [os registos do Monitor Azure](./hdinsight-hadoop-oms-log-analytics-tutorial.md) para fornecer uma única interface com a qual pode monitorizar todos os seus clusters.        |
|Disponibilidade global | O HDInsight está disponível em mais [regiões](https://azure.microsoft.com/regions/services/) do que qualquer outra oferta de análise de big data. O Azure HDInsight também está disponível no Azure Government, na China e na Alemanha, o que lhe permite satisfazer as suas necessidades empresariais nas principais áreas soberanas. |  
|Produtividade     |  O Azure HDInsight permite-lhe utilizar ferramentas produtivas avançadas para o Hadoop e o Spark com os seus ambientes de desenvolvimento preferidos. Estes ambientes de desenvolvimento incluem suporte do [Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md), [VSCode](./hdinsight-for-vscode.md), [Eclipse](./spark/apache-spark-eclipse-tool-plugin.md) e [IntelliJ](./spark/apache-spark-intellij-tool-plugin.md) para Scala, Python, R, Java e .NET. Os cientistas de dados também podem colaborar com blocos de notas populares, como o [Jupyter](./spark/apache-spark-jupyter-notebook-kernels.md) e o [Zeppelin](./spark/apache-spark-zeppelin-notebook.md).    |
|Extensibilidade     |  Pode expandir os clusters do HDInsight com componentes instalados (Hue, Presto, etc.) através da utilização de [ações de script](./hdinsight-hadoop-customize-cluster-linux.md), ao [adicionar nós periféricos](./hdinsight-apps-use-edge-node.md) ou ao [integrar noutras aplicações certificadas de macrodados](./hdinsight-apps-install-applications.md). O HDInsight permite a integração perfeita com as soluções mais populares de macrodados com uma implementação de [um só clique](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/).|

## <a name="scenarios-for-using-hdinsight"></a>Cenários para utilizar o HDInsight

O Azure HDInsight pode servir para uma variedade de cenários de processamento de macrodados. Podem ser dados históricos (dados já recolhidos e armazenados) ou dados em tempo real (dados transmitidos diretamente da origem). Os cenários para processar esses dados podem ser resumidos nas seguintes categorias:

### <a name="batch-processing-etl"></a>Processamento em lotes (ETL)

A extração, transformação e carregamento (ETL) é um processo onde os dados estruturados ou não estruturados são extraídos de origens de dados heterogéneas. Depois, são transformados num formato estruturado e carregados para um arquivo de dados. Pode utilizar os dados transformados para ciência de dados ou armazenamento de dados.

### <a name="data-warehousing"></a>Armazenamento de dados

Pode utilizar o HDInsight para executar consultas interativas em escalas de petabytes sobre dados estruturados ou não estruturados em qualquer formato. Também pode criar modelos ao ligá-las a ferramentas de BI. Para obter mais informações, [leia esta história do cliente](https://customers.microsoft.com/story/milliman).

![Arquitetura do HDInsight: armazenamento de dados](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png "HDInsight Data armazenagem arquitetura")

### <a name="internet-of-things-iot"></a>Internet das Coisas (IoT)

Pode utilizar o HDInsight para processar dados de streaming que são recebidos em tempo real a partir de diferentes tipos de dispositivos. Para obter mais informações, [leia esta mensagem do blogue do Azure que anuncia a pré-visualização pública do Apache Kafka no HDInsight com os discos geridos do Azure](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![Arquitetura do HDInsight: Internet das Coisas](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-iot.png "Arquitetura HDInsight IoT")

### <a name="data-science"></a>Ciência dos dados

Pode utilizar o HDInsight para criar aplicações que extraem informações críticas dos dados. Também pode utilizar o Azure Machine Learning para prever futuras tendências para a sua empresa. Para obter mais informações, [leia esta história do cliente](https://customers.microsoft.com/story/pros).

![Arquitetura do HDInsight: ciência de dados](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-science.png "Arquitetura de ciência de dados HDInsight")

### <a name="hybrid"></a>Híbrido

Pode utilizar o HDInsight para expandir a sua atual infraestrutura de macrodados local no Azure, para tirar partido de capacidades avançadas de análise da cloud.

![Arquitetura do HDInsight: híbrido](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png "Arquitetura híbrida HDInsight")

## <a name="cluster-types-in-hdinsight"></a>Tipos de clusters no HDInsight

O HDInsight inclui tipos de clusters específicos e capacidades de personalização de clusters, como a possibilidade de adicionar componentes, utilitários e linguagens. O HDInsight oferece os seguintes tipos de cluster:

|Tipo de Cluster | Descrição |
|---|---|
|[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)|Uma estrutura que utiliza hdfs, gestão de recursos YARN e um modelo de programação simples MapReduce para processar e analisar dados de lote em paralelo.|
|[Apache Spark](./spark/apache-spark-overview.md)|Um quadro de processamento paralelo de código aberto que suporta o processamento na memória para impulsionar o desempenho de aplicações de análise de big data. Veja [O que é o Apache Spark no HDInsight?](./spark/apache-spark-overview.md)|
|[Apache HBase](./hbase/apache-hbase-overview.md)|Uma base de dados NoSQL construída sobre Hadoop que fornece acesso aleatório e forte consistência para grandes quantidades de dados não estruturados e semi-estruturados - potencialmente biliões de linhas vezes milhões de colunas. Veja [O que é o HBase no HDInsight?](./hbase/apache-hbase-overview.md)|
|[Serviços ML](./r-server/r-server-overview.md)|Um servidor para hospedar e gerir processos R paralelos e distribuídos. Fornece cientistas de dados, peritos em estatística e programadores de R com acesso a pedido a métodos dimensionáveis distribuídos de análise no HDInsight. Veja [Descrição geral dos Serviços ML no HDInsight](./r-server/r-server-overview.md).|
|[Apache Storm](./storm/apache-storm-overview.md)|Um sistema de computação distribuído e em tempo real para o processamento rápido de grandes fluxos de dados. O Storm é fornecido como um cluster gerido no HDInsight. Consulte [Analisar dados de sensores em tempo real através do Storm e do Hadoop](./storm/apache-storm-overview.md).|
|[Consulta Interativa Apache](./interactive-query/apache-interactive-query-get-started.md)|Na memória, caching para consultas interativas e mais rápidas da Colmeia. Veja [Use Interactive Query in HDInsight (Utilizar o Interactive Query no HDInsight)](./interactive-query/apache-interactive-query-get-started.md).|
|[Apache Kafka](./kafka/apache-kafka-introduction.md)|Uma plataforma de código aberto que é usada para construir pipelines e aplicações de dados de streaming. O Kafka também fornece uma funcionalidade de fila de mensagens que lhe permite publicar e subscrever transmissões de dados. Consulte [Introduction to Apache Kafka on HDInsight (Introdução ao Apache Kafka no HDInsight)](./kafka/apache-kafka-introduction.md).|

## <a name="open-source-components-in-hdinsight"></a>Componentes de código aberto no HDInsight

O Azure HDInsight permite-lhe criar clusters com estruturas de código aberto como Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase e R. Estes clusters, por padrão, vêm com outros componentes de código aberto que estão incluídos no cluster como [Apache Ambari,](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) [Avro,](https://avro.apache.org/docs/current/spec.html) [Apache Hive,](https://hive.apache.org) [HCatalog,](https://cwiki.apache.org/confluence/display/Hive/HCatalog/) [Apache Mahout,](https://mahout.apache.org/) [Apache Hadoop MapReduce,](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) [Apache Hadoop YARN,](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) [Apache Phoenix,](https://phoenix.apache.org/) [Apache Pig,](https://pig.apache.org/) [Apache Sqoop,](https://sqoop.apache.org/) [Apache Tez, Apache](https://tez.apache.org/) [Oozie,](https://oozie.apache.org/) [Apache ZooKeeper.](https://zookeeper.apache.org/)  

## <a name="programming-languages-in-hdinsight"></a>Linguagens de programação no HDInsight

Os clusters do HDInsight, incluindo Spark, HBase, Kafka, Hadoop, entre outros, suportam muitas linguagens de programação. Contudo, algumas não estão instaladas por predefinição. Para bibliotecas, módulos ou pacotes que não sejam instalados por padrão, utilize uma ação de [script para instalar o componente](./hdinsight-hadoop-script-actions-linux.md).

|Linguagem de programação  |Informações  |
|---------|---------|
|Suporte de linguagens de programação predefinidas     | Por predefinição, os clusters do HDInsight suportam:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Ir</li></ul>  |
|Linguagens de máquina virtual de Java (JVM)     | Podem ser executadas muitas outras linguagens que não Java nas máquinas virtuais Java (JVM). No entanto, se executar algumas destas linguagens, poderá ter de instalar componentes adicionais no cluster. As linguagens baseadas em JVM abaixo são suportadas nos clusters do HDInsight: <ul><li>Clojure</li><li>Jython (Python para Java)</li><li>Scala</li></ul>     |
|Linguagens específicas do Hadoop     | Os clusters do HDInsight suportam as seguintes linguagens que são específicas da pilha de tecnologia do Hadoop: <ul><li>Pig Latin para tarefas do Pig</li><li>HiveQL para tarefas do Hive e SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>Ferramentas de desenvolvimento para HDInsight

Pode utilizar as ferramentas de desenvolvimento HDInsight, incluindo IntelliJ, Eclipse, Visual Studio Code e Visual Studio, para criar e submeter tarefas e consultas de dados HDInsight com integração simplificada no Azure.

* [Kit de ferramentas Azure para IntelliJ](./spark/apache-spark-intellij-tool-plugin.md)

* [Kit de ferramentas Azure para Eclipse](./spark/apache-spark-eclipse-tool-plugin.md)

* [Ferramentas do Azure HDInsight para VS Code](./hdinsight-for-vscode.md)

* [Ferramentas do Azure Data Lake para Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="business-intelligence-on-hdinsight"></a>Business intelligence no HDInsight

As ferramentas de business intelligence (BI) familiares obtêm, analisam e reportam dados integrados no HDInsight através do suplemento Power Query ou Microsoft Hive ODBC Driver:

* [Apache Spark BI a utilizar ferramentas de visualização de dados com o Azure HDInsight](./spark/apache-spark-use-bi-tools.md)

* [Visualizar os dados da Hive Apache com o Microsoft Power BI no Azure HDInsight](./hadoop/apache-hadoop-connect-hive-power-bi.md)

* [Visualizar dados de Consulta Interativa do Hive com o Power BI no Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Ligue excel ao Hadoop Apache com consulta](./hadoop/apache-hadoop-connect-excel-power-query.md) de energia (requer Windows)

* [Ligue excel ao Apache Hadoop com o Controlador Microsoft Hive ODBC](./hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (requer Windows)

## <a name="next-steps"></a>Passos seguintes

* [Criar o cluster Apache Hadoop no HDInsight](./hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
