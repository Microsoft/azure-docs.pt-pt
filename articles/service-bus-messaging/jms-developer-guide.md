---
title: Guia de desenvolvimento do Azure Service Bus JMS 2.0
description: Como utilizar o Java Message Service (JMS) 2.0 API para comunicar com o Azure Service Bus
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 492da46a5ce4f5955a72972d91a8c56d54c385bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726959"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Guia de desenvolvimento do Azure Service Bus JMS 2.0

Este guia contém informações detalhadas para ajudá-lo a comunicar com o Azure Service Bus utilizando o Java Message Service (JMS) 2.0 API.

Como desenvolvedor da Java, se é novo na Azure Service Bus, considere ler os artigos abaixo.

| Introdução | Conceitos |
|----------------|-------|
| <ul> <li> [O que é O Autocarro de Serviço Azure](service-bus-messaging-overview.md) </li> <li> [Filas, Tópicos e Assinaturas](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Autocarro de serviço Azure - nível premium](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>Modelo de programação do Serviço de Mensagens Java (JMS)

O modelo de programação da API do Serviço de Mensagens de Java é o indicado abaixo - 

> [!NOTE]
>
>**O nível Azure Service Bus Premium** suporta JMS 1.1 e JMS 2.0.
> <br> <br>
> **Azure Service Bus - Standard** tier suporta funcionalidade limitada JMS 1.1. Para mais detalhes, consulte esta [documentação.](service-bus-java-how-to-use-jms-api-amqp.md)
>

# <a name="jms-20-programming-model"></a>[Modelo de programação JMS 2.0](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png "alt-text="Diagrama mostrando o modelo de programação JMS 2.0." border="false":::

# <a name="jms-11-programming-model"></a>[Modelo de programação JMS 1.1](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png "alt-text="Diagrama mostrando o modelo de programação JMS 1.1." border="false":::

---

## <a name="jms---building-blocks"></a>JMS - Blocos de construção

Os blocos de construção abaixo estão disponíveis para comunicar com a aplicação JMS.

> [!NOTE]
> O guia abaixo foi adaptado a partir do [Tutorial Oracle Java EE 6 para o Serviço de Mensagens Java (JMS)](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
>
> Recomenda-se referir a este tutorial para uma melhor compreensão do Serviço de Mensagens Java (JMS).
>

### <a name="connection-factory"></a>Fábrica de conexão
O objeto da fábrica de ligação é utilizado pelo cliente para se conectar com o fornecedor JMS. A fábrica de ligação encapsula um conjunto de parâmetros de configuração de ligação definidos pelo administrador.

Cada fábrica de ligação é um exemplo `ConnectionFactory` de, `QueueConnectionFactory` ou `TopicConnectionFactory` interface.

Para simplificar a ligação com a Azure Service Bus, estas interfaces são implementadas através `ServiceBusJmsConnectionFactory` de , `ServiceBusJmsQueueConnectionFactory` e `ServiceBusJmsTopicConnectionFactory` respectivamente. A fábrica de ligação pode ser instantânea com os parâmetros abaixo - 
   * Cadeia de ligação - a cadeia de ligação para o espaço de nome do nível Azure Service Bus Premium.
   * ServiceBusJmsConnectionFactorySettings saco de propriedade que contém
      * connectionIdleTimeoutMS - tempo limite de ligação inativo em milissegundos.
      * traceFrames - bandeira booleana para recolher traços AMQP para depurar.
      * *outros parâmetros de configuração*

A fábrica pode ser criada como abaixo. A cadeia de ligação é um parâmetro necessário, mas as propriedades adicionais são opcionais.

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> As aplicações Java que utilizam a JMS 2.0 API devem ligar-se ao Azure Service Bus utilizando apenas a cadeia de ligação. Atualmente, a autenticação para clientes JMS só é suportada utilizando a cadeia De ligação.
>
> A autenticação apoiada pelo diretório ativo Azure (AAD) não é suportada atualmente.
>

### <a name="jms-destination"></a>Destino JMS

Um destino é o objeto que um cliente usa para especificar o alvo das mensagens que produz e a fonte das mensagens que consome.

Os destinos mapeiam para entidades no Azure Service Bus - filas (em cenários ponto a ponto) e tópicos (em pub-sub cenários).

### <a name="connections"></a>Ligações

Uma ligação encapsula uma ligação virtual com um fornecedor JMS. Com a Azure Service Bus, esta representa uma ligação imponente entre a aplicação e a Azure Service Bus sobre a AMQP.

Uma ligação é criada a partir da fábrica de ligação, como mostrado abaixo.

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>Sessões

Uma sessão é um contexto de linha única para produzir e consumir mensagens. Pode ser utilizado para criar mensagens, produtores de mensagens e consumidores, mas também fornece um contexto transacional para permitir o agrupamento de envios e receções numa unidade atómica de trabalho.

Uma sessão pode ser criada a partir do objeto de ligação, como mostrado abaixo.

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>Modos de sessão

Uma sessão pode ser criada com qualquer um dos modos abaixo.

| Modos de sessão | Comportamento |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | A sessão reconhece automaticamente a receção de uma mensagem por parte de um cliente, quer quando a sessão tiver regressado com sucesso de uma chamada para receber, quer quando o ouvinte da mensagem a sessão ligou para processar a mensagem com sucesso.|
|**Session.CLIENT_ACKNOWLEDGE** |O cliente reconhece uma mensagem consumida chamando o método de reconhecimento da mensagem.|
|**Session.DUPS_OK_ACKNOWLEDGE**|Este modo de reconhecimento instrui a sessão a reconhecer preguiçosamente a entrega de mensagens.| 
|**Session.SESSION_TRANSACTED**|Este valor pode ser passado como argumento para o método criarSession (int sessionMode) no objeto De Ligação para especificar que a sessão deve usar uma transação local.| 

Quando o modo de sessão não é especificado, o **Session.AUTO_ACKNOWLEDGE** é escolhido por defeito.

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext é definido como parte da especificação JMS 2.0.
>

O JMSContext combina a funcionalidade fornecida pela ligação e objeto de sessão. Pode ser criado a partir do objeto da fábrica de ligação.

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>Modos JMSContext

Tal como o objeto **Session,** o JMSContext pode ser criado com os mesmos modos de reconhecimento mencionados nos [modos Session](#session-modes).

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

Quando o modo não é especificado, o **JMSContext.AUTO_ACKNOWLEDGE** é escolhido por defeito.

### <a name="jms-message-producers"></a>Produtores de mensagens JMS

Um produtor de mensagens é um objeto que é criado usando um JMSContext ou uma Sessão e usado para enviar mensagens para um destino.

Pode ser criado como um objeto autónomo como abaixo - 

```java
JMSProducer producer = context.createProducer();
```

ou criado em tempo de execução quando uma mensagem é necessária para ser enviada.

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>Consumidores de mensagens JMS

Um consumidor de mensagens é um objeto que é criado por um JMSContext ou uma Sessão e usado para receber mensagens enviadas para um destino. Pode ser criado como mostrado abaixo -

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>Synchronous recebe através do método de receção()

O consumidor de mensagens fornece uma forma sincronizada de receber mensagens do destino através do `receive()` método.

Se não for especificado nenhum argumento/timeout ou se for especificado um tempo limite de '0', então o consumidor bloqueia indefinidamente, a menos que a mensagem chegue, ou a ligação esteja quebrada (o que for anterior).

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

Quando é apresentado um argumento positivo não zero, o consumidor bloqueia até que o temporizador expire.

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>Assíncronos recebe com ouvintes de mensagens JMS

Um ouvinte de mensagens é um objeto que é usado para o manuseamento assíncronos de mensagens num destino. Implementa a `MessageListener` interface que contém o método em que `onMessage` a lógica de negócio específica deve viver.

Um objeto de ouvinte de mensagem deve ser instantâneo e registado contra um consumidor de mensagens específicas utilizando o `setMessageListener` método.

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>Consumir de tópicos

[JMS Message Os consumidores](#jms-message-consumers) são criados contra um [destino](#jms-destination) que pode ser uma fila ou um tópico.

Os consumidores em filas são simplesmente objetos laterais do cliente que vivem no contexto da Sessão (e Ligação) entre a aplicação do cliente e a Azure Service Bus.

Os consumidores sobre temas, no entanto, têm 2 partes - 
   * Um **objeto do lado do cliente** que vive no contexto da Sessão (ou JMSContext), e,
   * Uma **subscrição** que é uma entidade na Azure Service Bus.

As subscrições são documentadas [aqui](java-message-service-20-entities.md#java-message-service-jms-subscriptions) e podem ser uma das abaixo - 
   * Assinaturas duradouras partilhadas
   * Assinaturas partilhadas não duráveis
   * Assinaturas duradouras não partilhadas
   * Assinaturas não partilhadas não duráveis

### <a name="jms-queue-browsers"></a>Navegadores de fila JMS

A API JMS fornece um `QueueBrowser` objeto que permite à aplicação navegar nas mensagens na fila e exibir os valores do cabeçalho para cada mensagem.

Um Navegador de Fila pode ser criado usando o JMSContext como abaixo.

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> A JMS API não fornece uma API para navegar num tópico.
>
> Isto porque o tema em si não armazena as mensagens. Assim que a mensagem é enviada para o tópico, é reencaminhada para as subscrições apropriadas.
>

### <a name="jms-message-selectors"></a>Seletores de mensagens JMS

Os seletores de mensagens podem ser utilizados recebendo aplicações para filtrar as mensagens que são recebidas. Com os seletores de mensagens, a aplicação recetora descarrega o trabalho de filtragem de mensagens para o fornecedor JMS (neste caso, Azure Service Bus) em vez de assumir essa responsabilidade em si.

Os selecionadores podem ser utilizados ao criar qualquer um dos consumidores abaixo - 
   * Assinatura duradoura partilhada
   * Assinatura duradoura não partilhada
   * Assinatura partilhada não durável
   * Assinatura não partilhada não durável
   * Navegador de fila


## <a name="summary"></a>Resumo

Este guia de desenvolvedores mostrou como as aplicações do cliente Java usando o Java Message Service (JMS) podem ligar-se com o Azure Service Bus.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Azure Service Bus e detalhes sobre as entidades do Java Message Service (JMS), confira os links abaixo - 
* [Autocarro de serviço - Filas, Tópicos e Assinaturas](service-bus-queues-topics-subscriptions.md)
* [Service Bus - Java Message Service entities](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Suporte AMQP 1.0 em Azure Service Bus](service-bus-amqp-overview.md)
* [Guia do desenvolvedor do ônibus de serviço AMQP 1.0](service-bus-amqp-dotnet.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [API do Serviço de Mensagens java (doc externo da Oracle)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Saiba como migrar de ActiveMQ para Service Bus](migrate-jms-activemq-to-servicebus.md)
