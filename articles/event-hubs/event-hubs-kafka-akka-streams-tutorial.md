---
title: Usando fluxos Akka para Apache Kafka - Azure Event Hubs| Microsoft Docs
description: Este artigo fornece informações sobre como ligar a Akka Streams a um centro de eventos Azure.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 92ab927189329493696c70b61ffc7f11cad22a66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369578"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Utilizar o Akka Streams com os Hubs de Eventos para o Apache Kafka

Este tutorial mostra-lhe como conectar a Akka Streams através do suporte do Event Hubs para Apache Kafka sem alterar os seus clientes de protocolo ou executar os seus próprios clusters. 

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Executar produtor de Akka Streams 
> * Executar consumidor de Akka Streams

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, certifique-se de que tem os seguintes pré-requisitos:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.8+](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Faça o download](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) um arquivo binário Maven
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Um espaço de nomes do Event Hubs é necessário para enviar ou receber de qualquer serviço de Event Hubs. Consulte [Criar um centro de eventos](event-hubs-create.md) para obter informações detalhadas. Certifique-se de que copia o fio de ligação Do Event Hubs para utilização posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que tem uma cadeia de conexão Event Hubs, clone os Hubs de Eventos Azure para o repositório de Kafka e navegue até à `akka` sub-ligação:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Executar produtor de Akka Streams

Utilizando o exemplo de produtor Akka Streams fornecido, envie mensagens para o serviço Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto final de Eventos Hubs Kafka

#### <a name="producer-applicationconf"></a>Aplicação do produtor.conf

Atualize os `bootstrap.servers` valores e `sasl.jaas.config` valores `producer/src/main/resources/application.conf` para direcionar o produtor para o ponto final do Event Hubs Kafka com a autenticação correta.

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

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}`Substitua-a pela cadeia de ligação para o seu espaço de nomes 'Centros de Eventos'. Para obter instruções sobre a obtenção da cadeia de ligação, consulte [obter uma cadeia de ligação Dos Centros de Eventos](event-hubs-get-connection-string.md). Aqui está uma configuração de exemplo: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-producer-from-the-command-line"></a>Executar produtor a partir da linha de comando

Para executar o produtor a partir da linha de comando, gere o JAR e, em seguida, corram a partir de Maven (ou gerem o JAR usando o Maven, e depois corram em Java adicionando os jar(s) necessários para o caminho de classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

O produtor começa a enviar eventos para o centro de eventos no tema `test` , e imprime os eventos para stdout.

## <a name="run-akka-streams-consumer"></a>Executar consumidor de Akka Streams

Utilizando o exemplo do consumidor fornecido, receba mensagens do centro de eventos.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto final de Eventos Hubs Kafka

#### <a name="consumer-applicationconf"></a>Aplicação ao consumidor.conf

Atualize os `bootstrap.servers` valores e `sasl.jaas.config` valores `consumer/src/main/resources/application.conf` para direcionar o consumidor para o ponto final do Event Hubs Kafka com a autenticação correta.

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

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}`Substitua-a pela cadeia de ligação para o seu espaço de nomes 'Centros de Eventos'. Para obter instruções sobre a obtenção da cadeia de ligação, consulte [obter uma cadeia de ligação Dos Centros de Eventos](event-hubs-get-connection-string.md). Aqui está uma configuração de exemplo: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-consumer-from-the-command-line"></a>Executar o consumidor a partir da linha de comando

Para executar o consumidor a partir da linha de comando, gere o JAR e, em seguida, corram a partir de Maven (ou gerem o JAR usando o Maven, e depois corram em Java adicionando os jar(s) necessários para o caminho de classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Se o centro de eventos tiver eventos (por exemplo, se o seu produtor também estiver em execução), então o consumidor começa a receber eventos do `test` tópico. 

Consulte o [Guia kafka Akka Streams](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) para obter informações mais detalhadas sobre a Akka Streams.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os Centros de Eventos para Kafka, consulte os seguintes artigos:  

- [Espelhar um mediador de Kafka num hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ligar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Ligar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integre kafka Connect com um centro de eventos](event-hubs-kafka-connect-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)
- [Guia de desenvolvimento apache Kafka para hubs de eventos Azure](apache-kafka-developer-guide.md)