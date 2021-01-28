---
title: Exemplo Apache Storm toplogis in Azure HDInsight
description: Uma lista de exemplo topologias storm criadas e testadas com Apache Storm em HDInsight, incluindo as topologias básicas C# e Java, e trabalhando com Os Centros de Eventos.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 8dabfec18cb904fa72518428220991b817b53529
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928914"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Exemplo Apache Storm toplogis e componentes para Apache Storm on HDInsight

Segue-se uma lista de exemplos criados e mantidos pela Microsoft para utilização com [Apache Storm](https://storm.apache.org/) em HDInsight. Estes exemplos abrangem uma variedade de tópicos, desde a criação de topologias básicas C# e Java até ao trabalho com serviços Azure como Event Hubs, Cosmos DB, SQL Database, [Apache HBase](https://hbase.apache.org/) em HDInsight e Azure Storage. Alguns exemplos também demonstram como trabalhar com tecnologias não-Azure, ou mesmo não-Microsoft, como o SignalR e Socket.IO.

| Descrição | Demonstra | Linguagem/Enquadramento |
|:--- |:--- |:--- |
| [Escreva para Azure Data Lake Storage from Apache Storm](apache-storm-write-data-lake-store.md) |Escrevendo para Azure Data Lake Storage |Java |
| [Evento Hub Spout e Fonte Bolt](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Fonte para o Event Hub Spout e Bolt |Java |
| [Desenvolver topologias baseadas em Java para a Tempestade Apache em HDInsight][5797064f] |Maven |Java |
| [Desenvolver topologias C# para Apache Storm em HDInsight usando Visual Studio][16fce2d1] |Ferramentas HDInsight para Estúdio Visual |C# |
| [Eventos de processo de Azure Event Hubs com Apache Storm on HDInsight (C#)][844d1d81] |Hubs de Eventos |C# e Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) (Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight [Java]) |Hubs de Eventos |Java |
| [Processar dados do sensor do veículo a partir de Centros de Eventos usando a Tempestade Apache em HDInsight][246ee964] |Hubs de Eventos, Cosmos DB, Azure Storage Blob (WASB) |C# |
| [Extrato, Transformação e Carga (ETL) dos Hubs de Eventos Azure para Apache HBase, usando a Tempestade Apache em HDInsight][b4b68194] |Hubs de Eventos, HBase |C# |
| [Modelo C# Projeto de topologia de tempestade para trabalhar com os serviços Azure da Apache Storm em HDInsight][ce0c02a2] |Centros de Eventos, Cosmos DB, SQL Database, HBase, SignalR |C# |
| [Critérios de escalabilidade para leitura de Azure Event Hubs usando Apache Storm em HDInsight][d6c540e3] |Mensagem de produção, Centros de Eventos, Base de Dados SQL |C# |
| [Use Apache Kafka com Apache Storm em HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm lendo e escrevendo para Apache Kafka | Java |

> [!WARNING]  
> Os exemplos C# desta lista foram originalmente criados e testados com HDInsight baseado no Windows, e podem não funcionar corretamente com clusters HDInsight baseados em Linux. Os clusters baseados em Linux utilizam o Mono para executar o código .NET e podem ter problemas de compatibilidade com as estruturas e pacotes utilizados no exemplo.
>
> O Linux é o único sistema operativo utilizado na versão 3.4 ou posterior do HDInsight.

## <a name="python-only"></a>Apenas Python

Consulte [Use Python com Apache Storm em HDInsight](apache-storm-develop-python-topology.md) para um exemplo de componentes Python com uma topologia de fluxo.

## <a name="next-steps"></a>Passos Seguintes

* [Criar e monitorizar uma topologia da Tempestade Apache em Azure HDInsight](./apache-storm-quickstart.md)
* [Saiba como implementar e gerir as topologias da Tempestade Apache com a Tempestade Apache em HDInsight][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Aprenda a implementar e gerir topologias utilizando o Painel de Tempestade Apache e a Tempestade UI ou as Ferramentas HDInsight para o Estúdio Visual."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Aprenda a criar topologias de tempestade C# utilizando as Ferramentas HDInsight para Estúdio Visual."
[5797064f]:apache-storm-develop-java-topology.md "Aprenda a criar topologias storm em Java, usando Maven, criando uma topologia básica de contagem de palavras."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Aprenda a ler e a escrever dados dos Azure Event Hubs com Storm on HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Aprenda a usar uma topologia storm para ler mensagens de Azure Event Hubs, leia documentos da Azure Cosmos DB para referência de dados e guarde dados para o Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Várias topologias para demonstrar a produção ao ler a partir de Azure Event Hubs e armazenar para a Base de Dados SQL usando Apache Storm em HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Saiba como ler dados do Azure Event Hubs, agregar & transformar os dados e, em seguida, armazená-lo na HBase em HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este projeto contém modelos para bicos, parafusos e topologias para interagir com vários serviços Azure como Event Hubs, Cosmos DB e SQL Database."
