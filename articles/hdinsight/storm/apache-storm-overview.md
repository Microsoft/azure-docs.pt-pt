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
ms.openlocfilehash: 0540d8a83a8dc1da9bc763bce0b30463889cfe90
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915043"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>O que é o Apache Storm no Azure HDInsight?

O [Apache Storm](https://storm.apache.org/) é um sistema de computação distribuído com tolerância a falhas e de código aberto. Você pode usar o Storm para processar fluxos de dados em tempo real com [Apache Hadoop](https://hadoop.apache.org/). As soluções do Storm também podem fornecer processamento garantido de dados, dada a capacidade de reprodução de dados que não foram processados com êxito à primeira vez.

## <a name="why-use-apache-storm-on-hdinsight"></a>Por que usar o Apache Storm no HDInsight?

O Storm no HDInsight oferece as funcionalidades seguintes:

* __99% contrato de nível de serviço (SLA) no tempo de atividade do Storm__: Para obter mais informações, veja o documento [Informações do SLA para o HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Suporta a personalização fácil ao executar scripts face a um cluster do Storm durante ou após a criação. Para obter mais informações, veja [Customize HDInsight clusters using script action (Personalizar clusters do HDInsight com ações de script)](../hdinsight-hadoop-customize-cluster-linux.md).

* **Crie soluções em vários idiomas**: Pode escrever os componentes do Storm na linguagem que preferir, como Java, C# e Python.

    * Integra o Visual Studio no HDInsight para o desenvolvimento, a gestão e a monitorização das topologias C#. Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](apache-storm-develop-csharp-visual-studio-topology.md).

    * Suporta a interface de Java Trident. Pode criar topologias Storm que suportam o processamento de mensagens "exatamente uma vez", persistência do arquivo de dados transacional e um conjunto de operações de análise de fluxo comuns.

* **Dimensionamento dinâmico**: Pode adicionar ou remover nós de trabalho sem qualquer impacto nas topologias Storm em execução.

    * Tem de desativar e reativar as topologias em execução para tirar partido de novos nós adicionados através de operações de dimensionamento.

* **Crie pipelines de streaming usando vários serviços do Azure**: O Storm no HDInsight integra-se a outros serviços do Azure, como hubs de eventos, banco de dados SQL, armazenamento do Azure e Azure Data Lake Storage.

    Para obter um exemplo de solução que se integra aos serviços do Azure, consulte [processar eventos dos hubs de eventos com Apache Storm no HDInsight](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

Para obter uma lista de empresas que utiliza o Apache Storm como solução de análise em tempo real, veja [Companies using Apache Storm](https://storm.apache.org/documentation/Powered-By.html) (Empresas que Utilizam o Apache Storm).

Para começar a usar o Storm, consulte [criar e monitorar uma topologia de Apache Storm no Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>Como funciona Apache Storm

O Storm executa topologias em vez dos trabalhos [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) com os quais você pode estar familiarizado. As topologias do Storm são compostas por múltiplos componentes que são dispostos num grafo dirigido acíclico (DAG). Fluxos de dados entre os componentes no gráfico. Cada componente consome um ou mais fluxos de dados e podem, opcionalmente, emitir um ou mais fluxos. O diagrama seguinte ilustra a forma como os dados fluem entre componentes numa topologia de contagem de palavras básica:

![Exemplo da disposição dos componentes numa topologia Storm](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Os componentes spout trazem dados para uma topologia. Emitem um ou mais fluxos para a topologia.

* Os componentes bolt consomem fluxos emitidos por spouts ou outros bolts. Opcionalmente, os bolts podem emitir fluxos para a topologia. Os bolts também são responsáveis pela escrita de dados em serviços externos ou armazenamento, como o HDFS, Kafka ou HBase.

## <a name="reliability"></a>Fiabilidade

O Apache Storm garante que cada mensagem a receber é sempre totalmente processada, mesmo quando a análise de dados está distribuída por centenas de nós.

O nó Nimbus fornece funcionalidade semelhante à Apache Hadoop JobTracker e atribui tarefas a outros nós em um cluster por meio de [Apache ZooKeeper](https://zookeeper.apache.org/). Os nós do Zookeeper asseguram a coordenação de um cluster e facilitam a comunicação entre o Nimbus e o processo do Supervisor nos nós de trabalho. Se um nó de processamento deixa de funcionar, o nó Nimbus é informado e atribui a tarefa e os dados associados a um outro nó.

A configuração predefinida para os clusters do Apache Storm é com apenas um nó Nimbus. O Storm no HDInsight fornece dois nós Nimbus. Se o nó principal falhar, o cluster do Storm muda para o nó secundário enquanto o nó principal se recupera. O seguinte diagrama ilustra a configuração do fluxo de tarefas para o Storm no HDInsight:

![Diagrama de nimbus, zookeeper e supervisor](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-creation"></a>Facilidade de criação

Pode criar um cluster novo do Storm no HDInsight em minutos. Para obter mais informações sobre como criar um cluster Storm, consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="ease-of-use"></a>Facilidade de utilização

* __Conectividade de Secure Shell (SSH)__ : Você pode acessar os nós de cabeçalho do seu cluster Storm pela Internet usando o SSH. Pode executar comandos diretamente no seu cluster através de SSH.

  Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* __Conectividade__com a Web: Todos os clusters HDInsight fornecem a interface do usuário da Web do amAmbari. Pode monitorizar, configurar e gerir serviços facilmente no seu cluster através da IU Web do Ambari. Os clusters do Storm também fornecem a IU do Storm. Pode monitorizar e gerir topologias Storm em execução a partir do seu browser na IU do Storm.

  Para obter mais informações, consulte [gerenciar o HDInsight usando a interface do usuário da Web do Apache Ambari](../hdinsight-hadoop-manage-ambari.md) e [monitorar e gerenciar usando os documentos da interface do usuário do Apache Storm](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui) .

* __Azure PowerShell e CLI clássica do Azure__: O PowerShell e a CLI clássica fornecem utilitários de linha de comando que você pode usar do seu sistema cliente para trabalhar com o HDInsight e outros serviços do Azure.

* __Integração do Visual Studio__: Ferramentas do Azure Data Lake para Visual Studio incluir modelos de projeto para C# criar topologias Storm usando a estrutura SCP.net. As Ferramentas do Data Lake também proporcionam ferramentas para implementar, monitorizar e gerir soluções com o Storm no HDInsight.

  Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integração noutros serviços do Azure

* __Azure data Lake Storage__: Para obter um exemplo de como usar Data Lake Storage com um cluster Storm, consulte [usar o Azure data Lake Storage com Apache Storm no HDInsight](apache-storm-write-data-lake-store.md).

* __Hubs de eventos__: Para obter um exemplo de como usar hubs de eventos com um cluster Storm, consulte os exemplos a seguir:

    * [Processar eventos dos Hubs de eventos do Azure com o Apache Storm no HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/)

    * [Processar eventos dos Hubs de eventos do Azure com o Apache Storm no HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __Banco de dados SQL__, __Cosmos DB__, __hubs de eventos__e __HBase__: Os exemplos de modelo estão incluídos nas ferramentas de Data Lake para Visual Studio. Para obter mais informações, consulte [desenvolver C# uma topologia para Apache Storm no HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

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

Para obter informações sobre cenários do mundo real, consulte o documento [como as empresas estão usando Apache Storm](https://storm.apache.org/documentation/Powered-By.html) .

## <a name="development"></a>Desenvolvimento

Os programadores em .NET podem conceber e implementar topologias em C# com as Ferramentas do Data Lake para Visual Studio. Também pode criar topologias híbridas que utilizam componentes de Java e C#.

Para obter mais informações, consulte o artigo [Desenvolver topologias C# para Apache Storm no HDInsight com Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Também pode desenvolver soluções Java com o IDE da sua preferência. Para obter mais informações, consulte [desenvolver topologias Java para Apache Storm no HDInsight](apache-storm-develop-java-topology.md).

Também pode ser utilizado Python para desenvolver componentes do Storm. Para obter mais informações, consulte [desenvolver Apache Storm topologias usando Python no HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Padrões comuns de desenvolvimento

### <a name="guaranteed-message-processing"></a>Processamento de mensagens garantido

O Apache Storm pode fornecer diferentes níveis de processamento garantido de mensagens. Por exemplo, um aplicativo Storm básico pode garantir um processamento de pelo menos uma vez e o [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) pode garantir exatamente o processamento.

Para obter mais informações, consulte o artigo [Garantias no processamento de dados](https://storm.apache.org/about/guarantees-data-processing.html) em apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

O padrão de ler uma tupla de entrada, emitir zero ou mais tuplas e, em seguida, confirmar a tupla de entrada imediatamente no final do método Execute é comum. O Storm fornece a interface [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) para automatizar este padrão.

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

O Storm usa o [Apache Log4J 2](https://logging.apache.org/log4j/2.x/) para registrar informações. Por predefinição, é registada uma grande quantidade de dados, podendo ser difícil organizar-se nas informações. Pode incluir um ficheiro de configuração de registo como parte da topologia do Storm para controlar o comportamento dos registos.

Para uma topologia de exemplo que demonstra como configurar o registo, veja o exemplo [WordCount baseado em Java](apache-storm-develop-java-topology.md) para Storm no HDInsight.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre soluções de análise em tempo real com o Apache Storm no HDInsight:

* [Criar e monitorar uma topologia de Apache Storm no Azure HDInsight](apache-storm-quickstart.md)
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)
