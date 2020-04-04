---
title: Utilização de Streams Akka para Apache Kafka - Hubs de eventos azure Microsoft Docs
description: Este artigo fornece informações sobre como ligar a Akka Streams a um centro de eventos Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: how-to
ms.date: 04/02/2020
ms.author: shvija
ms.openlocfilehash: 0b96f1448fd223aae2dde77c5c05a8c9bd74ee9b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632848"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Utilizar o Akka Streams com os Hubs de Eventos para o Apache Kafka
Este tutorial mostra-lhe como ligar a Akka Streams a um centro de eventos sem alterar os seus clientes protocolares ou executar os seus próprios clusters. O Azure Event Hubs para o Kafka suporta a [versão 1.0 da Apache Kafka.](https://kafka.apache.org/10/documentation.html)

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Executar produtor de Akka Streams 
> * Executar consumidor akka Streams

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, certifique-se de que tem os seguintes pré-requisitos:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Descarregue](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) um arquivo binário Maven
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Um espaço de nome de Event Hubs é necessário para enviar ou receber de qualquer serviço de Hubs de Eventos. Consulte [Criar um centro de eventos](event-hubs-create.md) para informações detalhadas. Certifique-se de copiar a cadeia de ligação De Eventos Hubs para posterior utilização.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que tem uma cadeia de ligação Event Hubs, clone os Hubs `akka` de Eventos Azure para o repositório Kafka e navegue para a subpasta:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Executar produtor de Akka Streams

Utilizando o exemplo fornecido do produtor Akka Streams, envie mensagens para o serviço De eventos Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Forneça um ponto final do Event Hubs Kafka

#### <a name="producer-applicationconf"></a>Aplicação do produtor.conf

Atualize `bootstrap.servers` `sasl.jaas.config` os `producer/src/main/resources/application.conf` valores e valores para direcionar o produtor para o ponto final do Event Hubs Kafka com a autenticação correta.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Executar produtor da linha de comando

Para executar o produtor a partir da linha de comando, gere o JAR e, em seguida, correr de dentro de Maven (ou gerar o JAR usando Maven, em seguida, correr em Java adicionando o(s) kafka necessário ao caminho da classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

O produtor começa a enviar eventos para o centro do evento no tópico `test`, e imprime os eventos para stdout.

## <a name="run-akka-streams-consumer"></a>Executar consumidor akka Streams

Utilizando o exemplo do consumidor fornecido, receba mensagens do centro do evento.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Forneça um ponto final do Event Hubs Kafka

#### <a name="consumer-applicationconf"></a>Aplicação do consumidor.conf

Atualize `bootstrap.servers` `sasl.jaas.config` os `consumer/src/main/resources/application.conf` valores e valores para direcionar o consumidor para o ponto final do Evento Hubs Kafka com a autenticação correta.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Executar consumidor a partir da linha de comando

Para executar o consumidor a partir da linha de comando, gere o JAR e, em seguida, correr de dentro de Maven (ou gerar o JAR usando Maven, em seguida, correr em Java adicionando o(s) kafka necessário ao caminho de classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Se o centro de eventos tiver eventos (por exemplo, se o `test`seu produtor também estiver em execução), então o consumidor começa a receber eventos do tópico . 

Consulte o [Guia Akka Streams Kafka](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) para obter informações mais detalhadas sobre os Streams acádios.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os Hubs de Eventos para Kafka, consulte os seguintes artigos:  

- [Espelhar um mediador de Kafka num hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ligar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Ligar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integrar kafka Connect com um hub de eventos](event-hubs-kafka-connect-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)
- [Guia de desenvolvimento apache Kafka para Hubs de Eventos Azure](apache-kafka-developer-guide.md)
