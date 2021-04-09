---
title: Mensagens de autocarro da Azure Service - Entidades de serviço de mensagens Java
description: Este artigo fornece uma visão geral das entidades de mensagens Azure Service Bus acessíveis através do serviço de mensagens Java API.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ee4e0124dced16b86d5292c647e129aa87645f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652586"
---
# <a name="java-message-service-jms-20-entities"></a>Serviço de mensagens Java (JMS) 2.0 entidades

As aplicações do cliente que se ligam ao Azure Service Bus Premium e que utilizam a [biblioteca JMS do Azure Service Bus](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) podem utilizar as entidades abaixo.

## <a name="queues"></a>Filas

As filas em JMS são semânticas comparáveis com as [tradicionais filas de autocarros](service-bus-queues-topics-subscriptions.md#queues)de serviço.

Para criar uma fila, use os métodos abaixo na `JMSContext` classe -

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Tópicos

Os tópicos em JMS são semânicamente comparáveis com os tópicos tradicionais [do Service Bus.](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)

Para criar um tópico, use os métodos abaixo na `JMSContext` classe -

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Filas temporárias

Se uma aplicação de cliente requer uma entidade temporária que exista para o tempo de vida da aplicação, pode utilizar filas temporárias. Estas entidades são utilizadas no padrão [de Resposta a Pedidos.](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)

Para criar uma fila temporária, use os métodos abaixo na `JMSContext` classe -

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Tópicos temporários

Tal como as Filas Temporárias, existem Tópicos Temporários para permitir a publicação/subscrição através de uma entidade temporária que existe para o tempo de vida da aplicação.

Para criar um tópico temporário, use os métodos abaixo na `JMSContext` classe -

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Assinaturas do serviço de mensagens Java (JMS)

Embora estas sejam semanticamente semelhantes às [Subscrições](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (isto é, existem sobre um tópico e permitem publicar/subscrever semântica), a especificação do Serviço de Mensagens Java introduz os conceitos de Atributos **Partilhados,** **Não-Partilhados,****Duráveis e **Não duráveis** para uma determinada subscrição.

> [!NOTE]
> As subscrições abaixo estão disponíveis no nível Azure Service Bus Premium para aplicações de clientes que ligam a Azure Service Bus utilizando a [biblioteca JMS do Azure Service Bus](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Apenas subscrições duráveis podem ser criadas usando o portal Azure.
>

### <a name="shared-durable-subscriptions"></a>Assinaturas duradouras partilhadas

Uma subscrição duradoura partilhada é utilizada quando todas as mensagens publicadas sobre um tópico devem ser recebidas e processadas por uma aplicação, independentemente de a aplicação estar a consumir ativamente a partir da subscrição em todos os momentos.

Qualquer aplicação autenticada para receber da Service Bus pode receber da subscrição duradoura partilhada.

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

Tal como uma subscrição duradoura partilhada, uma subscrição duradoura não partilhada é utilizada quando todas as mensagens publicadas sobre um tópico devem ser recebidas e processadas por uma aplicação, independentemente de a aplicação estar a consumir ativamente a partir da subscrição.

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

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Conceitos adicionais para subscrições do Java Message Service (JMS) 2.0

### <a name="client-scoping"></a>Scoping de cliente

As subscrições, conforme especificado no Serviço de Mensagens Java (JMS) 2.0 API, podem ou não ser *aparatosas para aplicações específicas do cliente (identificadas* com o `clientId` adequado).

Uma vez que a subscrição é scopeda, **só pode ser acedida** a partir de aplicações de clientes que tenham o mesmo id do cliente. 

Qualquer tentativa de acesso a uma subscrição a um ID de cliente específico (digamos clienteId1) a partir de uma aplicação com outro ID do cliente (digamos clientId2) levará à criação de outra subscrição angusada para o outro ID do cliente (clientId2).

> [!NOTE]
> A identificação do cliente pode ser nula ou vazia, mas deve corresponder ao ID do cliente definido na aplicação do cliente JMS. Do ponto de vista da Azure Service Bus, uma identificação de cliente nula e um id de cliente vazio têm o mesmo comportamento.
>
> Se a identificação do cliente estiver definida para nula ou vazia, só é acessível a aplicações de clientes cujo ID do cliente também esteja definido para nulo ou vazio.
>

### <a name="shareability"></a>Partilha

As subscrições **partilhadas** permitem que vários clientes/consumidores (isto é, objetos JMSConsumer) recebam mensagens dos mesmos.

>[!NOTE]
> As subscrições partilhadas a um ID de cliente específico ainda podem ser acedidas por vários clientes/consumidores (ou seja, objetos JMSConsumer), mas cada uma das aplicações do cliente deve ter o mesmo ID do cliente.
>
 

As assinaturas **não partilhadas** permitem que apenas um único cliente/consumidor (isto é, objeto JMSConsumer) receba mensagens deles. Se um `JMSConsumer` for criado numa subscrição não partilhada enquanto já tem uma escuta ativa `JMSConsumer` a ouvir mensagens, `JMSException` a é lançada.


### <a name="durability"></a>Durabilidade

As subscrições **duradouras** são persistiu e continuam a recolher mensagens do tema, independentemente de uma aplicação ( `JMSConsumer` ) estar a consumir mensagens da sua parte.

As subscrições **não duradouras** não são persistiu e recolhem mensagens do tópico desde que uma aplicação `JMSConsumer` () esteja a consumir mensagens a partir do mesmo. 

## <a name="representation-of-client-scoped-subscriptions"></a>Representação de subscrições de âmbito de cliente

Dado que as subscrições do cliente (JMS) devem coexistir com as [subscrições existentes,](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)a forma como as assinaturas do cliente (JMS) são representadas seguem o formato abaixo.

   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**D** (para assinaturas duradouras)
   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**ND** (para assinaturas não duráveis)

Aqui **$** está o delimiter.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações e exemplos de utilização de mensagens Service Bus, consulte os seguintes tópicos avançados:

* [Descrição geral de mensagens do Service Bus](service-bus-messaging-overview.md)
* [Use o Java Message Service 2.0 API com Azure Service Bus Premium](how-to-use-java-message-service-20.md)



