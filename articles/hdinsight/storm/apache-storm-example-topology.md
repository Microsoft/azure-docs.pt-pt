---
title: Exemplo de topologias de Apache Storm no Azure HDInsight
description: Uma lista de topologias Storm de exemplo criadas e testadas com Apache Storm no C# HDInsight, incluindo topologias básicas e Java, e trabalhando com hubs de eventos.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 302ba583f11b15be98832316b1ea05c7f9be931f
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530668"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Exemplo Apache Storm topologias e componentes para Apache Storm no HDInsight

Veja a seguir uma lista de exemplos criados e mantidos pela Microsoft para uso com o [Apache Storm](https://storm.apache.org/) no HDInsight. Esses exemplos abrangem uma variedade de tópicos, desde a C# criação de topologias básicas e Java até o trabalho com serviços do Azure, como hubs de eventos, Cosmos DB, banco de dados SQL, [Apache HBase](https://hbase.apache.org/) no HDInsight e armazenamento do Azure. Alguns exemplos também demonstram como trabalhar com tecnologias não Azure ou até mesmo que não sejam da Microsoft, como Signalr e Socket.IO.

| Descrição | Demonstra | Idioma/estrutura |
|:--- |:--- |:--- |
| [Gravar em Azure Data Lake Storage de Apache Storm](apache-storm-write-data-lake-store.md) |Gravando em Azure Data Lake Storage |Java |
| [Spout do hub de eventos e origem do raio](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Fonte para Spout e parafuso do hub de eventos |Java |
| [Desenvolver topologias baseadas em Java para Apache Storm no HDInsight][5797064f] |Maven |Java |
| [Desenvolver C# topologias para Apache Storm no HDInsight usando o Visual Studio][16fce2d1] |Ferramentas do HDInsight para Visual Studio |C#, Java |
| [Processar eventos dos Hubs de eventos do Azure com o Apache Storm no HDInsight (C#)][844d1d81] |Event Hubs |C#e Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) (Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight [Java]) |Event Hubs |Java |
| [Processar dados de sensor de veículo dos hubs de eventos usando Apache Storm no HDInsight][246ee964] |Hubs de eventos, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [Extração, transformação e carregamento (ETL) dos hubs de eventos do Azure para o Apache HBase, usando Apache Storm no HDInsight][b4b68194] |Hubs de eventos, HBase |C# |
| [Projeto C# de topologia Storm de modelo para trabalhar com serviços do Azure de Apache Storm no HDInsight][ce0c02a2] |Hubs de eventos, Cosmos DB, banco de dados SQL, HBase, Signalr |C#, Java |
| [Benchmarks de escalabilidade para leitura de hubs de eventos do Azure usando o Apache Storm no HDInsight][d6c540e3] |Taxa de transferência de mensagem, hubs de eventos, banco de dados SQL |C#, Java |
| [Usar Apache Kafka com Apache Storm no HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm leitura e gravação Apache Kafka | Java |

> [!WARNING]  
> Os C# exemplos nesta lista foram criados e testados originalmente com o HDInsight baseado no Windows e podem não funcionar corretamente com clusters hdinsight baseados em Linux. Os clusters baseados em Linux usam o mono para executar o código .NET e podem ter problemas de compatibilidade com as estruturas e os pacotes usados no exemplo.
>
> O Linux é o único sistema operacional usado no HDInsight versão 3,4 ou posterior.

## <a name="python-only"></a>Somente Python

Consulte [usar o Python com o Apache Storm no HDInsight](apache-storm-develop-python-topology.md) para obter um exemplo de componentes do Python com uma topologia de fluxo.

## <a name="next-steps"></a>Próximos Passos

* [Criar e monitorar uma topologia de Apache Storm no Azure HDInsight](./apache-storm-quickstart.md)
* [Saiba como implantar e gerenciar topologias de Apache Storm com o Apache Storm no HDInsight][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Saiba como implantar e gerenciar topologias usando o painel de Apache Storm baseado na Web e a interface do usuário do Storm ou as ferramentas do HDInsight para Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Saiba como criar C# topologias Storm usando as ferramentas do HDInsight para Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Saiba como criar topologias Storm em Java, usando o Maven, criando uma topologia básica de WordCount."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Saiba como ler e gravar dados de hubs de eventos do Azure com o Storm no HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Saiba como usar uma topologia Storm para ler mensagens de hubs de eventos do Azure, ler documentos de Azure Cosmos DB para referenciar dados e salvar dados no armazenamento do Azure."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Várias topologias para demonstrar a taxa de transferência ao ler dos hubs de eventos do Azure e armazenar no banco de dados SQL usando o Apache Storm no HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Saiba como ler dados de hubs de eventos do Azure, agregar & transformar os dados e, em seguida, armazená-los no HBase no HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este projeto contém modelos para esgotamentos, parafusos e topologias para interagir com vários serviços do Azure, como hubs de eventos, Cosmos DB e banco de dados SQL."
