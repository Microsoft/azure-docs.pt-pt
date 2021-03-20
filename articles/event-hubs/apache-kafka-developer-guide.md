---
title: Guia de desenvolvedores apache Kafka para centros de eventos
description: Este artigo fornece links para artigos que descrevem como integrar as suas aplicações Kafka com Azure Event Hubs.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 757db4faf2a933e366bc818d5cf6aab04d0b08ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90061738"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Guia de desenvolvimento apache Kafka para hubs de eventos Azure
Este artigo fornece links para artigos que descrevem como integrar as suas aplicações Apache Kafka com Azure Event Hubs. 

## <a name="overview"></a>Descrição Geral
O Event Hubs fornece um ponto final kafka que pode ser usado pelas suas aplicações existentes baseadas em Kafka como uma alternativa para executar o seu próprio cluster Kafka. O Event Hubs trabalha com muitas das suas aplicações kafka existentes. Para mais informações, consulte [Os Centros de Eventos para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Guias de Início Rápido
Você pode encontrar quickstarts no GitHub e neste conjunto de conteúdos que o ajuda rapidamente a subir em Event Hubs para Kafka.

### <a name="quickstarts-in-github"></a>Quickstarts em GitHub
Veja os seguintes quickstarts no **azure-event-hubs-for-kafka** repo: 

| Linguagem/enquadramento do cliente | Description | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Este quickstart mostrará como criar e conectar-se a um ponto final de Event Hubs Kafka usando um produtor de exemplo e consumidor escrito em C# usando .NET Core 2.0.</p><p>Esta amostra baseia-se no [cliente Apache Kafka .NET da Confluent,](https://github.com/confluentinc/confluent-kafka-dotnet)modificado para utilização com Centros de Eventos para Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Este quickstart mostrará como criar e conectar-se a um ponto final de Event Hubs Kafka usando um produtor e consumidor de exemplo escrito em Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Este quickstart mostrará como criar e conectar-se a um ponto final de Event Hubs Kafka usando um produtor de exemplo e consumidor escrito em Nó.</p><p>Esta amostra usa a biblioteca [nó-rdkafka.](https://github.com/Blizzard/node-rdkafka) </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Este quickstart mostrará como criar e conectar-se a um ponto final de Event Hubs Kafka usando um produtor e consumidor de exemplo escrito em python.</p><p>Esta amostra baseia-se no [cliente Apache Kafka Python da Confluent,](https://github.com/confluentinc/confluent-kafka-python)modificado para uso com Centros de Eventos para Kafka.</p>|
| [Ir](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Este quickstart mostrará como criar e conectar-se a um ponto final de Event Hubs Kafka usando um produtor de exemplo e consumidor escrito em Go.</p><p>Esta amostra baseia-se no [cliente Apache Kafka Golang da Confluent,](https://github.com/confluentinc/confluent-kafka-go)modificado para uso com Centros de Eventos para Kafka.</p>| 
| [Sarama kafka Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Este quickstart mostrará como criar e conectar-se a um ponto final de Event Hubs Kafka usando um produtor e consumidor de exemplo escrito em Go usando a biblioteca [de clientes Sarama Kafka.](https://github.com/Shopify/sarama) |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Este quickstart mostrará como criar e conectar-se a um ponto final de Event Hubs Kafka usando o CLI que vem agregado com a distribuição Apache Kafka.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | Kafkacat é um consumidor e produtor de linha de comando não-JVM baseado na librdkafka, popular devido à sua velocidade e pequena pegada. Este quickstart contém uma configuração de amostra e vários comandos simples de kafkacat de amostra. | 
 
### <a name="quickstarts-in-docs"></a>Quickstarts no DOCS
Consulte o quickstart: [Streaming de dados com Os Centros de Eventos utilizando o protocolo Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) neste conjunto de conteúdos, que fornece instruções passo a passo sobre como transmitir para Os Centros de Eventos. Aprende a usar os seus produtores e consumidores para falar com o Event Hubs com apenas uma alteração de configuração nas suas aplicações. 


## <a name="tutorials"></a>Tutoriais 

### <a name="tutorials-in-github"></a>Tutoriais em GitHub
Veja os seguintes tutoriais no GitHub:

| Tutorial | Description | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Este tutorial mostra como ligar akka Streams aos Centros de Eventos ativados pela Kafka sem alterar os seus clientes de protocolo ou executar os seus próprios clusters. Existem dois tutoriais separados usando linguagens de programação **java** e **Scala.** | 
| [Ligar](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Este documento irá acompanhá-lo através da integração do Kafka Connect com os Azure Event Hubs e da implementação de conectores básicos FileStreamSource e FileStreamSink. Embora estes conectores não sejam destinados ao uso da produção, demonstram um cenário de ligação kafka de ponta a ponta onde o Azure Event Hubs se disfarça como um corretor Kafka.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Este documento irá acompanhá-lo através da integração de Filebeat e Event Hubs através da saída kafka do Filebeat. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | Este tutorial mostrará como ligar o Apache Flink aos Centros de Eventos ativados pela Kafka sem alterar os seus clientes de protocolo ou executar os seus próprios clusters. | 
| [Fluente](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Este documento irá acompanhá-lo através da integração fluentd e Event Hubs usando o `out_kafka` plugin de saída para Fluentd. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | Este tutorial mostra-lhe como trocar eventos entre consumidores e produtores usando diferentes protocolos. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Este tutorial irá acompanhá-lo através da integração de Logstash com Centros de Eventos ativados por Kafka usando plugins de entrada/saída Logstash Kafka. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | Este tutorial mostra como um centro de eventos e Kafka MirrorMaker podem integrar um oleoduto Kafka existente em Azure, espelhando o fluxo de entrada kafka no serviço Event Hubs. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | Este tutorial mostrará como ligar o Apache NiFi a um espaço de nomes de Event Hubs. | 
| [Oauth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Os Quickstarts mostram-lhe como criar e conectar-se a um ponto final de Event Hubs Kafka usando um produtor e consumidor de exemplo escrito em linguagens de programação Go e Java. |
| [Registo de Schema da Confluent](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Este tutorial irá acompanhá-lo através da integração de Schema Registry and Event Hubs para Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | Este tutorial mostrará como ligar a sua aplicação Spark a um centro de eventos sem alterar os seus clientes de protocolo ou executar os seus próprios clusters Kafka. | 

### <a name="tutorials-in-docs"></a>Tutoriais em DOCS
Além disso, consulte o tutorial: [Processa Apache Kafka para eventos de Event Hubs usando a analytics Stream](event-hubs-kafka-stream-analytics.md) neste conjunto de conteúdos, que mostra como transmitir dados em Centros de Eventos e processá-lo com Azure Stream Analytics.

## <a name="how-to-guides"></a>Guias de procedimentos
Consulte os seguintes guias de como fazer na nossa documentação:

| Artigo | Description | 
| ------- | ----------- | 
| [Espelhar um mediador de Kafka num hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md) | Mostra como espelhar um corretor Kafka num centro de eventos usando Kafka MirrorMaker. |
| [Ligar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md) | Acompanha-o através da ligação da sua aplicação Spark aos Centros de Eventos para streaming em tempo real. |
| [Ligar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md) | Mostra-lhe como ligar o Apache Flink a um centro de eventos sem alterar os seus clientes de protocolo ou executar os seus próprios clusters. |
| [Integre o Apache Kafka Connect com um centro de eventos (Pré-visualização)](event-hubs-kafka-connect-tutorial.md) | Acompanha-o através da integração do Kafka Connect com um centro de eventos e da implementação de conectores básicos FileStreamSource e FileStreamSink. |
| [Ligar o Akka Streams a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md) | Mostra-lhe como ligar a Akka Streams a um centro de eventos sem alterar os seus clientes de protocolo ou executar os seus próprios clusters. |
| [Use o arranque de botas de mola para Apache Kafka com hubs de eventos Azure](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Demonstra como configurar um Binder de fluxo de nuvem de mola baseado em Java criado com o Inicializador de Botas de Mola para usar Apache Kafka com Azure Event Hubs. |

## <a name="next-steps"></a>Passos seguintes
Reveja amostras no GitHub repo [azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) sob pastas de arranque rápido e tutoriais.

Consulte também os seguintes artigos:

- [Guia de resolução de problemas de Apache Kafka para centros de eventos](apache-kafka-troubleshooting-guide.md)
- [Perguntas frequentes - Centros de Eventos para Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Guia de migração apache Kafka para centros de eventos](apache-kafka-migration-guide.md)



