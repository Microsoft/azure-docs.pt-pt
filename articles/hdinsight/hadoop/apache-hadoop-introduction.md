---
title: O que são a pilha de tecnologia do Apache Hadoop e do Apache Spark? -Azure HDInsight
description: Uma introdução ao HDInsight e à pilha de tecnologia do Apache Hadoop e Apache Spark e componentes, incluindo Kafka, Hive, Storm e HBase para análise de macrodados.
keywords: hadoop azure, azure hadoop, introdução ao hadoop, intro ao hadoop, pilha de tecnologia do hadoop, intro do hadoop, introdução do hadoop, o que é um cluster do hadoop, o que é o cluster do hadoop, para que é utilizado o hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: overview
ms.date: 01/28/2019
ms.openlocfilehash: ca5d0602948a0abe689578fad1508d8f3b3d5198
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443546"
---
# <a name="what-is-azure-hdinsight-and-the-apache-hadoop-technology-stack"></a>O que é o Azure HDInsight e a pilha de tecnologia do Apache Hadoop

Este artigo disponibiliza uma introdução ao Apache Hadoop no Azure HDInsight. O Azure HDInsight é um serviço de análise completamente gerido, de espectro completo e open-source para empresas. Pode usar as estruturas de open source, como Hadoop, Apache Spark, Apache Hive, LLAP, Apache Kafka, o Apache Storm, R e muito mais. 


## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>O que é o HDInsight e a pilha de tecnologia do Hadoop?

O [Apache Hadoop](https://hadoop.apache.org/) era a arquitetura de código aberto original para processamento distribuído e análise de conjuntos de macrodados em clusters. A pilha de tecnologia do Hadoop inclui software e utilitários, incluindo Apache Hive, Apache HBase, Spark, Kafka e muitos outros relacionados.


O Azure HDInsight é uma distribuição de cloud dos componentes do Hadoop da [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/). O Azure HDInsight torna mais fácil, rápido e rentável processar quantidades enormes de dados. Pode utilizar as estruturas de código aberto mais populares, como o Hadoop, o Spark, o Hive, o LLAP, o Kafka, o Storm, o R, entre outras. Com estas estruturas, pode permitir um vasto leque de cenários como extrair, transformar e carregar (ETL), armazenamento de dados, machine learning e IoT.

Para ver os componentes da pilha tecnológica do Hadoop disponíveis no HDInsight, veja [Componentes e versões disponíveis com o HDInsight][component-versioning]. Para ler mais sobre o Hadoop no HDInsight, veja a [página de funcionalidades do Azure para o HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-big-data"></a>O que são macrodados?

Os macrodados são recolhidos em volumes cada vez maiores, a velocidades mais rápidas e numa variedade de formatos mais ampla do que nunca. Podem ser históricos (isto é, armazenados) ou em tempo real (isto é, transmitidos em fluxo a partir da origem). Veja [Scenarios for using HDInsight (Cenários para utilizar o HDInsight)](#scenarios-for-using-hdinsight) para saber mais sobre os casos de utilização mais comuns de macrodados.

## <a name="why-should-i-use-hadoop-on-hdinsight"></a>Por que motivo devo utilizar o Hadoop no HDInsight?

Esta secção lista as capacidades do Azure HDInsight.


|Capacidade  |Descrição  |
|---------|---------|
|Cloud nativa     |     O Azure HDInsight permite-lhe criar clusters otimizados para o [Hadoop](apache-hadoop-linux-tutorial-get-started.md), o  [Spark](../spark/apache-spark-jupyter-spark-sql.md), a  [Consulta interativa (LLAP)](../interactive-query/apache-interactive-query-get-started.md), o  [Kafka](../kafka/apache-kafka-get-started.md), o  [Storm](../storm/apache-storm-tutorial-get-started-linux.md), o  [HBase](../hbase/apache-hbase-tutorial-get-started-linux.md) e os  [Serviços ML](../r-server/r-server-get-started.md) no Azure. O HDInsight também oferece um SLA de ponto a ponto em todas as suas cargas de trabalho de produção.  |
|Baixo custo e dimensionável     | O HDInsight permite-lhe [aumentar ou reduzir](../hdinsight-administer-use-portal-linux.md#scale-clusters) verticalmente cargas de trabalho. Pode  [criar clusters a pedido](../hdinsight-hadoop-create-linux-clusters-adf.md) e pagar apenas o que utiliza, para reduzir custos. Também pode criar pipelines de dados para operacionalizar as tarefas. A computação e o armazenamento desassociados oferecem um melhor desempenho e flexibilidade. |
|Seguro e conforme    | O HDInsight permite-lhe proteger os seus ativos de dados empresariais com a [Rede Virtual do Azure](../hdinsight-extend-hadoop-virtual-network.md), a [encriptação](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) e a integração no [Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md). O HDInsight também cumpre os [padrões de conformidade](https://azure.microsoft.com/overview/trusted-cloud) mais populares da indústria e do governo.        |
|Monitorização    | O Azure HDInsight integra-se com [registos do Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) para fornecer uma única interface com a qual pode monitorizar todos os clusters.        |
|Disponibilidade global | O HDInsight está disponível em mais de  [regiões](https://azure.microsoft.com/regions/services/) do que qualquer outra oferta de análise de macrodados. O Azure HDInsight também está disponível no Azure Government, na China e na Alemanha, o que lhe permite satisfazer as suas necessidades empresariais nas principais áreas soberanas. |  
|Produtividade     |  O Azure HDInsight permite-lhe utilizar ferramentas produtivas avançadas para o Hadoop e o Spark com os seus ambientes de desenvolvimento preferidos. Estes ambientes de desenvolvimento incluem suporte do [Visual Studio](apache-hadoop-visual-studio-tools-get-started.md), [VSCode](../hdinsight-for-vscode.md), [Eclipse](../spark/apache-spark-eclipse-tool-plugin.md) e [IntelliJ](../spark/apache-spark-intellij-tool-plugin.md) para Scala, Python, R, Java e .NET. Os cientistas de dados também podem colaborar com blocos de notas populares, como o [Jupyter](../spark/apache-spark-jupyter-notebook-kernels.md) e o [Zeppelin](../spark/apache-spark-zeppelin-notebook.md).    |
|Extensibilidade     |  Pode expandir os clusters do HDInsight com componentes instalados (Hue, Presto, etc.) através da utilização de [ações de script](../hdinsight-hadoop-customize-cluster-linux.md), ao [adicionar nós periféricos](../hdinsight-apps-use-edge-node.md) ou ao [integrar noutras aplicações certificadas de macrodados](../hdinsight-apps-install-applications.md). O HDInsight permite a integração perfeita com as soluções mais populares de macrodados com uma implementação de [um só clique](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/).|

## <a name="scenarios-for-using-hdinsight"></a>Cenários para utilizar o HDInsight

O Azure HDInsight pode servir para uma variedade de cenários de processamento de macrodados. Podem ser dados históricos (dados já recolhidos e armazenados) ou dados em tempo real (dados transmitidos diretamente da origem). Os cenários para processar esses dados podem ser resumidos nas seguintes categorias: 

### <a name="batch-processing-etl"></a>Processamento em lotes (ETL)

A extração, transformação e carregamento (ETL) é um processo onde os dados estruturados ou não estruturados são extraídos de origens de dados heterogéneas. Depois, são transformados num formato estruturado e carregados para um arquivo de dados. Pode utilizar os dados transformados para ciência de dados ou armazenamento de dados.

### <a name="data-warehousing"></a>Armazenamento de dados

Pode utilizar o HDInsight para executar consultas interativas em escalas de petabytes sobre dados estruturados ou não estruturados em qualquer formato. Também pode criar modelos ao ligá-las a ferramentas de BI. Para obter mais informações, [leia esta história do cliente](https://customers.microsoft.com/story/milliman). 

![Arquitetura do HDInsight: Armazenamento de dados](./media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png "arquitetura de armazenamento de dados de HDInsight")

### <a name="internet-of-things-iot"></a>Internet das Coisas (IoT)

Pode utilizar o HDInsight para processar os dados de transmissão em fluxo recebidos em tempo real de diversos dispositivos. Para obter mais informações, [leia esta mensagem do blogue do Azure que anuncia a pré-visualização pública do Apache Kafka no HDInsight com os discos geridos do Azure](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![Arquitetura do HDInsight: Internet das coisas](./media/apache-hadoop-introduction/hdinsight-architecture-iot.png "arquitetura do IoT do HDInsight") 

### <a name="data-science"></a>Ciência dos dados

Pode utilizar o HDInsight para criar aplicações que extraem informações críticas dos dados. Também pode utilizar o Azure Machine Learning para prever futuras tendências para a sua empresa. Para obter mais informações, [leia esta história do cliente](https://customers.microsoft.com/story/pros).

![Arquitetura do HDInsight: Ciência de dados](./media/apache-hadoop-introduction/hdinsight-architecture-data-science.png "arquitetura de ciência de dados do HDInsight")

### <a name="hybrid"></a>Híbrido

Pode utilizar o HDInsight para expandir a sua atual infraestrutura de macrodados local no Azure, para tirar partido de capacidades avançadas de análise da cloud.

![Arquitetura do HDInsight: Híbrido](./media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png "arquitetura do HDInsight híbrida")

## <a name="cluster-types-in-hdinsight"></a>Tipos de clusters no HDInsight

O HDInsight inclui tipos de clusters específicos e capacidades de personalização de clusters, como a possibilidade de adicionar componentes, utilitários e linguagens. O HDInsight oferece os seguintes tipos de cluster:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: Uma estrutura que utiliza HDFS, gestão de recursos do YARN e uma programação de MapReduce simple modelam para processar e analisar dados de lotes em paralelo.

* **[Apache Spark](https://spark.apache.org/)**: Uma arquitetura de código-fonte aberto, o processamento paralelo que suporta o processamento dentro da memória para melhorar o desempenho de aplicações de análise de macrodados. Veja [O que é o Apache Spark no HDInsight?](../spark/apache-spark-overview.md)

* **[Apache HBase](https://hbase.apache.org/)**: Uma base de dados NoSQL incorporada no Hadoop que fornece acesso aleatório e consistência segura para grandes quantidades de dados não estruturados e semiestruturados, potencialmente, biliões de linhas vezes milhões de colunas. Veja [O que é o HBase no HDInsight?](../hbase/apache-hbase-overview.md)

* **[Serviços de ML](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server)**: Um servidor para alojar e gerir processos R distribuídos paralelos. Fornece cientistas de dados, peritos em estatística e programadores de R com acesso a pedido a métodos dimensionáveis distribuídos de análise no HDInsight. Veja [Descrição geral dos Serviços ML no HDInsight](../r-server/r-server-overview.md).

* **[O Apache Storm](https://storm.incubator.apache.org/)**: Um sistema de cálculo distribuído e em tempo real para processamento rápido de grandes fluxos de dados. O Storm é fornecido como um cluster gerido no HDInsight. Consulte [Analisar dados de sensores em tempo real através do Storm e do Hadoop](../storm/apache-storm-sensor-data-analysis.md).

* **[Pré-visualização do Apache Interactive Query (também conhecido como: Live há muito tempo e processar)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: Cache na memória para consultas do Hive interativas e mais rápidas. Veja [Use Interactive Query in HDInsight (Utilizar o Interactive Query no HDInsight)](../interactive-query/apache-interactive-query-get-started.md).

* **[Apache Kafka](https://kafka.apache.org/)**: Uma plataforma de código-fonte aberto que é utilizada para criar aplicações e pipelines de dados de transmissão em fluxo. O Kafka também fornece uma funcionalidade de fila de mensagens que lhe permite publicar e subscrever transmissões de dados. Consulte [Introduction to Apache Kafka on HDInsight (Introdução ao Apache Kafka no HDInsight)](../kafka/apache-kafka-introduction.md).

## <a name="open-source-components-in-hdinsight"></a>Componentes de código aberto no HDInsight


O Azure HDInsight permite-lhe criar clusters com estruturas de open source, como Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase e R. Estes clusters, por predefinição, são fornecidos com outros componentes de código-fonte aberto que estão incluídos no cluster [Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](https://avro.apache.org/docs/current/spec.html), [Apache Hive](https://hive.apache.org), [ HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Apache Mahout](https://mahout.apache.org/), [Apache Hadoop MapReduce](https://wiki.apache.org/hadoop/MapReduce), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Phoenix](https://phoenix.apache.org/), [Apache Pig](https://pig.apache.org/), [Apache Sqoop](https://sqoop.apache.org/), [Apache Tez](https://tez.apache.org/), [Apache Oozie](https://oozie.apache.org/), [Apache ZooKeeper](https://zookeeper.apache.org/).  



## <a name="programming-languages-in-hdinsight"></a>Linguagens de programação no HDInsight
Os clusters do HDInsight, incluindo Spark, HBase, Kafka, Hadoop, entre outros, suportam muitas linguagens de programação. Contudo, algumas não estão instaladas por predefinição. Para bibliotecas, módulos ou pacotes não instalados por predefinição, [utilize uma ação de script para instalar o componente](../hdinsight-hadoop-script-actions-linux.md).


|Linguagem de programação  |Informações  |
|---------|---------|
|Suporte de linguagens de programação predefinidas     | Por predefinição, os clusters do HDInsight suportam:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Ir</li></ul>  |
|Linguagens de máquina virtual de Java (JVM)     | Podem ser executadas muitas outras linguagens que não Java nas máquinas virtuais Java (JVM). No entanto, se executar algumas destas linguagens, poderá ter de instalar componentes adicionais no cluster. As linguagens baseadas em JVM abaixo são suportadas nos clusters do HDInsight: <ul><li>Clojure</li><li>Jython (Python para Java)</li><li>Scala</li></ul>     |
|Linguagens específicas do Hadoop     | Os clusters do HDInsight suportam as seguintes linguagens que são específicas da pilha de tecnologia do Hadoop: <ul><li>Pig Latin para tarefas do Pig</li><li>HiveQL para tarefas do Hive e SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>Ferramentas de desenvolvimento para HDInsight

Pode utilizar as ferramentas de desenvolvimento HDInsight, incluindo IntelliJ, Eclipse, Visual Studio Code e Visual Studio, para criar e submeter tarefas e consultas de dados HDInsight com integração simplificada no Azure.

* [Azure Toolkit para IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin)

* [Azure Toolkit para Eclipse](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-eclipse-tool-plugin)

* [Ferramentas do Azure HDInsight para VS Code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode?branch=pr-en-us-22999)

* [Ferramentas do Azure Data Lake para Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started)

## <a name="business-intelligence-on-hdinsight"></a>Business intelligence no HDInsight

As ferramentas de business intelligence (BI) familiares obtêm, analisam e reportam dados integrados no HDInsight através do suplemento Power Query ou Microsoft Hive ODBC Driver:

* [Apache Spark BI a utilizar ferramentas de visualização de dados com o Azure HDInsight](../spark/apache-spark-use-bi-tools.md)

* [Visualize os dados do Apache Hive com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md) 

* [Visualizar dados de Consulta Interativa do Hive com o Power BI no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)


* [Ligar o Excel para Apache Hadoop com o Power Query](apache-hadoop-connect-excel-power-query.md) (requer o Windows) 


* [Ligar o Excel para Apache Hadoop com o controlador Microsoft Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md) (requer o Windows) 

* [Utilizar o SQL Server Analysis Services com o HDInsight](https://docs.microsoft.com/previous-versions/msp-n-p/dn749857(v=pandp.10))

* [Utilizar o SQL Server Reporting Services com o HDInsight](https://docs.microsoft.com/previous-versions/msp-n-p/dn749856(v=pandp.10))

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu o que é o Azure HDInsight e como fornece o Hadoop e outros tipos de cluster no Azure. Avance para o artigo seguinte para saber como criar um cluster do Apache Hadoop no HDInsight.

> [!div class="nextstepaction"]
> [Criar cluster do Apache Hadoop no HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)


[component-versioning]: ../hdinsight-component-versioning.md
[zookeeper]: https://zookeeper.apache.org/
