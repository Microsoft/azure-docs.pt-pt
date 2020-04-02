---
title: Use Apache Flink para Apache Kafka - Hubs de eventos Azure / Microsoft Docs
description: Este artigo fornece informações sobre como ligar o Apache Flink a um centro de eventos Azure
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6ab542e1328bb986f53d31e2eca75007cf1e0c75
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521789"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Utilizar o Apache Flink com os Hubs de Eventos do Azure para Apache Kafka
Este tutorial mostra-lhe como ligar o Apache Flink a um centro de eventos sem alterar os seus clientes protocolares ou executar os seus próprios clusters. O Azure Event Hubs suporta a [versão 1.0 da Apache Kafka.](https://kafka.apache.org/10/documentation.html)

Um dos principais benefícios da utilização do Apache Kafka é o ecossistema de estruturas a que se pode ligar. Os Centros de Eventos combinam a flexibilidade de Kafka com a escalabilidade, consistência e apoio do ecossistema Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Executar produtor flink 
> * Executar o consumidor flink

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, certifique-se de que tem os seguintes pré-requisitos:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Kit de Desenvolvimento Java (JDK) 1.7+](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Descarregue](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) um arquivo binário Maven
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Um espaço de nome de Event Hubs é necessário para enviar ou receber de qualquer serviço de Hubs de Eventos. Consulte [a Criação de um centro](event-hubs-create.md) de eventos para instruções para criar um espaço de nome e um centro de eventos. Certifique-se de copiar a cadeia de ligação De Eventos Hubs para posterior utilização.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que tem a cadeia de ligação Event Hubs, clone os Hubs `flink` de Eventos Azure para o repositório Kafka e navegue para a subpasta:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Executar produtor flink

Utilizando o exemplo fornecido pelo produtor Flink, envie mensagens para o serviço Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Forneça um ponto final do Event Hubs Kafka

#### <a name="producerconfig"></a>produtor.config

Atualize `bootstrap.servers` `sasl.jaas.config` os `producer/src/main/resources/producer.config` valores e valores para direcionar o produtor para o ponto final do Event Hubs Kafka com a autenticação correta.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Executar produtor da linha de comando

Para executar o produtor a partir da linha de comando, gere o JAR e, em seguida, correr de dentro de Maven (ou gerar o JAR usando Maven, em seguida, correr em Java adicionando o(s) kafka necessário ao caminho da classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

O produtor vai agora começar a enviar `test` eventos para o centro do evento no tópico e imprimir os eventos para stdout.

## <a name="run-flink-consumer"></a>Executar o consumidor flink

Utilizando o exemplo do consumidor fornecido, receba mensagens do centro do evento. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Forneça um ponto final do Event Hubs Kafka

#### <a name="consumerconfig"></a>consumidor.config

Atualize `bootstrap.servers` `sasl.jaas.config` os `consumer/src/main/resources/consumer.config` valores e valores para direcionar o consumidor para o ponto final do Evento Hubs Kafka com a autenticação correta.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Executar consumidor a partir da linha de comando

Para executar o consumidor a partir da linha de comando, gere o JAR e, em seguida, correr de dentro de Maven (ou gerar o JAR usando Maven, em seguida, correr em Java adicionando o(s) kafka necessário ao caminho de classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Se o centro de eventos tiver eventos (por exemplo, se o seu `test`produtor também estiver em execução), então o consumidor começa agora a receber eventos do tema .

Consulte o Guia de [Ligação Kafka da Flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) para obter informações mais detalhadas sobre a ligação do Flink a Kafka.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a ligar o Apache Flink aos Event Hubs sem alterar os seus clientes protocolares ou executar os seus próprios clusters. Realizou os seguintes passos como parte deste tutorial: 

> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Executar produtor flink 
> * Executar o consumidor flink

Para saber mais sobre os Hubs de Eventos e os Hubs de Eventos para Kafka, veja os tópicos seguintes:  

- [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) (Hubs de Eventos para Apache Kafka)
- [Criar um hub de eventos](event-hubs-create.md)
- [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Espelhar um mediador de Kafka num hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ligar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Integrar kafka Connect com um hub de eventos](event-hubs-kafka-connect-tutorial.md)
- [Ligue a Akka Streams a um centro de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)
