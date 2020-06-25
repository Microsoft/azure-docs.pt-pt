---
title: Use AMQP com Java Message Service API & ônibus de serviço Azure
description: Como utilizar o Serviço de Mensagens Java (JMS) com o Azure Service Bus e o Advanced Message Queing Protocol (AMQP) 1.0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: ccea6175d0baec56b609538d15c32892bb2edff0
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85341738"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Utilize o Serviço de Mensagens Java (JMS) com ônibus de serviço Azure e AMQP 1.0
Este artigo explica como usar funcionalidades de mensagens Azure Service Bus (filas e publicar/subscrever tópicos) de aplicações Java usando a popular norma API do Java Message Service (JMS). Existe um [artigo de acompanhante](service-bus-amqp-dotnet.md) que explica como fazer o mesmo usando o Azure Service Bus .NET API. Você pode usar estes dois guias juntos para aprender sobre mensagens cross-platform usando AMQP 1.0.

O Protocolo avançado de Filade Mensagens (AMQP) 1.0 é um protocolo de mensagens eficiente, fiável e de nível de fio que pode utilizar para construir aplicações de mensagens robustas e inter-plataformas.

Suporte para AMQP 1.0 no Azure Service Bus significa que você pode usar as funcionalidades de mensagens intermediadas em fila e publicar/subscrever funcionalidades de mensagens intermediadas de uma série de plataformas usando um protocolo binário eficiente. Além disso, pode construir aplicações compostas por componentes construídos utilizando uma mistura de idiomas, quadros e sistemas operativos.

## <a name="get-started-with-service-bus"></a>Introdução ao Service Bus
Este guia pressupõe que já tem um espaço de nomes de Service Bus contendo uma fila chamada `basicqueue` . Se não o fizer, poderá [criar o espaço de nome e a fila](service-bus-create-namespace-portal.md) utilizando o portal [Azure](https://portal.azure.com). Para obter mais informações sobre como criar espaços e filas de nomes de Service Bus, consulte [Começar com as filas do Service Bus.](service-bus-dotnet-get-started-with-queues.md)

> [!NOTE]
> Filas e tópicos divididos também apoiam AMQP. Para obter mais informações, consulte [as entidades de mensagens Partitioned](service-bus-partitioning.md) e [o suporte amQP 1.0 para filas e tópicos divididos do Service Bus.](service-bus-partitioned-queues-and-topics-amqp-overview.md)
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Descarregar a biblioteca de clientes AMQP 1.0 JMS
Para obter informações sobre onde descarregar a versão mais recente da biblioteca cliente Apache Qpid JMS AMQP 1.0, visite [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html) .

Deve adicionar os seguintes quatro ficheiros JAR do arquivo de distribuição Apache Qpid JMS AMQP 1.0 ao Java CLASSPATH ao construir e executar aplicações JMS com Service Bus:

* geronimo-jms \_ 1.1 \_ spec-1.0.jar
* qpid-jms-cliente-[versão].jar

> [!NOTE]
> Os nomes e versões JMS JAR podem ter mudado. Para mais detalhes, consulte [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Aplicações de codificação java
### <a name="java-naming-and-directory-interface-jndi"></a>Java Naming and Directy Interface (JNDI)
JMS usa a Interface de Nomeação e Diretório Java (JNDI) para criar uma separação entre nomes lógicos e nomes físicos. Dois tipos de objetos JMS são resolvidos usando JNDI: ConnectionFactory e Destination. A JNDI utiliza um modelo de fornecedor no qual pode ligar diferentes serviços de diretório para lidar com os deveres de resolução de nomes. A biblioteca Apache Qpid JMS AMQP 1.0 vem com um simples fornecedor JNDI baseado em ficheiros de propriedade que está configurado usando um ficheiro de propriedades do seguinte formato:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Configurar o contexto JNDI e configurar a ConnectionFactory

O **ConnectionString** referenciado no disponível nas "Políticas de Acesso Partilhado" no [portal Azure](https://portal.azure.com) sob **a Cadeia de Ligação Primária**
```java
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

#### <a name="configure-producer-and-consumer-destination-queues"></a>Configure as filas de produtor e de destino ao consumidor
A entrada utilizada para definir um destino no ficheiro de propriedades Qpid JNDI é do seguinte formato:

Para criar a fila de destino para o Produtor - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Para criar uma fila de destino para o Consumidor - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Escreva a aplicação JMS
Não são necessárias APIs especiais ou opções quando se utiliza o JMS com o Service Bus. No entanto, existem algumas restrições que serão abrangidas mais tarde. Como em qualquer aplicação JMS, a primeira coisa necessária é a configuração do ambiente JNDI, para ser capaz de resolver uma **ConnectionFactory** e destinos.

#### <a name="configure-the-jndi-initialcontext"></a>Configure o Texto Inicial JNDI
O ambiente JNDI é configurado passando uma hastácbula de informação de configuração para o construtor da classe tialContext javax.naming.Ini. Os dois elementos necessários no haxixe são o nome de classe da Fábrica de Contexto Inicial e do URL do Fornecedor. O código que se segue mostra como configurar o ambiente JNDI para utilizar o fornecedor JNDI baseado em propriedades Qpid com um ficheiro de propriedades chamado **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Uma aplicação JMS simples usando uma fila de ônibus de serviço
O seguinte programa de exemplo envia as mensagens JMS TextMess para uma fila de ônibus de serviço com o nome lógico JNDI de QUEUE, e recebe as mensagens de volta.

Todos podem aceder a todas as informações de código fonte e configuração a partir do [quickstart da Fila JMS do Serviço Azure](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

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
Passe a cadeia de **ligação** das políticas de acesso partilhado para executar a aplicação.
Abaixo está a saída do formulário executando a Aplicação:

```Output
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

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Disposição amQP e mapeamento da operação de ônibus de serviço
Eis como uma disposição amqp se traduz numa operação de Autocarro de Serviço:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS Tópicos vs. Tópicos de Autocarro de Serviço
A utilização de tópicos e subscrições do Azure Service Bus através do Java Message Service (JMS) a API fornece capacidades básicas de envio e receção. É uma escolha conveniente quando a apresentação de aplicações de outros corretores de mensagens com APIs compatíveis com JMS, mesmo que os tópicos do Service Bus diferem dos tópicos JMS e exijam alguns ajustes. 

Os tópicos do Azure Service Bus encaminham mensagens para subscrições nomeadas, partilhadas e duráveis que são geridas através da interface Azure Resource Management, das ferramentas da linha de comando Azure ou através do portal Azure. Cada subscrição permite até 2000 regras de seleção, cada uma das quais pode ter uma condição de filtro e, para filtros SQL, também uma ação de transformação de metadados. Cada correspondência de condição de filtro seleciona a mensagem de entrada a ser copiada na subscrição.  

Receber mensagens de subscrições é idêntico receber mensagens de filas. Cada subscrição tem uma fila de letras mortas associada e a capacidade de encaminhar automaticamente mensagens para outra fila ou tópicos. 

Os Tópicos JMS permitem aos clientes criar dinâmicas subscritores não duradouros e duráveis que, opcionalmente, permitam filtrar mensagens com seletores de mensagens. Estas entidades não partilhadas não são apoiadas pela Service Bus. A sintaxe da regra do filtro SQL para o Service Bus é, no entanto, semelhante à sintaxe do seletor de mensagens suportada pelo JMS. 

O lado da editora JMS Topic é compatível com o Service Bus, como mostrado nesta amostra, mas os subscritores dinâmicos não são. As seguintes APIs relacionadas com topologia não são suportadas com o Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Funcionalidades e restrições não suportadas
Existem as seguintes restrições ao utilizar o JMS em amQP 1.0 com o Service Bus, nomeadamente:

* Apenas é permitido um **MensagemProdutor** ou **MessageConsumer** por **Sessão**. Se necessitar de criar **vários MessageProducers** ou **MessageConsumers** numa aplicação, crie uma **Sessão** dedicada para cada um deles.
* As subscrições de tópicos voláteis não são suportadas atualmente.
* **Os selecionadores de mensagens** não são suportados atualmente.
* As transações distribuídas não são suportadas (mas as sessões transacionadas são suportadas).

Além disso, a Azure Service Bus divide o plano de controlo do plano de dados e, portanto, não suporta várias das funções dinâmicas de topologia da JMS:

| Método não suportado          | Substituir                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | criar uma subscrição de tópico porting o seletor de mensagens                                 |
| criarDurableConsumer       | criar uma subscrição de tópico porting o seletor de mensagens                                 |
| createPartindo        | Os tópicos do Service Bus são sempre partilháveis, veja acima                                       |
| createSharedDurableConsumer | Os tópicos do Service Bus são sempre partilháveis, veja acima                                       |
| createSteporaryTopic        | criar um tópico através da gestão API/tools/portal com *AutoDeleteOnIdle* definido para um período de expiração |
| criarTopic                 | criar um tópico através da gestão API/tools/portal                                           |
| cancelar a subscrição                 | eliminar a API/ferramentas/portal de gestão de tópicos                                             |
| criarRowser               | sem apoio. Utilize a funcionalidade Peek() da API do Autocarro de Serviço                         |
| criarQueue                 | criar uma fila através da gestão API/tools/portal                                           | 
| createSteporaryQueue        | criar uma fila através da gestão API/tools/portal com *AutoDeleteOnIdle* definido para um período de expiração |
| receberNoWait               | utilizar o método de receção() fornecido pelo Service Bus SDK e especificar um tempo limite muito baixo ou zero |

## <a name="summary"></a>Resumo
Este guia de como usar funcionalidades de mensagens intermediadas do Service Bus (filas e tópicos de publicação/subscrição) de Java utilizando os populares JMS API e AMQP 1.0.

Também pode utilizar o Service Bus AMQP 1.0 de outras línguas, incluindo .NET, C, Python e PHP. Os componentes construídos utilizando estes diferentes idiomas podem trocar mensagens de forma fiável e plena fidelidade utilizando o suporte AMQP 1.0 em Service Bus.

## <a name="next-steps"></a>Passos seguintes
* [Suporte AMQP 1.0 em Azure Service Bus](service-bus-amqp-overview.md)
* [Como utilizar amQP 1.0 com o Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)
* [Guia do desenvolvedor do ônibus de serviço AMQP 1.0](service-bus-amqp-dotnet.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Centro de Programadores do Java](https://azure.microsoft.com/develop/java/)

