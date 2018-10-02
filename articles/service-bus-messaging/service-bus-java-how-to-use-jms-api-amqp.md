---
title: Como utilizar o AMQP 1.0 com a API de barramento de serviço de Java | Documentos da Microsoft
description: Como utilizar o Java Message Service (JMS) com o Azure Service Bus e o Advanced Message Queuing Protodol (AMQP) 1.0.
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
editor: ''
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2018
ms.author: spelluru
ms.openlocfilehash: 9a223c67e0c1f2e71d2953be63924a114e7420af
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018235"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Como utilizar o Java Message Service (JMS) API com do Service Bus e AMQP 1.0
O Advanced Message colocação Protocol (AMQP) 1.0 é um protocolo de mensagens eficiente, fiável e ao nível da transmissão que pode utilizar para criar aplicativos de mensagens robustos e para várias plataformas.

Suporte de AMQP 1.0 no Service Bus significa que pode utilizar a colocação em fila e de publicação/subscrição funcionalidades de mensagens mediadas entre uma variedade de plataformas através de um protocolo binário eficiente. Além disso, pode criar aplicativos compostos por componentes criados usando uma combinação de linguagens, arquiteturas e sistemas operativos.

Este artigo explica como utilizar o Service Bus (filas e tópicos de publicação/subscrição) de recursos de mensagens das aplicações Java com o popular Java Message Service (JMS) API standard. Há uma [artigo complementar](service-bus-amqp-dotnet.md) que explica como fazer o mesmo com a API de .NET do Service Bus. Pode utilizar estes guias de dois em conjunto para saber mais sobre mensagens de várias plataformas com AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Introdução ao Service Bus
Este guia assume que já tem um espaço de nomes do Service Bus que contém uma fila com o nome **basicqueue**. Se não o fizer, em seguida, pode [criar o espaço de nomes e a fila](service-bus-create-namespace-portal.md) utilizando o [portal do Azure](https://portal.azure.com). Para obter mais informações sobre como criar espaços de nomes do Service Bus e filas, veja [introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Filas e tópicos particionados também suportam AMQP. Para obter mais informações, consulte [entidades de mensagens Particionadas](service-bus-partitioning.md) e [suporte de AMQP 1.0 para o Service Bus filas e tópicos particionados](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Baixar a biblioteca de cliente AMQP 1.0 JMS
Para obter informações sobre onde pode transferir a versão mais recente da biblioteca de cliente Apache Qpid JMS AMQP 1.0, visite [ https://qpid.apache.org/download.html ](https://qpid.apache.org/download.html).

Tem de adicionar os seguintes ficheiros JAR quatro de arquivo de distribuição Apache Qpid JMS AMQP 1.0 para o caminho da classe de Java quando criar e executar aplicativos de JMS com o Service Bus:

* geronimo jms\_1.1\_spec 1.0.jar
* qpid-jms - client-[version]. JAR

> ! [NOTA] Podem ter alterado JMS JAR nomes e versões. Para obter detalhes, consulte [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Programar aplicações Java
### <a name="java-naming-and-directory-interface-jndi"></a>A atribuição de nomes de Java e a Interface de diretório (JNDI)
JMS usa a nomenclatura de Java e a Interface de diretório (JNDI) para criar uma separação entre nomes de lógicos e físico. Dois tipos de objetos JMS são resolvidos usando JNDI: ConnectionFactory e de destino. JNDI utiliza um modelo de provedor no qual pode conectar os serviços de diretório diferente para processar tarefas de resolução de nome. Formato do Apache Qpid JMS AMQP 1.0 biblioteca é fornecida com um simples propriedades fornecedor JNDI baseados em ficheiros, que é configurado usando um arquivo de propriedades dos seguintes procedimentos:

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Definir o contexto JNDI e configurar o ConnectionFactory

O **ConnectionString** referenciado no disponível na "partilhados políticas de acesso" na [Portal do Azure](https://portal.azure.com) sob **cadeia de ligação primária**
```
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Configurar o produtor e consumidor filas de destino
A entrada usada para definir um destino no fornecedor JNDI do arquivo de propriedades de Qpid é no seguinte formato:

Para criar a fila de destino para o produtor- 
```
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Para criar uma fila de destino para o consumidor- 
```
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Escreva a aplicação de JMS
Não há especial APIs ou opções necessárias ao utilizar JMS com o Service Bus. No entanto, existem algumas restrições que serão abordadas mais tarde. Como com qualquer aplicativo JMS, é a primeira coisa que necessário, configuração do ambiente JNDI, ser capaz de resolver uma **ConnectionFactory** e destinos.

#### <a name="configure-the-jndi-initialcontext"></a>Configurar o InitialContext JNDI
O ambiente de JNDI está configurado, passando uma tabela de hash de informações de configuração para o construtor da classe javax.naming.InitialContext. Os dois necessário elementos da tabela de hash são o nome da classe de fábrica de contexto inicial e o URL do fornecedor. O código seguinte mostra como configurar o ambiente de JNDI para utilizar o arquivo de propriedades baseado em provedor JNDI com um arquivo de propriedades com o nome de Qpid **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Um aplicativo JMS simples com uma fila do Service Bus
O programa de exemplo seguintes envia JMS TextMessages uma fila do Service Bus com o nome lógico JNDI da fila e recebe as mensagens de volta.

Pode aceder aos todas as origem código e a configuração informações do [Azure Service Bus exemplos JMS fila início rápido](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Executar a aplicação
Passar o **cadeia de ligação** das políticas a iniciativas partilhados para executar a aplicação.
Segue-se a saída do formulário ao executar o aplicativo:

```
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Disposição de AMQP e mapeamento de operação do Service Bus
Eis como uma disposição de AMQP traduz-se uma operação do Service Bus:

```
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```


## <a name="unsupported-features-and-restrictions"></a>Funcionalidades não suportadas e restrições
As seguintes restrições existem ao utilizar JMS através do AMQP 1.0 com o Service Bus, ou seja:

* Apenas um **MessageProducer** ou **MessageConsumer** permitidas por **sessão**. Se precisar de criar vários **MessageProducers** ou **MessageConsumers** num aplicativo, criar um dedicado **sessão** para cada uma delas.
* Subscrições de tópicos volátil não são atualmente suportadas.
* **MessageSelectors** não são atualmente suportadas.
* Sessões confiáveis e transações distribuídas não são suportadas.

Além disso, o Azure Service Bus divide o plano de controlo do plano de dados e, portanto, não suporta várias funções de topologia dinâmico do JMS:

| Método não suportado          | Substituir por                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | criar uma subscrição de tópico portar o Seletor de mensagem                                 |
| createDurableConsumer       | criar uma subscrição de tópico portar o Seletor de mensagem                                 |
| createSharedConsumer        | Tópicos do Service Bus são sempre partilhável, consulte acima                                       |
| createSharedDurableConsumer | Tópicos do Service Bus são sempre partilhável, consulte acima                                       |
| createTemporaryTopic        | criar um tópico através da gestão de API/ferramentas/portal com *AutoDeleteOnIdle* definido para um período de expiração |
| createTopic                 | criar um tópico através da gestão de API/ferramentas/portal                                           |
| anular a subscrição                 | eliminar a API/ferramentas/portal de gestão de tópico                                             |
| createBrowser               | não suportado. Utilizar a funcionalidade de Peek() da API do Service Bus                         |
| createQueue                 | criar uma fila através da gestão de API/ferramentas/portal                                           | 
| createTemporaryQueue        | criar uma fila através da gestão de API/ferramentas/portal com *AutoDeleteOnIdle* definido para um período de expiração |

## <a name="summary"></a>Resumo
Este guia de procedimentos mostrou como utilizar mediadas do Service Bus funcionalidades de mensagens (filas e tópicos de publicação/subscrição) do Java com a API de JMS populares e AMQP 1.0.

Também pode utilizar o Service Bus AMQP 1.0 de outras linguagens, incluindo .NET, C, Python e PHP. Componentes criados com essas linguagens diferentes podem trocar mensagens de forma fiável e em fidelidade total usando o AMQP 1.0 suporte no Service Bus.

## <a name="next-steps"></a>Passos Seguintes
* [Suporte de AMQP 1.0 no Azure Service Bus](service-bus-amqp-overview.md)
* [Como utilizar o AMQP 1.0 com a API .NET do Service Bus](service-bus-dotnet-advanced-message-queuing.md)
* [AMQP 1.0 no Service Bus guia para programadores](service-bus-amqp-dotnet.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Centro de Programadores do Java](https://azure.microsoft.com/develop/java/)

