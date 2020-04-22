---
title: Guia de desenvolvimento apache Kafka para Centros de Eventos
description: Este artigo fornece links para artigos que descrevem como integrar as suas aplicações Kafka com o Azure Event Hubs.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: b4d9dc51451a06b2c87ddce78547655b51c6fabd
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729634"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Guia de desenvolvimento apache Kafka para Hubs de Eventos Azure
Este artigo fornece links para artigos que descrevem como integrar as suas aplicações Apache Kafka com hubs de eventos Azure. 

## <a name="overview"></a>Descrição geral
O Event Hubs fornece um ponto final de Kafka que pode ser usado pelas suas aplicações baseadas em Kafka como alternativa para executar o seu próprio cluster Kafka. O Event Hubs suporta o protocolo Apache Kafka 1.0 e mais tarde, e trabalha com as suas aplicações kafka existentes, incluindo mirrormaker. Para mais informações, consulte Os Centros de [Eventos para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Guias de Início Rápido
Você pode encontrar quickstarts no GitHub e neste conjunto de conteúdos que o ajuda a acelerar rapidamente em Hubs de Eventos para Kafka.

### <a name="quickstarts-in-github"></a>Quickstarts no GitHub
Veja as seguintes acelerações no **azure-event-hubs-for-kafka** repo: 

| Linguagem/enquadramento do cliente | Descrição | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Este quickstart mostrará como criar e ligar-se a um ponto final do Event Hubs Kafka usando um produtor e consumidor de exemplo escrito em C# usando .NET Core 2.0.</p><p>Esta amostra baseia-se no [cliente Apache Kafka .NET da Confluent,](https://github.com/confluentinc/confluent-kafka-dotnet)modificado para utilização com Hubs de Eventos para Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Este quickstart mostrará como criar e ligar-se a um ponto final do Event Hubs Kafka usando um produtor e consumidor de exemplo escrito em Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Este quickstart mostrará como criar e ligar-se a um ponto final do Event Hubs Kafka usando um produtor e consumidor de exemplo escrito no Nó.</p><p>Esta amostra usa a biblioteca [nó-rdkafka.](https://github.com/Blizzard/node-rdkafka) </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Este quickstart mostrará como criar e ligar-se a um ponto final do Event Hubs Kafka usando um produtor e consumidor de exemplo escrito em python.</p><p>Esta amostra baseia-se no [cliente Apache Kafka Python da Confluent,](https://github.com/confluentinc/confluent-kafka-python)modificado para uso com Hubs de Eventos para Kafka.</p>|
| [Ir](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Este quickstart mostrará como criar e ligar-se a um ponto final do Event Hubs Kafka usando um produtor e consumidor de exemplo escrito em Go.</p><p>Esta amostra baseia-se no [cliente Apache Kafka Golang da Confluent,](https://github.com/confluentinc/confluent-kafka-go)modificado para uso com Hubs de Eventos para Kafka.</p>| 
| [Sarama kafka Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Este quickstart mostrará como criar e ligar-se a um ponto final do Event Hubs Kafka usando um produtor e consumidor de exemplo escrito em Go usando a biblioteca [de clientes Sarama Kafka.](https://github.com/Shopify/sarama) |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Este quickstart mostrará como criar e ligar-se a um ponto final do Event Hubs Kafka usando o CLI que vem agregado com a distribuição Apache Kafka.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat é um consumidor e produtor de linha de comando não JVM baseado em librdkafka, popular devido à sua velocidade e pequena pegada. Este quickstart contém uma configuração de amostra e vários comandos simples de kafkacat de amostra. | 
 
### <a name="quickstarts-in-docs"></a>Quickstarts no DOCS
Consulte o quickstart: [Data streaming with Event Hubs using the Kafka protocol](event-hubs-quickstart-kafka-enabled-event-hubs.md) in this content set, which provide step-by-step instructions on how to streaming into Event Hubs. Aprende a usar os seus produtores e consumidores para falar com o Event Hubs com apenas uma mudança de configuração nas suas aplicações. 


## <a name="tutorials"></a>Tutoriais 

### <a name="tutorials-in-github"></a>Tutoriais em GitHub
Consulte os seguintes tutoriais no GitHub:

| Tutorial | Descrição | 
| ------------------------- | ----------- | 
| [Acádio](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Este tutorial mostra como ligar a Akka Streams a Centros de Eventos ativados por Kafka sem alterar os seus clientes protocolares ou executar os seus próprios clusters. Existem dois tutoriais separados usando linguagens de programação **Java** e **Scala.** | 
| [Ligar](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Este documento irá acompanhá-lo através da integração do Kafka Connect com os Hubs de Eventos Azure e da implementação de conectores básicos fileStreamSource e FileStreamSink. Embora estes conectores não sejam destinados à produção, demonstram um cenário de ligação de kafka de ponta a ponta onde o Azure Event Hubs se disfarça como corretor de Kafka.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Este documento irá acompanhá-lo através da integração de Filebeat e Event Hubs através da saída kafka do Filebeat. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | Este tutorial mostrará como ligar o Apache Flink a Centros de Eventos ativados por Kafka sem alterar os seus clientes protocolares ou executar os seus próprios clusters. | 
| [Fluente](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Este documento irá acompanhá-lo através da integração de Centros fluentes e de eventos utilizando o `out_kafka` plugin de saída para Fluente. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | Este tutorial mostra-lhe como trocar eventos entre consumidores e produtores usando diferentes protocolos. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Este tutorial irá acompanhá-lo através da integração de Logstash com Centros de Eventos ativados por Kafka usando plugins de entrada/saída Logstash Kafka. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | Este tutorial mostra como um hub de eventos e kafka MirrorMaker podem integrar um oleoduto kafka existente em Azure, espelhando o fluxo de entrada kafka no serviço Event Hubs. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | Este tutorial mostrará como ligar o Apache NiFi a um espaço de nome sinuoso do Event Hubs. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Os quickstarts mostram-lhe como criar e ligar-se a um ponto final do Event Hubs Kafka usando um produtor e consumidor de exemplo escrito em linguagens de programação Go e Java. |
| [Registo schema da Confluent](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Este tutorial irá acompanhá-lo através da integração de Schema Registry e Event Hubs para Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | Este tutorial mostrará como ligar a sua aplicação Spark a um hub de eventos sem alterar os seus clientes protocolares ou executar os seus próprios clusters Kafka. | 

### <a name="tutorials-in-docs"></a>Tutoriais no DOCS
Além disso, consulte o tutorial: [Processe o Apache Kafka para eventos de Event Hubs utilizando](event-hubs-kafka-stream-analytics.md) a análise do Stream neste conjunto de conteúdos, que mostra como transmitir dados para Centros de Eventos e processá-lo com o Azure Stream Analytics.

## <a name="how-to-guides"></a>Guias de procedimentos
Consulte os seguintes guias como fazer na nossa documentação:

| Artigo | Descrição | 
| ------- | ----------- | 
| [Espelhar um mediador de Kafka num hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md) | Mostra como espelhar um corretor kafka num centro de eventos usando Kafka MirrorMaker. |
| [Ligar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md) | Acompanha-o através da ligação da sua aplicação Spark aos Centros de Eventos para streaming em tempo real. |
| [Ligar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md) | Mostra-lhe como ligar o Apache Flink a um centro de eventos sem alterar os seus clientes protocolares ou executar os seus próprios clusters. |
| [Integrar o Apache Kafka Connect com um centro de eventos (Pré-visualização)](event-hubs-kafka-connect-tutorial.md) | Acompanha-o através da integração do Kafka Connect com um hub de eventos e da implementação de conectores básicos fileStreamSource e FileStreamSink. |
| [Ligar o Akka Streams a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md) | Mostra-lhe como ligar a Akka Streams a um centro de eventos sem alterar os seus clientes protocolares ou executar os seus próprios clusters. |
| [Use o arranque de bota de primavera para Apache Kafka com Hubs de Eventos Azure](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Demonstra como configurar um Cloud Stream Binder baseado em Java criado com o Ininicializador de Botas de primavera para usar Apache Kafka com Hubs de Eventos Azure. |

## <a name="next-steps"></a>Passos seguintes
Reveja as amostras no GitHub [repo-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) sob pastas quickstart e tutoriais.

Consulte também os seguintes artigos:

- [Guia de resolução de problemas apache Kafka para Centros de Eventos](apache-kafka-troubleshooting-guide.md)
- [Perguntas frequentes - Centros de Eventos para Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Guia de migração Apache Kafka para Centros de Eventos](apache-kafka-migration-guide.md)



