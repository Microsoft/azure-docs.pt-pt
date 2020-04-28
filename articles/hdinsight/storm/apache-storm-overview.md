---
title: O que é o Apache Storm - Azure HDInsight
description: O Apache Storm permite-lhe processar fluxos de dados em tempo real. O Azure HDInsight permite-lhe criar facilmente clusters do Storm na cloud do Azure. Com o Visual Studio, pode criar soluções Storm com c# e implementar nos seus clusters do HDInsight Storm.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 97b1466e6ac1f2c2dfb931655b64b0f9937ba21d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183267"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>O que é o Apache Storm no Azure HDInsight?

O [Apache Storm](https://storm.apache.org/) é um sistema de computação distribuído com tolerância a falhas e de código aberto. Você pode usar storm para processar fluxos de dados em tempo real com [Apache Hadoop](../hadoop/apache-hadoop-introduction.md). As soluções de tempestade também podem fornecer o processamento garantido de dados, com a capacidade de reproduzir dados que não foram processados com sucesso da primeira vez.

## <a name="why-use-apache-storm-on-hdinsight"></a>Porquê usar a Tempestade Apache no HDInsight?

O Storm no HDInsight oferece as funcionalidades seguintes:

* __99% Acordo de Nível de Serviço (SLA) sobre o tempo de tempestade__: Tempestade no HDInsight vem com suporte contínuo total. O Storm no HDInsight tem também um SLA de 99,9 por cento. Isto significa que a Microsoft garante que os clusters do Storm têm conectividade externa em, pelo menos, 99,9 por cento das vezes. Para obter mais informações, veja o [suporte do Azure](https://azure.microsoft.com/support/options/). Consulte também informações sobre SLA para o documento [HDInsight.](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/)

* Suporta a personalização fácil ao executar scripts face a um cluster do Storm durante ou após a criação. Para obter mais informações, veja [Customize HDInsight clusters using script action (Personalizar clusters do HDInsight com ações de script)](../hdinsight-hadoop-customize-cluster-linux.md).

* **Crie soluções em várias linguagens**: pode escrever os componentes do Storm na linguagem que preferir, como Java, C# e Python.

    * Integra o Visual Studio no HDInsight para o desenvolvimento, a gestão e a monitorização das topologias C#. Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](apache-storm-develop-csharp-visual-studio-topology.md).

    * Suporta a interface de Java Trident. Pode criar topologias Storm que suportam o processamento de mensagens "exatamente uma vez", persistência do arquivo de dados transacional e um conjunto de operações de análise de fluxo comuns.

* **Dimensionamento dinâmico**: pode adicionar ou remover nós de trabalho sem qualquer impacto nas topologias Storm em execução. Desative e reative as topoologias de execução para tirar partido de novos nós adicionados através de operações de escala.

* **Criar gasodutos de streaming utilizando vários serviços Azure**: Storm on HDInsight integra-se com outros serviços Azure. Tais como Hubs de Eventos, Base de Dados SQL, Armazenamento Azure e Armazenamento de Lagos De Dados Azure. Para uma solução de exemplo que se integra com os serviços Azure, consulte [eventos de processo sacana com tempestade Apache no HDInsight](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

Para obter uma lista de empresas que utiliza o Apache Storm como solução de análise em tempo real, veja [Companies using Apache Storm](https://storm.apache.org/Powered-By.html) (Empresas que Utilizam o Apache Storm).

Para começar a usar storm, consulte [Create e monitorize uma topologia de tempestade Apache em Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>Como funciona a Tempestade Apache

A tempestade faz topoologias em vez do Mapa Apache [HadoopReduzir](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) empregos que possa sê-lo. As topologias do Storm são compostas por múltiplos componentes que são dispostos num grafo dirigido acíclico (DAG). Fluxos de dados entre os componentes no gráfico. Cada componente consome um ou mais fluxos de dados e podem, opcionalmente, emitir um ou mais fluxos. O diagrama seguinte ilustra a forma como os dados fluem entre componentes numa topologia de contagem de palavras básica:

![Exemplo da disposição dos componentes numa topologia Storm](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Os componentes spout trazem dados para uma topologia. Emitem um ou mais fluxos para a topologia.

* Os componentes bolt consomem fluxos emitidos por spouts ou outros bolts. Opcionalmente, os bolts podem emitir fluxos para a topologia. Os bolts também são responsáveis pela escrita de dados em serviços externos ou armazenamento, como o HDFS, Kafka ou HBase.

## <a name="reliability"></a>Fiabilidade

O Apache Storm garante que cada mensagem a receber é sempre totalmente processada, mesmo quando a análise de dados está distribuída por centenas de nós.

O nó Nimbus fornece uma funcionalidade semelhante ao Apache Hadoop JobTracker. Nimbus atribui tarefas a outros nós num aglomerado através do Apache ZooKeeper. Os nós do zookeeper fornecem coordenação para um cluster e ajudam a comunicação entre nimbus e o processo supervisor sobre os nós dos trabalhadores. Se um nó de processamento deixa de funcionar, o nó Nimbus é informado e atribui a tarefa e os dados associados a um outro nó.

A configuração predefinida para os clusters do Apache Storm é com apenas um nó Nimbus. O Storm no HDInsight fornece dois nós Nimbus. Se o nó principal falhar, o cluster do Storm muda para o nó secundário enquanto o nó principal se recupera. O seguinte diagrama ilustra a configuração do fluxo de tarefas para o Storm no HDInsight:

![Diagrama de nimbus, zookeeper e supervisor](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-use"></a>Facilidade de utilização

|Utilizar |Descrição |
|---|---|
|Conectividade Secure Shell (SSH)|Você pode aceder aos nós da cabeça do seu aglomerado de tempestade através da Internet usando SSH. Pode executar comandos diretamente no seu cluster através de SSH. Para mais informações, consulte [Use SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).|
|Conectividade web|Todos os clusters HDInsight fornecem a UI web Ambari. Pode monitorizar, configurar e gerir serviços facilmente no seu cluster através da IU Web do Ambari. Os clusters do Storm também fornecem a IU do Storm. Pode monitorizar e gerir topologias Storm em execução a partir do seu browser na IU do Storm. Para obter mais informações, consulte o [Manage HDInsight utilizando o Apache Ambari Web UI](../hdinsight-hadoop-manage-ambari.md) e [monitor e gerencie usando os](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui) documentos Apache Storm UI.|
|Azure PowerShell e Azure CLI|A PowerShell e a Azure CLI fornecem ambos utilitários de linha de comando que pode utilizar a partir do seu sistema de clientes para trabalhar com o HDInsight e outros serviços Azure.|
|Integração com o Visual Studio|As ferramentas do Lago de Dados Azure para o Estúdio Visual incluem modelos de projeto para a criação de topologias C# Storm utilizando a estrutura SCP.NET. As Ferramentas do Data Lake também proporcionam ferramentas para implementar, monitorizar e gerir soluções com o Storm no HDInsight. Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](apache-storm-develop-csharp-visual-studio-topology.md).|

## <a name="integration-with-other-azure-services"></a>Integração noutros serviços do Azure

* Armazenamento de __lagos azure data__: Veja use armazenamento de lago de dados [azure com tempestade Apache em HDInsight](apache-storm-write-data-lake-store.md).

* __Hubs de Eventos__: para obter um exemplo de como utilizar os Hubs de Eventos com um cluster do Storm, veja os exemplos seguintes:

    * [Eventos de processo saqueados de Hubs de Eventos Azure com Tempestade Apache no HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Processe eventos a partir de Hubs de Eventos Azure com Tempestade Apache em HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __Base de Dados SQL__, __Cosmos DB__, __Hubs de Eventos__ e __HBase__: exemplos do modelos estão incluídos nas Ferramentas do Data Lake para Visual Studio. Para mais informações, consulte [Desenvolver uma topologia C# para apache storm no HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="apache-storm-use-cases"></a>Casos de utilização do Apache Storm

Seguem-se alguns cenários comuns nos quais poderá utilizar o Storm no HDInsight:

* Internet das Coisas (IoT)
* Deteção de fraudes
* Análise social
* Extração, transformação e carregamento (ETL)
* Monitorização de rede
* Pesquisa
* Mobile Engagement

Para obter informações sobre cenários do mundo real, consulte a [forma como as empresas estão](https://storm.apache.org/Powered-By.html) a usar o documento Apache Storm.

## <a name="development"></a>Desenvolvimento

Os programadores em .NET podem conceber e implementar topologias em C# com as Ferramentas do Data Lake para Visual Studio. Também pode criar topologias híbridas que utilizam componentes de Java e C#. Para obter mais informações, consulte o artigo [Desenvolver topologias C# para Apache Storm no HDInsight com Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Também pode desenvolver soluções Java com o IDE da sua preferência. Para mais informações, consulte [Desenvolver topoologias java para apache storm no HDInsight](apache-storm-develop-java-topology.md).

Também pode ser utilizado Python para desenvolver componentes do Storm. Para mais informações, consulte [Desenvolver topoologias de tempestade Apache utilizando Python no HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Padrões comuns de desenvolvimento

### <a name="guaranteed-message-processing"></a>Processamento de mensagens garantido

O Apache Storm pode fornecer diferentes níveis de processamento garantido de mensagens. Por exemplo, uma aplicação de tempestade básica garante pelo menos uma vez o processamento, e tridente pode garantir exatamente uma vez processamento. Consulte [garantias sobre o processamento](https://storm.apache.org/about/guarantees-data-processing.html) de dados em apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

O padrão de leitura de uma túnica de entrada, emitindo zero ou mais tuples, e, em seguida, confirmando a entrada tuple imediatamente no final do método de execução é comum. O Storm fornece a interface [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) para automatizar este padrão.

### <a name="joins"></a>Associações

O modo como os fluxos de dados são associados varia consoante as aplicações. Por exemplo, pode juntar cada tuple de vários riachos em um novo fluxo, ou juntar apenas lotes de tuples para uma janela específica. De qualquer forma, a associação pode ser efetuada através de [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). O agrupamento de campos é uma forma de definir como as tuplas são encaminhadas para bolts.

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

A tempestade usa Apache Log4j 2 para registar informações. Por predefinição, é registada uma grande quantidade de dados, podendo ser difícil organizar-se nas informações. Pode incluir um ficheiro de configuração de registo como parte da topologia do Storm para controlar o comportamento dos registos.

Para uma topologia de exemplo que demonstra como configurar o registo, veja o exemplo [WordCount baseado em Java](apache-storm-develop-java-topology.md) para Storm no HDInsight.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre soluções de análise em tempo real com apache storm no HDInsight:

* [Crie e monitorize uma topologia de tempestade Apache em Azure HDInsight](apache-storm-quickstart.md)
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)
