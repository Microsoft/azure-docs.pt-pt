---
title: Aplicações do Serviço de Mensagens De Java Migrae (JMS) da Apache ActiveMQ para a Azure Service Bus Microsoft Docs
description: Este artigo explica como migrar aplicações JMS existentes que interagem com a Apache ActiveMQ para interagir com a Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.custom: devx-track-java
ms.openlocfilehash: 1b07faa5b2540aafafc27a51192d824d4445ce35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88067159"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>Migrar aplicações existentes do Java Message Service (JMS) 2.0 da Apache ActiveMQ para Azure Service Bus

Este artigo discute como modificar uma aplicação 2.0 do Java Message Service (JMS) existente que interage com um Corretor JMS para interagir com a Azure Service Bus. Em particular, o artigo abrange a migração da Apache ActiveMQ ou da Amazon MQ.

A Azure Service Bus suporta cargas de trabalho java 2, Enterprise Edition e primavera que utilizam a API JMS 2.0 sobre o Protocolo avançado de fila de mensagens (AMQP).

## <a name="before-you-start"></a>Antes de começar

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Diferenças entre o Azure Service Bus e o Apache ActiveMQ

A Azure Service Bus e Apache ActiveMQ são ambos corretores de mensagens, funcionando como fornecedores JMS para aplicações de clientes para enviar mensagens e receber mensagens de. Ambos permitem a semântica ponto-a-ponto com filas e publicam semântica com tópicos e assinaturas. 

Ainda assim, existem algumas diferenças entre os dois, como mostra a tabela seguinte:

| Categoria | ActiveMQ | Service Bus do Azure |
| --- | --- | --- |
| Tiering de aplicações | Monólito agrupado | Dois níveis <br> (gateway + back end) |
| Suporte de protocolo | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Modo de aprovisionamento | <ul> <li> Infraestrutura como serviço (IaaS), no local </li> <li> Amazon MQ (plataforma gerida como um serviço) </li> | Plataforma gerida como um serviço (PaaS) |
| Tamanho da mensagem | Configurável do cliente | 1 MB (nível premium) |
| Elevada disponibilidade | Gerido pelo cliente | Plataforma gerida |
| Recuperação após desastre | Gerido pelo cliente | Plataforma gerida | 

### <a name="current-supported-and-unsupported-features"></a>Funcionalidades suportadas e não apoiadas atuais

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>Considerações

A natureza de dois níveis da Azure Service Bus oferece várias capacidades de continuidade de negócios (alta disponibilidade e recuperação de desastres). No entanto, existem algumas considerações quando se está a usar funcionalidades JMS.

#### <a name="service-upgrades"></a>Atualizações de serviços

Em caso de atualizações e reinícios de autocarros de serviço, são eliminadas filas ou tópicos temporários. Se a sua aplicação for sensível à perda de dados em filas ou tópicos temporários, não utilize filas ou tópicos temporários. Em vez disso, utilize filas, tópicos e subscrições duráveis.

#### <a name="data-migration"></a>Migração de dados

Como parte da migração e modificação das aplicações do seu cliente para interagir com a Azure Service Bus, os dados detidos no ActiveMQ não são migrados para o Service Bus. Poderá necessitar de uma aplicação personalizada para drenar as filas, tópicos e subscrições do ActiveMQ e, em seguida, reproduzir as mensagens para as filas, tópicos e subscrições do Service Bus.

#### <a name="authentication-and-authorization"></a>Autenticação e autorização

O controlo de acesso baseado em funções (RBAC), apoiado pelo Azure Ative Directory, é o mecanismo de autenticação preferido para o Service Bus. Uma vez que o RBAC, ou a autenticação baseada em sinistros, não é atualmente suportado pelo Apache QPID JMS, no entanto, deve utilizar as chaves SAS para autenticação.

## <a name="pre-migration"></a>Pré-migração

### <a name="version-check"></a>Verificação de versão

Utiliza os seguintes componentes e versões enquanto escreve as aplicações JMS: 

| Componente | Versão |
|---|---|
| Serviço de Mensagens Java (JMS) API | 1.1 ou maior |
| Protocolo AMQP | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Certifique-se de que as portas AMQP estão abertas

A Service Bus suporta a comunicação sobre o protocolo AMQP. Para o efeito, permitir a comunicação sobre as portas 5671 (AMQP) e 443 (TCP). Dependendo do local onde as aplicações do cliente estão hospedadas, você pode precisar de um bilhete de apoio para permitir a comunicação sobre estes portos.

> [!IMPORTANT]
> O Service Bus suporta apenas o protocolo AMQP 1.0.

### <a name="set-up-enterprise-configurations"></a>Configurar configurações empresariais

O Service Bus permite várias funcionalidades de segurança empresarial e de alta disponibilidade. Para obter mais informações, consulte: 

  * [Pontos finais de serviço de rede virtual](service-bus-service-endpoints.md)
  * [Firewall](service-bus-ip-filtering.md)
  * [Encriptação lateral de serviço com chave gerida pelo cliente (BYOK)](configure-customer-managed-key.md)
  * [Pontos finais privados](private-link-service.md)
  * [Autenticação e autorização](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Monitorização, alertas e rastreio

Para cada espaço de nomes do Service Bus, publica métricas no Azure Monitor. Você pode usar estas métricas para alertar e escalar dinâmico de recursos alocados ao espaço de nome.

Para obter mais informações sobre as diferentes métricas e como configurar alertas sobre elas, consulte as [métricas do Service Bus no Azure Monitor](service-bus-metrics-azure-monitor.md). Também pode saber mais sobre [o rastreio do lado do cliente para operações](service-bus-end-to-end-tracing.md) de dados e registo [operacional/diagnóstico para operações de gestão.](service-bus-diagnostic-logs.md)

### <a name="metrics---new-relic"></a>Métricas - Nova Relíquia

Pode correlacionar quais métricas do mapa ActiveMQ a que métricas no Azure Service Bus. Veja o seguinte no site da New Relic:

  * [ActiveMQ/Amazon MQ Novas Métricas Relíquias](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure Service Bus Novas Métricas Relíquias](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Atualmente, a New Relic não tem integração direta e perfeita com o ActiveMQ, mas eles têm métricas disponíveis para o MQ da Amazon. Como a Amazon MQ é derivada do ActiveMQ, a tabela seguinte mapeia as novas métricas relíquias da Amazon MQ para a Azure Service Bus.
>

|Agrupamento métrico| Métrica amazon MQ/ActiveMQ | Métrica do autocarro da Azure Service |
|------------|---------------------------|--------------------------|
|Corretor|`CpuUtilization`|`CPUXNS`|
|Corretor|`MemoryUsage`|`WSXNS`|
|Corretor|`CurrentConnectionsCount`|`activeConnections`|
|Corretor|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Corretor|`InactiveDurableTopicSubscribersCount`|Use métricas de subscrição|
|Corretor|`TotalMessageCount`|Use o nível de fila/tópico/subscrição `activeMessages`|
|Fila/Tópico|`EnqueueCount`|`incomingMessages`|
|Fila/Tópico|`DequeueCount`|`outgoingMessages`|
|Fila|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migração

Para migrar a sua aplicação JMS 2.0 existente para interagir com o Service Bus, siga os passos nas próximas secções.

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>Exporte a topologia da ActiveMQ e crie as entidades em Service Bus (opcional)

Para garantir que as aplicações do cliente podem conectar-se perfeitamente com o Service Bus, migrar a topologia (incluindo filas, tópicos e subscrições) da Apache ActiveMQ para o Service Bus.

> [!NOTE]
> Para aplicações JMS, cria filas, tópicos e subscrições como uma operação de tempo de execução. A maioria dos fornecedores JMS (corretores de mensagens) dão-lhe a capacidade de criá-los em tempo de execução. É por isso que este passo de exportação é considerado opcional. Para garantir que a sua aplicação tem permissões para criar a topologia em tempo de execução, utilize o fio de ligação com `Manage` permissões SAS.

Para efetuar este procedimento:

1. Utilize as [ferramentas da linha de comando ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) para exportar a topologia.
1. Re-crie a mesma topologia utilizando um [modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
1. Executar o modelo de Gestor de Recursos Azure.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Importar a dependência de maven para implementação de Bus de Serviço JMS

Para garantir uma conectividade perfeita com o Service Bus, adicione o `azure-servicebus-jms` pacote como uma dependência ao ficheiro Maven, da seguinte `pom.xml` forma:

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Alterações na configuração do servidor de aplicações

Esta peça é personalizada para o servidor de aplicações que está hospedando as aplicações do seu cliente conectando-se ao ActiveMQ.

#### <a name="tomcat"></a>Tomcat

Aqui, começa com a configuração específica do ActiveMQ, como mostrado no `/META-INF/context.xml` ficheiro.

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

Adapta-se isto a point to Service Bus, da seguinte forma:

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Aplicações de primavera

##### <a name="update-the-applicationproperties-file"></a>Atualizar o `application.properties` ficheiro

Se estiver a utilizar uma aplicação de arranque primavera para ligar ao ActiveMQ, pretende remover as propriedades específicas do ActiveMQ do `application.properties` ficheiro.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Em seguida, adicione as propriedades específicas do Service Bus ao `application.properties` ficheiro.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Substituir `ActiveMQConnectionFactory` por `ServiceBusJmsConnectionFactory`

O próximo passo é substituir a instância de `ActiveMQConnectionFactory` `ServiceBusJmsConnectionFactory` .

> [!NOTE] 
> As alterações reais do código são específicas da aplicação e da forma como as dependências são geridas, mas a amostra que se segue fornece as orientações sobre o que deve ser alterado.
>

Anteriormente, poderia ter sido instantaneamente um objeto `ActiveMQConnectionFactory` de, da seguinte forma:

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Agora, estás a mudar isto para instantaneaizar um objeto `ServiceBusJmsConnectionFactory` de, da seguinte forma:

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Pós-migração

Agora que modificou a aplicação para começar a enviar e receber mensagens da Service Bus, deve verificar se funciona como espera. Quando isso estiver feito, pode proceder para aperfeiçoar e modernizar a sua pilha de aplicações.

## <a name="next-steps"></a>Passos seguintes

Utilize o [Boot Starter Spring para o Azure Service Bus JMS](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) para uma integração perfeita com o Service Bus.

Para saber mais sobre as mensagens de Service Bus e JMS, consulte:

* [JMS de autocarro de serviço](service-bus-java-how-to-use-jms-api-amqp.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)