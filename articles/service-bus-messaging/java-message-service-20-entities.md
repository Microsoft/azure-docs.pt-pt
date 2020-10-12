---
title: Mensagens de autocarro da Azure Service - Entidades de serviço de mensagens Java (pré-visualização)
description: Este artigo fornece uma visão geral das entidades de mensagens Azure Service Bus acessíveis através do serviço de mensagens Java API.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 1a7fe3d6355146ccf0fce50266a6f3b8da5231b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87801803"
---
# <a name="java-message-service-jms-20-entities-preview"></a>Serviço de mensagens Java (JMS) 2.0 entidades (Pré-visualização)

As aplicações do cliente que ligam ao Azure Service Bus Premium e que utilizam a [biblioteca JMS do Azure Service Bus](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) podem alavancar as entidades abaixo.

## <a name="queues"></a>Filas

As filas em JMS são semânticas comparáveis com as [tradicionais filas de autocarros](service-bus-queues-topics-subscriptions.md#queues)de serviço.

Para criar uma fila, utilize os métodos abaixo na `JMSContext` classe -

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Tópicos

Os tópicos em JMS são semânicamente comparáveis com os tópicos tradicionais [do Service Bus.](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)

Para criar um tópico, utilize os métodos abaixo na `JMSContext` classe -

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Filas temporárias

Quando uma aplicação de cliente requer uma entidade temporária que exista para o tempo de vida da aplicação, pode utilizar filas temporárias. Estes são utilizados no padrão [de Resposta de Pedido.](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)

Para criar uma fila temporária, utilize os métodos abaixo na `JMSContext` classe -

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Tópicos temporários

Tal como as Filas Temporárias, existem Tópicos Temporários para permitir a publicação/subscrição através de uma entidade temporária que existe para o tempo de vida da aplicação.

Para criar um tópico temporário, utilize os métodos abaixo na `JMSContext` classe -

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Assinaturas do serviço de mensagens Java (JMS)

Embora estas sejam semânticas semelhantes às [Subscrições](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (ou seja, existem sobre um tópico e permitem publicar/subscrever semântica), a especificação java Message Service introduz os conceitos de atributos **Partilhados,** **Não-Partilhados,** **Duráveis** e **Não duráveis** para uma determinada subscrição.

> [!NOTE]
> As subscrições abaixo estão disponíveis no nível Azure Service Bus Premium para pré-visualização para aplicações de clientes que se ligam ao Azure Service Bus utilizando a [biblioteca JMS do Autocarro de Serviço Azure.](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)
>
> Para a pré-visualização do Público, estas subscrições não podem ser criadas utilizando o portal Azure.
>

### <a name="shared-durable-subscriptions"></a>Assinaturas duradouras partilhadas

Uma subscrição duradoura partilhada é utilizada quando todas as mensagens publicadas sobre um tópico devem ser recebidas e processadas por uma aplicação, independentemente de a aplicação estar a consumir ativamente a partir da subscrição em todos os momentos.

Uma vez que esta é uma subscrição partilhada, qualquer aplicação que seja autenticada para receber da Service Bus pode receber da subscrição.

Para criar uma subscrição duradoura partilhada, utilize os métodos abaixo na `JMSContext` classe -

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

A subscrição duradoura partilhada continua a existir a menos que seja eliminada utilizando o `unsubscribe` método na `JMSContext` classe.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Assinaturas duradouras não partilhadas

Tal como uma subscrição duradoura partilhada, uma subscrição duradoura não partilhada é utilizada quando todas as mensagens publicadas sobre um tópico devem ser recebidas e processadas por uma aplicação, independentemente de a aplicação estar a consumir ativamente a partir da subscrição em todos os momentos.

No entanto, uma vez que se trata de uma subscrição não partilhada, apenas a aplicação que criou a subscrição pode receber da sua parte.

Para criar uma subscrição duradoura não partilhada, utilize os métodos abaixo da `JMSContext` classe - 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> A `noLocal` funcionalidade não é suportada e ignorada.
>

A subscrição duradoura não partilhada continua a existir a menos que seja eliminada utilizando o `unsubscribe` método da `JMSContext` classe.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Assinaturas partilhadas não duráveis

Uma subscrição partilhada não durável é utilizada quando várias aplicações de clientes precisam de receber e processar mensagens de uma única subscrição, apenas até que estejam ativamente a consumir/receber dela.

Uma vez que a subscrição não é duradoura, não é persistido. As mensagens não são recebidas por esta subscrição quando não há consumidores ativos nela.

Para criar uma subscrição partilhada não durável, crie um `JmsConsumer` como mostrado nos métodos abaixo da `JMSContext` classe -

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

A subscrição partilhada não durável continua a existir até que haja consumidores ativos a receber dela.

### <a name="unshared-non-durable-subscriptions"></a>Assinaturas não partilhadas não duráveis

Uma subscrição não partilhada não durável é utilizada quando a aplicação do cliente precisa de receber e processar a mensagem a partir de uma subscrição, apenas até que esteja a consumir ativamente a partir da sua parte. Apenas um consumidor pode existir nesta subscrição, ou seja, o cliente que criou a subscrição.

Uma vez que a subscrição não é duradoura, não é persistido. As mensagens não são recebidas por esta subscrição quando não há um consumidor ativo nela.

Para criar uma subscrição não-duradoura, crie uma `JMSConsumer` como mostrado nos métodos abaixo da `JMSContext` classe -

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> A `noLocal` funcionalidade não é suportada e ignorada.
>

A subscrição não-duradoura não-duradoura continua a existir até que haja um consumidor ativo a receber dela.

### <a name="message-selectors"></a>Seletores de mensagens

Tal como **existem Filtros e Ações** para subscrições regulares de Service Bus, existem **Seletores de Mensagens** para subscrições JMS.

Os seletores de mensagens podem ser configurados em cada uma das subscrições JMS e existem como uma condição de filtro nas propriedades do cabeçalho da mensagem. Apenas são entregues mensagens com propriedades de cabeçalho correspondentes à expressão seletora de mensagens. Um valor de nulo ou uma corda vazia indica que não existe um seletor de mensagens para a Assinatura/Consumidor JMS.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações e exemplos de utilização de mensagens Service Bus, consulte os seguintes tópicos avançados:

* [Descrição geral de mensagens do Service Bus](service-bus-messaging-overview.md)
* [Use o Java Message Service 2.0 API com Azure Service Bus Premium (Preview)](how-to-use-java-message-service-20.md)



