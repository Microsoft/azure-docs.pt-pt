---
title: Use o Java Message Service 2.0 API com Azure Service Bus Premium
description: Como utilizar o Serviço de Mensagens Java (JMS) com o Azure Service Bus
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8363011187a4c2ef77681ece4bb8b1de73ec7a63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87801648"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium-preview"></a>Use o Java Message Service 2.0 API com Azure Service Bus Premium (Preview)

Este artigo explica como usar o popular **Java Message Service (JMS) 2.0** API para interagir com o Azure Service Bus sobre o protocolo de fila de mensagens avançadas (AMQP 1.0).

> [!NOTE]
> Suporte para Java Message Service (JMS) 2.0 API só está disponível no **nível Azure Service Bus Premium** e está atualmente em **pré-visualização**.
>

## <a name="get-started-with-service-bus"></a>Introdução ao Service Bus

Este guia pressupõe que já tem um espaço de nomes de Service Bus. Se não o fizer, poderá [criar o espaço de nome e a fila](service-bus-create-namespace-portal.md) utilizando o portal [Azure](https://portal.azure.com). 

Para obter mais informações sobre como criar espaços e filas de nomes de Service Bus, consulte [Começar com as filas do Service Bus através do portal Azure.](service-bus-quickstart-portal.md)

## <a name="what-jms-features-are-supported"></a>Quais as funcionalidades JMS suportadas?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>Descarregar a biblioteca de clientes do Java Message Service (JMS)

Para utilizar todas as funcionalidades disponíveis no nível Azure Service Bus Premium, a biblioteca abaixo deve ser adicionada ao caminho de construção do projeto.

[Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> Para adicionar os [Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) ao caminho de construção, utilize a ferramenta de gestão de dependência preferida para o seu projeto como [Maven](https://maven.apache.org/) ou [Gradle](https://gradle.org/).
>

## <a name="coding-java-applications"></a>Aplicações de codificação java

Uma vez importadas as dependências, as aplicações java podem ser escritas de forma agnóstica do provedor JMS.

### <a name="connecting-to-azure-service-bus-using-jms"></a>Ligação ao autocarro de serviço da Azure usando o JMS

Para se conectar com o Azure Service Bus utilizando clientes JMS, precisa da cadeia de **ligação** que está disponível nas "Políticas de Acesso Partilhado" no [portal Azure](https://portal.azure.com) em **cadeia de ligação primária.**

1. Instantaneamente o `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Instantaneamente o `ServiceBusJmsConnectionFactory` com o apropriado `ServiceBusConnectionString` .

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Use o `ConnectionFactory` para criar um `Connection` e, em seguida, um `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    ou um `JMSContext` (para clientes JMS 2.0)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

### <a name="write-the-jms-application"></a>Escreva a aplicação JMS

Uma vez que o `Session` ou `JMSContext` tenha sido instantâneo, a sua aplicação pode usar as APIs JMS familiares para executar tanto operações de gestão como de dados.

Consulte a lista de [funcionalidades JMS suportadas](how-to-use-java-message-service-20.md#what-jms-features-are-supported) para ver quais AS APIs são suportadas como parte desta pré-visualização.

Abaixo estão alguns snippets de código de amostra para começar com JMS -

#### <a name="sending-messages-to-a-queue-and-topic"></a>Envio de mensagens para uma fila e tópico

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>Receber mensagens de uma fila

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>Receber mensagens de uma subscrição duradoura partilhada sobre um tópico

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>Resumo

Este guia mostrou como as aplicações do cliente Java usando o Java Message Service (JMS) em amQP 1.0 podem interagir com o Azure Service Bus.

Também pode utilizar o Service Bus AMQP 1.0 de outras línguas, incluindo .NET, C, Python e PHP. Os componentes construídos utilizando estes diferentes idiomas podem trocar mensagens de forma fiável e plena fidelidade utilizando o suporte AMQP 1.0 em Service Bus.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Azure Service Bus e detalhes sobre as entidades do Java Message Service (JMS), confira os links abaixo - 
* [Autocarro de serviço - Filas, Tópicos e Assinaturas](service-bus-queues-topics-subscriptions.md)
* [Service Bus - Java Message Service entities](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Suporte AMQP 1.0 em Azure Service Bus](service-bus-amqp-overview.md)
* [Guia do desenvolvedor do ônibus de serviço AMQP 1.0](service-bus-amqp-dotnet.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [API do Serviço de Mensagens java (doc externo da Oracle)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Saiba como migrar de ActiveMQ para Service Bus](migrate-jms-activemq-to-servicebus.md)
