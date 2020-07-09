---
title: Use AMQP com Java Message Service API & ônibus de serviço Azure
description: Como utilizar o Serviço de Mensagens Java (JMS) com o Azure Service Bus e o Advanced Message Queing Protocol (AMQP) 1.0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119162"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Utilize o Serviço de Mensagens Java (JMS) com ônibus de serviço Azure e AMQP 1.0

O protocolo de suporte ao **Protocolo avançado de Fila de Mensagens (AMQP) 1.0** no Azure Service Bus significa que pode utilizar as funcionalidades de mensagens intermediadas em filas e publicar/subscrever funcionalidades de mensagens intermediadas a partir de uma série de plataformas utilizando um protocolo binário eficiente. Além disso, pode construir aplicações compostas por componentes construídos utilizando uma mistura de idiomas, quadros e sistemas operativos.

Este artigo explica como usar funcionalidades de mensagens Azure Service Bus (filas e publicar/subscrever tópicos) de aplicações Java usando a popular API do **Java Message Service (JMS)** sobre o protocolo AMQP.

## <a name="get-started-with-service-bus"></a>Introdução ao Service Bus
Este guia pressupõe que já tem um espaço de nomes de Service Bus contendo uma fila chamada `basicqueue` . Se não o fizer, poderá [criar o espaço de nome e a fila](service-bus-create-namespace-portal.md) utilizando o portal [Azure](https://portal.azure.com). Para obter mais informações sobre como criar espaços e filas de nomes de Service Bus, consulte [Começar com as filas do Service Bus.](service-bus-dotnet-get-started-with-queues.md)

> [!NOTE]
> Filas e tópicos divididos também apoiam AMQP. Para obter mais informações, consulte [as entidades de mensagens Partitioned](service-bus-partitioning.md) e [o suporte amQP 1.0 para filas e tópicos divididos do Service Bus.](service-bus-partitioned-queues-and-topics-amqp-overview.md)
> 
>

## <a name="what-jms-features-are-supported"></a>Quais as funcionalidades JMS suportadas?

Aqui estão as funcionalidades JMS que são suportadas no Azure Service Bus.

| Funcionalidades | Azure Service Bus Standard tier - JMS 1.1 | Azure Service Bus Premium tier - JMS 2.0 (Pré-visualização) |
|---|---|---|
| Criação automática de entidades sobre AMQP | Não suportado | **Suportado** |
| Filas | **Suportado** | **Suportado** |
| Tópicos | **Suportado** | **Suportado** |
| Filas Temporárias | Não suportado <br/> (Criar uma fila regular com o conjunto *AutoDeleteOnIdle)* | **Suportado** |
| Tópicos Temporários | Não suportado | **Suportado** |
| Seletores de mensagens | Não suportado | **Suportado** |
| Navegadores de fila | Não suportado <br/> (Utilize a funcionalidade *Peek* da API do Autocarro de Serviço) | **Suportado** |
| Assinaturas Duradouras Partilhadas | **Suportado** | **Suportado**|
| Assinaturas duradouras não partilhadas | Não suportado | **Suportado** |
| Assinaturas partilhadas não duráveis | Não suportado | **Suportado** |
| Assinaturas não partilhadas não duráveis | Não suportado | **Suportado** |
| Cancelar subscrições duradouras | Não suportado | **Suportado** |
| ReceberNoWait | Não suportado | **Suportado** |
| Transações Distribuídas | Não suportado | Não suportado |
| Terminus durável | Não suportado | Não suportado |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>Ressalvas adicionais para o nível Standard de ônibus de serviço
Apenas é permitido um **MensagemProdutor** ou **MessageConsumer** por **Sessão**. Se necessitar de criar **vários MessageProducers** ou **MessageConsumers** numa aplicação, crie uma **Sessão** dedicada para cada um deles.

## <a name="downloading-the-java-message-service-jms-client-library"></a>Descarregar a biblioteca de clientes do Java Message Service (JMS)

Para se conectar com o Azure Service Bus e aproveitar a API do Java Message Service (JMS) em vez de AMQP, as bibliotecas abaixo precisam de ser alavancadas. Estes devem ser adicionados ao caminho de construção utilizando a ferramenta de gestão de dependência preferida para o seu projeto.

A biblioteca de clientes necessária depende do nível de preços utilizado.

### <a name="premium-tier---jms-20-over-amqp-preview"></a>Nível premium - JMS 2.0 sobre AMQP (Pré-visualização)

Para utilizar todas as funcionalidades de pré-visualização disponíveis no nível Azure Service Bus Premium utilize a biblioteca [Azure-servicebus-jms.](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

### <a name="standard-tier---jms-11-over-amqp"></a>Nível padrão - JMS 1.1 sobre AMQP

Para utilizar as funcionalidades JMS suportadas pelo service bus standard (ver [quais as funcionalidades JMS são suportadas?](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)) utilize as bibliotecas abaixo -

* [Espectro Geronimo JMS 1.1](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Cliente Qpid JMS](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> Os nomes e versões JMS JAR podem ter mudado. Para mais detalhes, consulte [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).
>

## <a name="coding-java-applications"></a>Aplicações de codificação java

Uma vez importadas as dependências, as aplicações java podem ser escritas de forma agnóstica do provedor JMS.

Uma vez que a Azure Service Bus Standard e Premium diferem nas dependências e no número de funcionalidades JMS que suportam, o modelo de programação é ligeiramente diferente para os dois.

> [!IMPORTANT]
> O guia abaixo mostra como ligar-se ao Azure Service Bus, dada uma aplicação simples.
>
> Dado que a maioria das arquiteturas de aplicações empresariais podem ter uma forma personalizada de gerir dependências e configurações, use o abaixo como um guia para entender o que é necessário e adaptar-se à sua aplicação adequadamente.
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>Ligação ao autocarro de serviço da Azure usando o JMS

Para se conectar com o Azure Service Bus utilizando clientes JMS, precisa do **ConnectionString** que está disponível nas "Políticas de Acesso Partilhado" no [portal Azure](https://portal.azure.com) em **cadeia de ligação primária.**


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>Ligar ao Azure Service Bus Premium sobre JMS 2.0 (Pré-visualização)

1. Instantaneamente o`ServiceBusJmsConnectionFactorySettings`
    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();

    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Instantaneamente o `ServiceBusJmsConnectionFactory` com o apropriado `ServiceBusConnectionString` .
    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Use o `ConnectionFactory` para criar um `Connection` e, em seguida, um`Session` 
    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    ou um `JMSContext` (para clientes JMS 2.0)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>Ligação ao Azure Service Bus Standard sobre jMS 1.1

1. Insira a configuração do Azure Service Bus no ficheiro de propriedades JNDI chamado **servicebus.properties**.
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. Configurar o contexto JNDI e configurar a ConnectionFactory
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. Use o `ConnectionFactory` para criar um `Connection` e, em seguida, um `Session` .
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>Escreva a aplicação JMS

Uma vez que o `Session` ou `JMSContext` tenha sido instantâneo, a sua aplicação pode aproveitar as CONHECIDAS JMS para executar tanto operações de gestão como de dados.

Consulte a lista de [funcionalidades JMS suportadas](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported) para o nível Standard e Premium para ver quais AS APIs serão suportadas em cada nível.

## <a name="summary"></a>Resumo
Este guia mostrou como as aplicações do cliente Java usando o Java Message Service (JMS) em amQP 1.0 podem interagir com o Azure Service Bus.

Também pode utilizar o Service Bus AMQP 1.0 de outras línguas, incluindo .NET, C, Python e PHP. Os componentes construídos utilizando estes diferentes idiomas podem trocar mensagens de forma fiável e plena fidelidade utilizando o suporte AMQP 1.0 em Service Bus.

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre o Azure Service Bus e detalhes sobre as entidades do Java Message Service (JMS) confira os links abaixo - 
* [Autocarro de serviço - Filas, Tópicos e Assinaturas](service-bus-queues-topics-subscriptions.md)
* [Service Bus - Java Message Service entities](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Suporte AMQP 1.0 em Azure Service Bus](service-bus-amqp-overview.md)
* [Guia do desenvolvedor do ônibus de serviço AMQP 1.0](service-bus-amqp-dotnet.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)

