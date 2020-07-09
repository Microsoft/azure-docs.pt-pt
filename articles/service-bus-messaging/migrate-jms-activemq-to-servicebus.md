---
title: Aplicações do Serviço de Mensagens Java Da Migração Java (JMS) da ActiveMQ para a Azure Service Bus Microsoft Docs
description: Este artigo explica como migrar aplicações JMS existentes que interagem com mQ ativo para interagir com a Azure Service Bus.
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
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122414"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>Migrar aplicações existentes do Java Message Service (JMS) 2.0 da Ative MQ para Azure Service Bus

O Azure Service Bus suporta cargas de trabalho Java/J2EE e primavera que utilizam o Serviço de Mensagens Java (JMS) 2.0 API sobre o protocolo de fila de mensagens avançadas (AMQP).

Este guia descreve o que deve estar ciente quando pretende modificar uma aplicação 2.0 do Java Message Service (JMS) existente que interage com um Corretor JMS (especificamente Apache ActiveMQ ou Amazon MQ) para interagir com o Azure Service Bus.

## <a name="before-you-start"></a>Antes de começar

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Diferenças entre o Azure Service Bus e o Apache ActiveMQ

A azure Service Bus e Apache ActiveMQ são ambos corretores de mensagens que estão a funcionar como fornecedores de JMS para aplicações de clientes para enviar mensagens e receber mensagens de. Ambos permitem a semântica ponto-a-ponto com **filas** e semântica de publicação com **tópicos** e **assinaturas.** 

Mesmo assim, há algumas diferenças entre os dois.

| Categoria | MQ ativo | Service Bus do Azure |
| --- | --- | --- |
| Tiering de aplicações | Monólito agrupado | Dois níveis <br> (Gateway + Backend) |
| Suporte de protocolo | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Modo de aprovisionamento | <ul> <li> IaaS (no local) </li> <li> Amazon MQ (PaaS gerido) </li> | PaaS gerido |
| Tamanho da mensagem | Configurável do cliente | 1 MB (nível premium) |
| Elevada Disponibilidade | Gerido pelo cliente | Plataforma gerida |
| Recuperação Após Desastre | Gerido pelo cliente | Plataforma gerida | 

### <a name="current-supported-and-unsupported-features"></a>Funcionalidades suportadas e não apoiadas atuais

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>Ressalvas

A natureza de dois níveis da Azure Service Bus oferece várias capacidades de continuidade de negócios (alta disponibilidade e recuperação de desastres). No entanto, existem algumas considerações ao utilizar funcionalidades JMS.

#### <a name="service-upgrades"></a>Atualizações de serviços

Em caso de atualizações e reinícios de autocarros de serviço, as filas temporárias ou tópicos serão eliminados.

Se a aplicação for sensível à perda de dados em filas ou tópicos temporários, recomenda-se **não** utilizar filas temporárias ou tópicos e utilizar filas, tópicos e subscrições duráveis.

#### <a name="data-migration"></a>Migração de dados

Como parte da migração/modificação das aplicações do seu cliente para interagir com a Azure Service Bus, os dados detidos no ActiveMQ não serão migrados para o Service Bus.

Poderá ser necessária uma aplicação personalizada para drenar as filas, tópicos e subscrições do ActiveMQ e reproduzir as mensagens nas filas, tópicos e subscrições do Service Bus.

#### <a name="authentication-and-authorization"></a>Autenticação e autorização

O Controle de Acesso Baseado em Função (RBAC) apoiado pela Azure ActiveDirectory é o mecanismo de autenticação preferido da Azure Service Bus.

No entanto, uma vez que o RBAC não é atualmente suportado devido à falta de suporte de autenticação baseado em sinistros por Apache QPID JMS.

Por enquanto, a autenticação é suportada apenas com as teclas SAS.

## <a name="pre-migration"></a>Pré-migração

### <a name="version-check"></a>Verificação de versão

Abaixo estão os componentes utilizados enquanto escrevem as aplicações JMS e as versões específicas que são suportadas. 

| Componentes | Versão |
|---|---|
| Serviço de Mensagens Java (JMS) API | 1.1 ou maior |
| Protocolo AMQP | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Certifique-se de que as portas AMQP estão abertas

A Azure Service Bus suporta a comunicação sobre o protocolo AMQP. Para o efeito, é necessário ativar a comunicação sobre as portas 5671 (AMQP) e 443 (TCP). Dependendo do local onde as aplicações do cliente estão hospedadas, poderá necessitar de um bilhete de apoio para permitir a comunicação sobre estas portas.

> [!IMPORTANT]
> A Azure Service Bus suporta **apenas** o protocolo AMQP 1.0.

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>Configurar configurações empresariais (VNET, Firewall, ponto final privado, etc.)

O Azure Service Bus permite várias funcionalidades de segurança empresarial e de alta disponibilidade. Para saber mais sobre eles, siga os links de documentação abaixo.

  * [Pontos finais de serviço de rede virtual](service-bus-service-endpoints.md)
  * [Firewall](service-bus-ip-filtering.md)
  * [Encriptação lateral de serviço com chave gerida pelo cliente (BYOK)](configure-customer-managed-key.md)
  * [Pontos finais privados](private-link-service.md)
  * [Autenticação e Autorização](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Monitorização, alertas e rastreio

As métricas são publicadas para cada espaço de nome do Service Bus no Azure Monitor e podem ser alavancadas para alertar e escalar dinâmicamente os recursos atribuídos ao espaço de nome.

Leia mais sobre as diferentes métricas e como configurar alertas sobre elas nas [métricas do Service Bus no Azure Monitor](service-bus-metrics-azure-monitor.md).

Leia também mais sobre [o rastreio do lado do cliente para operações de dados](service-bus-end-to-end-tracing.md) e registo [operacional/diagnóstico para operações de gestão](service-bus-diagnostic-logs.md)

### <a name="metrics---newrelic"></a>Métricas - NewRelic

Abaixo está um guia prático sobre quais as métricas do mapa ActiveMQ para as métricas em Azure Service Bus. As abaixo são referenciadas pela NewRelic.

  * [ActiveMQ/Amazon MQ Novas Métricas Relíquias](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Métricas novas relíquias do autocarro da Azure Service](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Atualmente, a NewRelic não tem uma integração perfeita com o ActiveMQ diretamente, mas eles têm métricas disponíveis para o MQ Amazon.
> Uma vez que a Amazon MQ é derivada do ActiveMQ, o guia abaixo mapeia as métricas NewRelic da AmazonMQ para a Azure Service Bus.
>

|Agrupamento métrico| Métrica AmazonMQ/MQ ativa | Métrica do autocarro da Azure Service |
|------------|---------------------------|--------------------------|
|Corretor|`CpuUtilization`|`CPUXNS`|
|Corretor|`MemoryUsage`|`WSXNS`|
|Corretor|`CurrentConnectionsCount`|`activeConnections`|
|Corretor|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Corretor|`InactiveDurableTopicSubscribersCount`|Métricas de subscrição de alavancagem|
|Corretor|`TotalMessageCount`|Alavancagem Fila/Tópico/Nível de subscrição`activeMessages`|
|Fila/Tópico|`EnqueueCount`|`incomingMessages`|
|Fila/Tópico|`DequeueCount`|`outgoingMessages`|
|Filas|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migração

Para migrar a sua aplicação JMS 2.0 existente para interagir com o Azure Service Bus, é necessário executar os passos abaixo.

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>Exportar topologia da ActiveMQ e criar as entidades em Azure Service Bus (opcional)

Para garantir que as aplicações do cliente podem conectar-se perfeitamente com o Azure Service Bus, a topologia - que inclui filas, tópicos e subscrições - precisa de ser migrada de **Apache ActiveMQ** para **Azure Service Bus.**

> [!NOTE]
> Para aplicações do Java Message Service (JMS), a criação de Filas, Tópicos e Subscrições é uma operação de tempo de execução. A maioria dos fornecedores de Serviço de Mensagens Java (JMS) (corretores de mensagens) oferecem a funcionalidade para criar *filas,* *tópicos* e *subscrições* em tempo de execução.
>
> Assim, o passo acima é opcional.
>
> Para garantir que a sua aplicação tem as permissões para criar a topologia em tempo de execução, certifique-se de que o fio de ligação com permissões ***SAS 'Manage'*** é utilizado.

Para efetuar isto 
  * Aproveite as [ferramentas da Linha de Comando ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) para exportar a topologia
  * Recrie a mesma topologia usando um [modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
  * Execute o modelo Azure Resource Manager.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Importar a dependência de maven para implementação de Bus de Serviço JMS

Para garantir uma conectividade perfeita com a Azure Service Bus, o pacote ***azure-servicebus-jms*** precisa de ser adicionado como uma dependência do ficheiro Maven `pom.xml` como abaixo.

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

Esta parte é personalizada para o servidor de aplicações que está hospedando as aplicações do seu cliente conectando-se a MQ Ativo.

#### <a name="tomcat"></a>Tomcat

Aqui, começamos com a configuração específica para MQ ativo, como mostrado no `/META-INF/context.xml` ficheiro.

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

que pode ser adaptado como abaixo para apontar para Azure Service Bus

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

##### <a name="update-applicationproperties-file"></a>Atualizar `application.properties` ficheiro

Se utilizar uma aplicação de arranque de mola para ligar ao ActiveMQ.

Aqui, o objetivo é ***remover*** as propriedades específicas do ActiveMQ do `application.properties` ficheiro.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Em seguida, ***adicione*** as propriedades específicas do Service Bus ao `application.properties` ficheiro.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Substitua a ActiveMQConnectionFactory por ServiceBusJmsConnectionFactory

O próximo passo é substituir a instância de ActiveMQConnectionFactory pela ServiceBusJmsConnectionFactory.

> [!NOTE] 
> As alterações reais do código são específicas da aplicação e da forma como as dependências são geridas, mas a amostra abaixo fornece as orientações sobre ***o que*** deve ser alterado.
>

Anteriormente, poderá estar a instantanear um objeto da ActiveMQConnectionFactory como abaixo.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Isto seria alterado para o instantâneo um objeto da ServiceBusJmsConnectionFactory

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Pós-migração

Agora que modificou a aplicação para começar a enviar e receber mensagens da Azure Service Bus, deve verificar se funciona como espera. Uma vez feito, pode proceder para aperfeiçoar e modernizar a sua pilha de aplicações.

## <a name="next-steps"></a>Próximos passos

Aproveite o [Boot Starter de Mola para o Azure Service Bus JMS](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) para uma integração perfeita com o Azure Service Bus.

Para saber mais sobre mensagens de Service Bus e Java Message Service (JMS), consulte os seguintes tópicos:

* [JMS de autocarro de serviço](service-bus-java-how-to-use-jms-api-amqp.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
