---
title: Usando fluxos de Akka para Apache Kafka-hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como conectar fluxos do Akka a um hub de eventos do Azure habilitado Apache Kafka.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: ba81ce88bcdf039d020dcd945e45a11cf603c114
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555751"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Usando fluxos de Akka com hubs de eventos para Apache Kafka
Este tutorial mostra como conectar fluxos de Akka a hubs de eventos habilitados para Kafka sem alterar seus clientes de protocolo ou executar seus próprios clusters. Os hubs de eventos do Azure para o Kafka dão suporte à [versão 1,0 do Apache Kafka.](https://kafka.apache.org/10/documentation.html)

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Executar o produtor do Akka streams 
> * Executar consumidor de fluxos de Akka

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem os seguintes pré-requisitos:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Baixar](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário Maven
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Um namespace de hubs de eventos é necessário para enviar ou receber de qualquer serviço de hubs de eventos. Consulte [criar hubs de eventos habilitados para Kafka](event-hubs-create-kafka-enabled.md) para obter informações sobre como obter um ponto de extremidade Kafka dos hubs de eventos. Certifique-se de copiar a cadeia de conexão dos hubs de eventos para uso posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que você tem uma cadeia de conexão de hubs de eventos habilitada para Kafka, clone os hubs de eventos do Azure para o repositório Kafka e navegue até a subpasta `akka`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Executar o produtor do Akka streams

Usando o exemplo de produtor do Akka streams fornecido, envie mensagens para o serviço de hubs de eventos.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto de extremidade Kafka dos hubs de eventos

#### <a name="producer-applicationconf"></a>Application. conf do produtor

Atualize os valores de `bootstrap.servers` e `sasl.jaas.config` no `producer/src/main/resources/application.conf` para direcionar o produtor para o ponto de extremidade Kafka dos hubs de eventos com a autenticação correta.

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

### <a name="run-producer-from-the-command-line"></a>Executar o Producer na linha de comando

Para executar o produtor da linha de comando, gere o JAR e, em seguida, execute de dentro do Maven (ou gere o JAR usando o Maven, em seguida, execute em Java adicionando os Kafka JAR necessários ao classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

O produtor começa a enviar eventos para o Hub de eventos habilitado para Kafka no tópico `test` e imprime os eventos em stdout.

## <a name="run-akka-streams-consumer"></a>Executar consumidor de fluxos de Akka

Usando o exemplo de consumidor fornecido, receba mensagens dos hubs de eventos habilitados para Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto de extremidade Kafka dos hubs de eventos

#### <a name="consumer-applicationconf"></a>Application. conf do consumidor

Atualize os valores de `bootstrap.servers` e `sasl.jaas.config` no `consumer/src/main/resources/application.conf` para direcionar o consumidor para o ponto de extremidade Kafka dos hubs de eventos com a autenticação correta.

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

### <a name="run-consumer-from-the-command-line"></a>Executar o consumidor na linha de comando

Para executar o consumidor a partir da linha de comando, gere o JAR e, em seguida, execute de dentro do Maven (ou gere o JAR usando o Maven, em seguida, execute em Java adicionando os Kafka JAR necessários ao classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Se o Hub de eventos habilitado para Kafka tiver eventos (por exemplo, se o seu produtor também estiver em execução), o consumidor começará a receber eventos do tópico `test`. 

Confira o [guia Akka streams Kafka](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) para obter informações mais detalhadas sobre fluxos de Akka.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você aprendeu a conectar fluxos de Akka a hubs de eventos habilitados para Kafka sem alterar seus clientes de protocolo ou executar seus próprios clusters. Os hubs de eventos do Azure para o Kafka dão suporte à [versão 1,0 do Apache Kafka.](https://kafka.apache.org/10/documentation.html). Você fez as seguintes ações como parte deste tutorial: 

> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Clonar o projeto de exemplo
> * Executar o produtor do Akka streams 
> * Executar consumidor de fluxos de Akka

Para saber mais sobre os Hubs de Eventos e os Hubs de Eventos para Kafka, veja os tópicos seguintes:  

- [Saiba mais sobre Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) (Hubs de Eventos para Apache Kafka)
- [How to create Kafka enabled Event Hubs](event-hubs-create-kafka-enabled.md) (Como criar os Hubs de Eventos ativados para Kafka)
- [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Mirror a Kafka broker in a Kafka-enabled event hub](event-hubs-kafka-mirror-maker-tutorial.md) (Espelhar um mediador de Kafka num hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Ligar o Apache Spark a um hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-flink-tutorial.md) (Ligar o Apache Flink a um hub de eventos com Kafka ativado)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md) (Integrar o Kafka Connect num hub de eventos com Kafka ativado)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)
