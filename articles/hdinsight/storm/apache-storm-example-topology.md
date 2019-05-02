---
title: Topologias do Apache Storm de exemplo no Azure HDInsight
description: Uma lista de topologias do Storm de exemplo criado e testado com o Apache Storm no HDInsight, incluindo as topologias de c# e Java básicas e trabalhar com os Hubs de eventos.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: b2407200b995ad956f233248f6cdd58488b583d6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688792"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Topologias do Apache Storm de exemplo e componentes para o Apache Storm no HDInsight

Segue-se uma lista de exemplos criados e mantidos pela Microsoft para utilização com [Apache Storm](https://storm.apache.org/) no HDInsight. Estes exemplos abrangem uma variedade de tópicos, desde a criação básica C# e topologias de Java para trabalhar com os serviços do Azure, como os Hubs de eventos, Cosmos DB, base de dados SQL [Apache HBase](https://hbase.apache.org/) no HDInsight e o armazenamento do Azure. Alguns exemplos também demonstram como trabalhar com tecnologias não pertencente ao Azure, ou até mesmo não à Microsoft, como o SignalR e Socket.IO.

| Descrição | Demonstra | Arquitetura/linguagem |
|:--- |:--- |:--- |
| [Escrever no armazenamento do Azure Data Lake do Apache Storm](apache-storm-write-data-lake-store.md) |Escrever no armazenamento do Azure Data Lake |Java |
| [Origem de Spout do Hub de eventos e Bolt](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Origem de Spout do Hub de eventos e Bolt |Java |
| [Desenvolver topologias baseadas em Java para Apache Storm no HDInsight][5797064f] |Maven |Java |
| [Desenvolver topologias c# para Apache Storm no HDInsight com o Visual Studio][16fce2d1] |Ferramentas do HDInsight para o Visual Studio |C#, Java |
| [Processar eventos dos Hubs de eventos do Azure com o Apache Storm no HDInsight (C#)][844d1d81] |Hubs de Eventos |C# e Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) (Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight [Java]) |Hubs de Eventos |Java |
| [Processar dados de sensores de veículos de Hubs de eventos com o Apache Storm no HDInsight][246ee964] |Blob de armazenamento do Azure Event Hubs, Cosmos DB, (WASB) |C#, Java |
| [Extração, transformação e carregamento (ETL) dos Hubs de eventos do Azure para o Apache HBase, utilizar o Apache Storm no HDInsight][b4b68194] |Hubs de eventos, HBase |C# |
| [Modelo C# project de topologia do Storm para trabalhar com os serviços do Azure do Apache Storm no HDInsight][ce0c02a2] |Event Hubs, Cosmos DB, SQL da base de dados, HBase, SignalR |C#, Java |
| [Parâmetros de comparação de escalabilidade para ler a partir dos Hubs de eventos do Azure com o Apache Storm no HDInsight][d6c540e3] |Débito de mensagem, os Hubs de eventos, base de dados SQL |C#, Java |
| [Utilizar Python com o Apache Storm no HDInsight](apache-storm-develop-python-topology.md) |Componentes de Python com uma topologia de fluxo |Python |
| [Utilizar o Apache Kafka com o Apache Storm no HDInsight](../hdinsight-apache-storm-with-kafka.md) | O Apache Storm, leitura e gravação ao Apache Kafka | Java |

> [!WARNING]  
> Os exemplos de c# nesta lista foram originalmente criados e testados com o HDInsight baseado em Windows e Maio não funcionam corretamente com clusters do HDInsight baseado em Linux. Clusters baseados em Linux utilizam o Mono para executar o código .NET e podem ter problemas de compatibilidade com as arquiteturas e pacotes utilizados no exemplo.
>
> Linux é o único sistema operativo utilizado no HDInsight versão 3.4 ou posterior.

### <a name="next-steps"></a>Próximos Passos

* [Introdução ao Apache Storm no HDInsight][2b8c3488]
* [Saiba como implementar e gerir topologias Apache Storm, Apache Storm no HDInsight][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Saiba como criar um Apache Storm no cluster do HDInsight e utilizar o Storm Dashboard para implementar topologias de exemplo."
[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Saiba como implementar e gerir topologias com o Dashboard do Storm baseado na web Apache e a IU do Storm ou as ferramentas do HDInsight para Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Saiba como criar topologias Storm c#, utilizando as ferramentas do HDInsight para Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Saiba como criar topologias Storm no Java, com o Maven, através da criação de uma topologia do wordcount básica."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Saiba como ler e escrever dados a partir dos Hubs de eventos do Azure com o Storm no HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Saiba como utilizar uma topologia do Storm para ler mensagens a partir dos Hubs de eventos do Azure, leia os documentos do Azure Cosmos DB para referenciar a dados e guarde os dados ao armazenamento do Azure."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Várias topologias para demonstrar o débito ao ler a partir de Event Hubs do Azure e armazenamento para a base de dados SQL com o Apache Storm no HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Saiba como ler dados a partir dos Hubs de eventos do Azure, agregação e transformar os dados, em seguida, armazená-los para o HBase no HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este projeto contém modelos para spouts, os bolts e topologias para interagir com vários serviços do Azure, como os Hubs de eventos, o Cosmos DB e base de dados SQL."

