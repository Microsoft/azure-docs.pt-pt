---
title: Exemplo Apache Storm topoologias em Azure HDInsight
description: Uma lista de exemplos de topoologias storm criadas e testadas com Apache Storm no HDInsight, incluindo topologias básicas de C# e Java, e trabalhando com Hubs de Eventos.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 302ba583f11b15be98832316b1ea05c7f9be931f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530668"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Exemplo Apache Storm topoologias e componentes para Tempestade Apache no HDInsight

Segue-se uma lista de exemplos criados e mantidos pela Microsoft para utilização com [a Apache Storm](https://storm.apache.org/) no HDInsight. Estes exemplos abrangem uma variedade de tópicos, desde a criação de topologias básicas de C# e Java até trabalhar com serviços Azure como Hubs de Eventos, Cosmos DB, SQL Database, [Apache HBase](https://hbase.apache.org/) em HDInsight e Armazenamento Azure. Alguns exemplos também demonstram como trabalhar com tecnologias não-Azure, ou mesmo não-Microsoft, como signalR e Socket.IO.

| Descrição | Demonstra | Língua/Enquadramento |
|:--- |:--- |:--- |
| [Escreva para o armazenamento do lago De dados Azure da tempestade Apache](apache-storm-write-data-lake-store.md) |Escrita para armazenamento de lago de dados azure |Java |
| [Spout do Hub de Eventos e fonte bolt](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Fonte para o Spout e Bolt do Hub de Eventos |Java |
| [Desenvolver topoologias baseadas em Java para apache storm no HDInsight][5797064f] |Maven |Java |
| [Desenvolva topoologias C# para Apache Storm no HDInsight usando o Estúdio Visual][16fce2d1] |Ferramentas HDInsight para estúdio visual |C#, Java |
| [Processe eventos a partir de Hubs de Eventos Azure com Tempestade Apache em HDInsight (C#)][844d1d81] |Event Hubs |C# e Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) (Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight [Java]) |Event Hubs |Java |
| [Processar dados de sensores de veículos a partir de Centros de Eventos usando apache storm no HDInsight][246ee964] |Hubs de eventos, Cosmos DB, Blob de Armazenamento Azure (WASB) |C#, Java |
| [Extrato, Transformação e Carga (ETL) dos Hubs de Eventos Azure para Apache HBase, utilizando a Tempestade Apache no HDInsight][b4b68194] |Hubs de eventos, HBase |C# |
| [Projeto de topologia de tempestade modelo C# para trabalhar com serviços Azure da Tempestade Apache no HDInsight][ce0c02a2] |Hubs de eventos, Cosmos DB, Base de Dados SQL, HBase, SignalR |C#, Java |
| [Referências de escalabilidade para leitura de Hubs de Eventos Azure usando Apache Storm no HDInsight][d6c540e3] |Entrada de mensagem, Hubs de Eventos, Base de Dados SQL |C#, Java |
| [Use Apache Kafka com Tempestade Apache no HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm lendo e escrevendo para Apache Kafka | Java |

> [!WARNING]  
> Os exemplos C# desta lista foram originalmente criados e testados com hDInsight baseado no Windows, e podem não funcionar corretamente com clusters HDInsight baseados em Linux. Os clusters baseados em Linux usam mono para executar o código .NET, e podem ter problemas de compatibilidade com os quadros e pacotes utilizados no exemplo.
>
> O Linux é o único sistema operativo utilizado na versão 3.4 do HDInsight ou posterior.

## <a name="python-only"></a>Apenas Python

Consulte [use Python com Tempestade Apache no HDInsight](apache-storm-develop-python-topology.md) para um exemplo de componentes Python com uma topologia flux.

## <a name="next-steps"></a>Passos Seguintes

* [Crie e monitorize uma topologia de tempestade Apache em Azure HDInsight](./apache-storm-quickstart.md)
* [Saiba como implementar e gerir as topoologias da Tempestade Apache com a Tempestade Apache no HDInsight][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Aprenda a implementar e gerir topoologias utilizando o Apache Storm Dashboard e storm UI ou as Ferramentas HDInsight para Estúdio Visual."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Aprenda a criar topoologias de Tempestade C# utilizando as Ferramentas HDInsight para Estúdio Visual."
[5797064f]:apache-storm-develop-java-topology.md "Aprenda a criar topoologias de tempestade em Java, usando Maven, criando uma topologia básica de contagem de palavras."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Saiba ler e escrever dados de Azure Event Hubs com Storm no HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Saiba como usar uma topologia storm para ler mensagens do Azure Event Hubs, leia documentos do Azure Cosmos DB para referenciar dados e guarde dados para o Armazenamento Azure."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Várias topoologias para demonstrar a produção ao ler a partir de Hubs de Eventos Azure e armazenar para a Base de Dados SQL usando apache storm no HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Saiba como ler dados dos Hubs de Eventos Do Azure, agregar & transformar os dados e, em seguida, armazená-lo para HBase no HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este projeto contém modelos para bicos, parafusos e topoologias para interagir com vários serviços Azure como Hubs de Eventos, Cosmos DB e Base de Dados SQL."
