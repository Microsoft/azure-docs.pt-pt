---
title: O que é o Apache Storm - Azure HDInsight
description: O Apache Storm permite-lhe processar fluxos de dados em tempo real. O Azure HDInsight permite-lhe criar facilmente clusters do Storm na cloud do Azure. Com o Visual Studio, pode criar soluções Storm com c# e implementar nos seus clusters do HDInsight Storm.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: apache storm use cases,storm cluster,what is apache storm
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: b9c66216411f42e46cc7c7be486f6103db9c7208
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894602"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>O que é o Apache Storm no Azure HDInsight?

O [Apache Storm](https://storm.apache.org/) é um sistema de computação distribuído com tolerância a falhas e de código aberto. Pode utilizar o Storm para processar fluxos de dados em tempo real com [Apache Hadoop](https://hadoop.apache.org/). As soluções do Storm também podem fornecer processamento garantido de dados, dada a capacidade de reprodução de dados que não foram processados com êxito à primeira vez.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="why-use-apache-storm-on-hdinsight"></a>Porquê utilizar o Apache Storm no HDInsight?

O Storm no HDInsight oferece as funcionalidades seguintes:

* __99% contrato de nível de serviço (SLA) no tempo de atividade do Storm__: Para obter mais informações, veja o documento [Informações do SLA para o HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Suporta a personalização fácil ao executar scripts face a um cluster do Storm durante ou após a criação. Para obter mais informações, veja [Customize HDInsight clusters using script action (Personalizar clusters do HDInsight com ações de script)](../hdinsight-hadoop-customize-cluster-linux.md).

* **Criar soluções em vários idiomas**: Pode escrever os componentes do Storm na linguagem que preferir, como Java, C# e Python.

    * Integra o Visual Studio no HDInsight para o desenvolvimento, a gestão e a monitorização das topologias C#. Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](apache-storm-develop-csharp-visual-studio-topology.md).

    * Suporta a interface de Java Trident. Pode criar topologias Storm que suportam o processamento de mensagens "exatamente uma vez", persistência do arquivo de dados transacional e um conjunto de operações de análise de fluxo comuns.

* **Dimensionamento dinâmico**: Pode adicionar ou remover nós de trabalho sem qualquer impacto nas topologias Storm em execução.

    > [!NOTE]  
    > Tem de desativar e reativar as topologias em execução para tirar partido de novos nós adicionados através de operações de dimensionamento.

* **Criar pipelines de transmissão em fluxo com vários serviços do Azure**: O Storm no HDInsight integra-se com outros serviços do Azure, como os Hubs de eventos, base de dados SQL, armazenamento do Azure e armazenamento do Azure Data Lake.

    Para uma solução de exemplo que se integra com serviços do Azure, consulte [processar eventos dos Hubs de eventos com o Apache Storm no HDInsight](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

Para obter uma lista de empresas que utiliza o Apache Storm como solução de análise em tempo real, veja [Companies using Apache Storm](https://storm.apache.org/documentation/Powered-By.html) (Empresas que Utilizam o Apache Storm).

Para começar a utilizar o Storm, veja [introdução ao Apache Storm no HDInsight][gettingstarted].

## <a name="how-does-apache-storm-work"></a>Como funciona o Apache Storm

O Storm executa topologias em vez do [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) tarefas que poderá estar familiarizado. As topologias do Storm são compostas por múltiplos componentes que são dispostos num grafo dirigido acíclico (DAG). Fluxos de dados entre os componentes no gráfico. Cada componente consome um ou mais fluxos de dados e podem, opcionalmente, emitir um ou mais fluxos. O diagrama seguinte ilustra a forma como os dados fluem entre componentes numa topologia de contagem de palavras básica:

![Exemplo da disposição dos componentes numa topologia Storm](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Os componentes spout trazem dados para uma topologia. Emitem um ou mais fluxos para a topologia.

* Os componentes bolt consomem fluxos emitidos por spouts ou outros bolts. Opcionalmente, os bolts podem emitir fluxos para a topologia. Os bolts também são responsáveis pela escrita de dados em serviços externos ou armazenamento, como o HDFS, Kafka ou HBase.

## <a name="reliability"></a>Fiabilidade

O Apache Storm garante que cada mensagem a receber é sempre totalmente processada, mesmo quando a análise de dados está distribuída por centenas de nós.

O nó Nimbus fornece uma funcionalidade semelhante ao JobTracker do Apache Hadoop e atribui tarefas a outros nós num cluster através do [Apache ZooKeeper](https://zookeeper.apache.org/). Os nós do Zookeeper asseguram a coordenação de um cluster e facilitam a comunicação entre o Nimbus e o processo do Supervisor nos nós de trabalho. Se um nó de processamento deixa de funcionar, o nó Nimbus é informado e atribui a tarefa e os dados associados a um outro nó.

A configuração predefinida para os clusters do Apache Storm é com apenas um nó Nimbus. O Storm no HDInsight fornece dois nós Nimbus. Se o nó principal falhar, o cluster do Storm muda para o nó secundário enquanto o nó principal se recupera. O seguinte diagrama ilustra a configuração do fluxo de tarefas para o Storm no HDInsight:

![Diagrama de nimbus, zookeeper e supervisor](./media/apache-storm-overview/nimbus.png)

## <a name="ease-of-creation"></a>Facilidade de criação

Pode criar um cluster novo do Storm no HDInsight em minutos. Para obter mais informações sobre como criar clusters do Storm, veja [Introdução ao Storm no HDInsight](apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Facilidade de utilização

* __Conectividade Secure Shell (SSH)__: Pode aceder a nós principais do cluster do Storm através da Internet ao utilizar SSH. Pode executar comandos diretamente no seu cluster através de SSH.

  Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* __Conectividade Web__: Todos os clusters do HDInsight fornecem a IU web do Ambari. Pode monitorizar, configurar e gerir serviços facilmente no seu cluster através da IU Web do Ambari. Os clusters do Storm também fornecem a IU do Storm. Pode monitorizar e gerir topologias Storm em execução a partir do seu browser na IU do Storm.

  Para obter mais informações, consulte a [gerir o HDInsight com a IU do Apache Ambari Web](../hdinsight-hadoop-manage-ambari.md) e [monitorizar e gerir com a interface do Usuário do Apache Storm](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui) documentos.

* __O Azure PowerShell e CLI do Azure clássico__: PowerShell e CLI clássica fornecem utilitários da linha de comandos que pode utilizar a partir do sistema cliente para trabalhar com o HDInsight e outros serviços do Azure.

* __Integração do Visual Studio__: O Azure Data Lake Tools para Visual Studio incluem modelos de projeto para criar C# topologias do Storm através da utilização da arquitetura SCP.NET. As Ferramentas do Data Lake também proporcionam ferramentas para implementar, monitorizar e gerir soluções com o Storm no HDInsight.

  Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integração noutros serviços do Azure

* __Armazenamento do Azure Data Lake__: Para obter um exemplo de como utilizar o armazenamento do Data Lake com um cluster do Storm, consulte [armazenamento do uso do Azure Data Lake com o Apache Storm no HDInsight](apache-storm-write-data-lake-store.md).

* __Os Hubs de eventos__: Para obter um exemplo de como utilizar os Hubs de eventos com um cluster do Storm, veja os exemplos seguintes:

    * [Processar eventos dos Hubs de eventos do Azure com o Apache Storm no HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/)

    * [Processar eventos dos Hubs de eventos do Azure com o Apache Storm no HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __Base de dados SQL__, __Cosmos DB__, __dos Hubs de eventos__, e __HBase__: Exemplos do modelos estão incluídos nas ferramentas do Data Lake para Visual Studio. Para obter mais informações, consulte [desenvolver um C# topologia para Apache Storm no HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

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
* Pesquisa
* Mobile Engagement

Para obter informações sobre cenários reais, veja a [como as empresas estão a utilizar o Apache Storm](https://storm.apache.org/documentation/Powered-By.html) documento.

## <a name="development"></a>Desenvolvimento

Os programadores em .NET podem conceber e implementar topologias em C# com as Ferramentas do Data Lake para Visual Studio. Também pode criar topologias híbridas que utilizam componentes de Java e C#.

Para obter mais informações, consulte o artigo [Desenvolver topologias C# para Apache Storm no HDInsight com Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Também pode desenvolver soluções Java com o IDE da sua preferência. Para obter mais informações, consulte [topologias de Java desenvolver para o Apache Storm no HDInsight](apache-storm-develop-java-topology.md).

Também pode ser utilizado Python para desenvolver componentes do Storm. Para obter mais informações, consulte [topologias de desenvolver o Apache Storm com Python no HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Padrões comuns de desenvolvimento

### <a name="guaranteed-message-processing"></a>Processamento de mensagens garantido

O Apache Storm pode fornecer diferentes níveis de processamento garantido de mensagens. Por exemplo, uma aplicação Storm básica pode garantir o processamento pelo-menos-uma vez, e [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) pode garantir o processamento exatamente uma vez.

Para obter mais informações, consulte o artigo [Garantias no processamento de dados](https://storm.apache.org/about/guarantees-data-processing.html) em apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

O padrão de ler uma tupla entrada, emitir zero ou mais cadeias de identificação e, em seguida, confirmar a tupla entrada imediatamente no final do método execute é comum. O Storm fornece a interface [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) para automatizar este padrão.

### <a name="joins"></a>Associações

O modo como os fluxos de dados são associados varia consoante as aplicações. Por exemplo, pode associar cada cadeia de identificação de vários fluxos a um novo fluxo ou pode associar apenas lotes de cadeias de identificação de uma janela específica. De qualquer forma, a associação pode ser efetuada através de [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). O agrupamento de campos é uma forma de definir como as tuplas são encaminhadas para bolts.

No exemplo de Java a seguir, utiliza-se fieldsGrouping para encaminhar cadeias de identificação que têm origem nos componentes "1", "2" e "3" para o bolt MyJoiner:

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>Lotes

O Apache Storm fornece um mecanismo de sincronização interno conhecido como "tupla de sinalização". Pode definir a frequência com que uma tupla de sinalização é emitida na sua topologia.

Para obter um exemplo de como utilizar uma cadeia de identificação de sinalização a partir de um componente em C#, veja [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Caches

A colocação em cache de memória utiliza-se frequentemente como um mecanismo para acelerar o processamento porque mantém os itens utilizados com maior frequência na memória. Como uma topologia é distribuída por múltiplos nós e processos em cada nó, deve considerar a utilização de [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Utilize `fieldsGrouping` para garantir que as cadeias de identificação que contêm os campos utilizados na pesquisa de cache são sempre encaminhados para o mesmo processo. Esta funcionalidade de agrupamento evita a duplicação de entradas em cache entre os processos.

### <a name="stream-top-n"></a>Transmitir o "valor N principal"

Quando a sua topologia depende do cálculo de um valor N principal, calcule-o em paralelo. Em seguida, intercale o resultado desses cálculos num valor global. Esta operação pode ser efetuada com [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) para encaminhar por campo para processamento paralelo. Em seguida, pode encaminhar um bolt que determina globalmente o valor N principal.

Para obter um exemplo de cálculo de um valor N principal, veja o exemplo [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Registo

Utiliza o Storm [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) para registar informações. Por predefinição, é registada uma grande quantidade de dados, podendo ser difícil organizar-se nas informações. Pode incluir um ficheiro de configuração de registo como parte da topologia do Storm para controlar o comportamento dos registos.

Para uma topologia de exemplo que demonstra como configurar o registo, veja o exemplo [WordCount baseado em Java](apache-storm-develop-java-topology.md) para Storm no HDInsight.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as soluções de análise em tempo real com Apache Storm no HDInsight:

* [Introdução ao Apache Storm no HDInsight][gettingstarted]
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: https://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: apache-storm-tutorial-get-started-linux.md
