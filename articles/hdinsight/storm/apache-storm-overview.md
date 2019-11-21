---
title: O que é o Apache Storm - Azure HDInsight
description: O Apache Storm permite-lhe processar fluxos de dados em tempo real. O Azure HDInsight permite-lhe criar facilmente clusters do Storm na cloud do Azure. Com o Visual Studio, pode criar soluções Storm com c# e implementar nos seus clusters do HDInsight Storm.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: apache storm use cases,storm cluster,what is apache storm
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: overview
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 9c7e49fe522859f97f00f760822d5eef60db5f69
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228863"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>O que é o Apache Storm no Azure HDInsight?

O [Apache Storm](https://storm.apache.org/) é um sistema de computação distribuído com tolerância a falhas e de código aberto. You can use Storm to process streams of data in real time with [Apache Hadoop](https://hadoop.apache.org/). As soluções do Storm também podem fornecer processamento garantido de dados, dada a capacidade de reprodução de dados que não foram processados com êxito à primeira vez.

## <a name="why-use-apache-storm-on-hdinsight"></a>Why use Apache Storm on HDInsight?

O Storm no HDInsight oferece as funcionalidades seguintes:

* __99% de tempo de atividade do Contrato de Nível de Serviço (SLA) no Storm__: para obter mais informações, veja o documento [SLA information for HDInsight (Informações do SLA para o HDInsight)](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Suporta a personalização fácil ao executar scripts face a um cluster do Storm durante ou após a criação. Para obter mais informações, veja [Customize HDInsight clusters using script action (Personalizar clusters do HDInsight com ações de script)](../hdinsight-hadoop-customize-cluster-linux.md).

* **Crie soluções em várias linguagens**: pode escrever os componentes do Storm na linguagem que preferir, como Java, C# e Python.

    * Integra o Visual Studio no HDInsight para o desenvolvimento, a gestão e a monitorização das topologias C#. Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](apache-storm-develop-csharp-visual-studio-topology.md).

    * Suporta a interface de Java Trident. Pode criar topologias Storm que suportam o processamento de mensagens "exatamente uma vez", persistência do arquivo de dados transacional e um conjunto de operações de análise de fluxo comuns.

* **Dimensionamento dinâmico**: pode adicionar ou remover nós de trabalho sem qualquer impacto nas topologias Storm em execução.

    * Tem de desativar e reativar as topologias em execução para tirar partido de novos nós adicionados através de operações de dimensionamento.

* **Create streaming pipelines using multiple Azure services**: Storm on HDInsight integrates with other Azure services such as Event Hubs, SQL Database, Azure Storage, and Azure Data Lake Storage.

    For an example solution that integrates with Azure services, see [Process events from Event Hubs with Apache Storm on HDInsight](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

Para obter uma lista de empresas que utiliza o Apache Storm como solução de análise em tempo real, veja [Companies using Apache Storm](https://storm.apache.org/documentation/Powered-By.html) (Empresas que Utilizam o Apache Storm).

To get started using Storm, see [Create and monitor an Apache Storm topology in Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>How does Apache Storm work

Storm runs topologies instead of the [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)  jobs that you might be familiar with. As topologias do Storm são compostas por múltiplos componentes que são dispostos num grafo dirigido acíclico (DAG). Fluxos de dados entre os componentes no gráfico. Cada componente consome um ou mais fluxos de dados e podem, opcionalmente, emitir um ou mais fluxos. O diagrama seguinte ilustra a forma como os dados fluem entre componentes numa topologia de contagem de palavras básica:

![Exemplo da disposição dos componentes numa topologia Storm](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Os componentes spout trazem dados para uma topologia. Emitem um ou mais fluxos para a topologia.

* Os componentes bolt consomem fluxos emitidos por spouts ou outros bolts. Opcionalmente, os bolts podem emitir fluxos para a topologia. Os bolts também são responsáveis pela escrita de dados em serviços externos ou armazenamento, como o HDFS, Kafka ou HBase.

## <a name="reliability"></a>Fiabilidade

O Apache Storm garante que cada mensagem a receber é sempre totalmente processada, mesmo quando a análise de dados está distribuída por centenas de nós.

The Nimbus node provides functionality similar to the Apache Hadoop JobTracker, and it assigns tasks to other nodes in a cluster through [Apache ZooKeeper](https://zookeeper.apache.org/). Os nós do Zookeeper asseguram a coordenação de um cluster e facilitam a comunicação entre o Nimbus e o processo do Supervisor nos nós de trabalho. Se um nó de processamento deixa de funcionar, o nó Nimbus é informado e atribui a tarefa e os dados associados a um outro nó.

A configuração predefinida para os clusters do Apache Storm é com apenas um nó Nimbus. O Storm no HDInsight fornece dois nós Nimbus. Se o nó principal falhar, o cluster do Storm muda para o nó secundário enquanto o nó principal se recupera. O seguinte diagrama ilustra a configuração do fluxo de tarefas para o Storm no HDInsight:

![Diagrama de nimbus, zookeeper e supervisor](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-creation"></a>Facilidade de criação

Pode criar um cluster novo do Storm no HDInsight em minutos. For more information on creating a Storm cluster, see [Create Apache Hadoop clusters using the Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="ease-of-use"></a>Facilidade de utilização

* __Conectividade Secure Shell (SSH)__ : pode aceder ao nós principais do cluster do Storm através da Internet ao utilizar SSH. Pode executar comandos diretamente no seu cluster através de SSH.

  Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* __Conectividade Web__: todos os clusters do HDInsight fornecem a IU da Web do Ambari. Pode monitorizar, configurar e gerir serviços facilmente no seu cluster através da IU Web do Ambari. Os clusters do Storm também fornecem a IU do Storm. Pode monitorizar e gerir topologias Storm em execução a partir do seu browser na IU do Storm.

  For more information, see the [Manage HDInsight using the Apache Ambari Web UI](../hdinsight-hadoop-manage-ambari.md) and [Monitor and manage using the Apache Storm UI](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui) documents.

* __Azure PowerShell and Azure Classic CLI__: PowerShell and classic CLI both provide command-line utilities that you can use from your client system to work with HDInsight and other Azure services.

* __Visual Studio integration__: Azure Data Lake Tools for Visual Studio include project templates for creating C# Storm topologies by using the SCP.NET framework. As Ferramentas do Data Lake também proporcionam ferramentas para implementar, monitorizar e gerir soluções com o Storm no HDInsight.

  Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integração noutros serviços do Azure

* __Azure Data Lake Storage__: For an example of using Data Lake Storage with a Storm cluster, see [Use Azure Data Lake Storage with Apache Storm on HDInsight](apache-storm-write-data-lake-store.md).

* __Hubs de Eventos__: para obter um exemplo de como utilizar os Hubs de Eventos com um cluster do Storm, veja os exemplos seguintes:

    * [Process events from Azure Event Hubs with Apache Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Process events from Azure Event Hubs with Apache Storm on HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __Base de Dados SQL__, __Cosmos DB__, __Hubs de Eventos__ e __HBase__: exemplos do modelos estão incluídos nas Ferramentas do Data Lake para Visual Studio. For more information, see [Develop a C# topology for Apache Storm on HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="support"></a>Suporte

O Storm no HDInsight inclui suporte contínuo e completo de nível empresarial. O Storm no HDInsight tem também um SLA de 99,9 por cento. Isto significa que a Microsoft garante que os clusters do Storm têm conectividade externa em, pelo menos, 99,9 por cento das vezes.

Para obter mais informações, veja o [suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="apache-storm-use-cases"></a>Casos de utilização do Apache Storm

Seguem-se alguns cenários comuns nos quais poderá utilizar o Storm no HDInsight:

* Internet das Coisas (IoT)
* Deteção de fraudes
* Análise social
* Extração, transformação e carregamento (ETL)
* Monitorização de rede
* Procurar
* Mobile Engagement

For information about real-world scenarios, see the [How companies are using Apache Storm](https://storm.apache.org/Powered-By.html) document.

## <a name="development"></a>Desenvolvimento

Os programadores em .NET podem conceber e implementar topologias em C# com as Ferramentas do Data Lake para Visual Studio. Também pode criar topologias híbridas que utilizam componentes de Java e C#.

Para obter mais informações, consulte o artigo [Desenvolver topologias C# para Apache Storm no HDInsight com Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Também pode desenvolver soluções Java com o IDE da sua preferência. For more information, see [Develop Java topologies for Apache Storm on HDInsight](apache-storm-develop-java-topology.md).

Também pode ser utilizado Python para desenvolver componentes do Storm. For more information, see [Develop Apache Storm topologies using Python on HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Padrões comuns de desenvolvimento

### <a name="guaranteed-message-processing"></a>Processamento de mensagens garantido

O Apache Storm pode fornecer diferentes níveis de processamento garantido de mensagens. For example, a basic Storm application can guarantee at-least-once processing, and [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) can guarantee exactly once processing.

Para obter mais informações, consulte o artigo [Garantias no processamento de dados](https://storm.apache.org/about/guarantees-data-processing.html) em apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

The pattern of reading an input tuple, emitting zero or more tuples, and then acknowledging the input tuple immediately at the end of the execute method is common. O Storm fornece a interface [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) para automatizar este padrão.

### <a name="joins"></a>Associações

O modo como os fluxos de dados são associados varia consoante as aplicações. Por exemplo, pode associar cada cadeia de identificação de vários fluxos a um novo fluxo ou pode associar apenas lotes de cadeias de identificação de uma janela específica. De qualquer forma, a associação pode ser efetuada através de [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). O agrupamento de campos é uma forma de definir como as tuplas são encaminhadas para bolts.

No exemplo de Java a seguir, utiliza-se fieldsGrouping para encaminhar cadeias de identificação que têm origem nos componentes "1", "2" e "3" para o bolt MyJoiner:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Lotes

O Apache Storm fornece um mecanismo de sincronização interno conhecido como "tupla de sinalização". Pode definir a frequência com que uma tupla de sinalização é emitida na sua topologia.

Para obter um exemplo de como utilizar uma cadeia de identificação de sinalização a partir de um componente em C#, veja [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Caches

A colocação em cache de memória utiliza-se frequentemente como um mecanismo para acelerar o processamento porque mantém os itens utilizados com maior frequência na memória. Como uma topologia é distribuída por múltiplos nós e processos em cada nó, deve considerar a utilização de [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Utilize `fieldsGrouping` para garantir que as cadeias de identificação que contêm os campos utilizados na pesquisa de cache são sempre encaminhados para o mesmo processo. Esta funcionalidade de agrupamento evita a duplicação de entradas em cache entre os processos.

### <a name="stream-top-n"></a>Transmitir o "valor N principal"

Quando a sua topologia depende do cálculo de um valor N principal, calcule-o em paralelo. Em seguida, intercale o resultado desses cálculos num valor global. Esta operação pode ser efetuada com [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) para encaminhar por campo para processamento paralelo. Em seguida, pode encaminhar um bolt que determina globalmente o valor N principal.

Para obter um exemplo de cálculo de um valor N principal, veja o exemplo [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Registo

Storm uses [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) to log information. Por predefinição, é registada uma grande quantidade de dados, podendo ser difícil organizar-se nas informações. Pode incluir um ficheiro de configuração de registo como parte da topologia do Storm para controlar o comportamento dos registos.

Para uma topologia de exemplo que demonstra como configurar o registo, veja o exemplo [WordCount baseado em Java](apache-storm-develop-java-topology.md) para Storm no HDInsight.

## <a name="next-steps"></a>Passos seguintes

Learn more about real-time analytics solutions with Apache Storm on HDInsight:

* [Create and monitor an Apache Storm topology in Azure HDInsight](apache-storm-quickstart.md)
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)
