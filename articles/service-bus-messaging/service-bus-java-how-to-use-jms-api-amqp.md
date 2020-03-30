---
title: Use AMQP com Java Message Service API & Azure Service Bus
description: Como utilizar o Java Message Service (JMS) com o Azure Service Bus e o Protocolo avançado de Fila de Mensagens (AMQP) 1.0.
services: service-bus-messaging
documentationcenter: java
author: axisc
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: cd06838abbb69af5684fdea18c42f6a8f95ffe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371266"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Utilize o Java Message Service (JMS) com o Azure Service Bus e o AMQP 1.0
Este artigo explica como usar funcionalidades de mensagens Azure Service Bus (filas e tópicos de publicação/subscrição) a partir de aplicações Java utilizando a popular norma API do Java Message Service (JMS). Há um artigo de [acompanhante](service-bus-amqp-dotnet.md) que explica como fazer o mesmo usando o Azure Service Bus .NET API. Pode utilizar estes dois guias em conjunto para aprender sobre mensagens cross-platform usando AMQP 1.0.

O Protocolo de Fila de Mensagens Avançadas (AMQP) 1.0 é um protocolo de mensagens eficiente, fiável e de nível de fio que pode usar para construir aplicações de mensagens robustas e transplataformas.

Suporte para AMQP 1.0 em Ônibus de serviço Azure significa que você pode usar as funcionalidades de mensagens intermediadas de fila e subscrição de uma série de plataformas usando um protocolo binário eficiente. Além disso, pode construir aplicações compostas por componentes construídos utilizando uma mistura de línguas, quadros e sistemas operativos.

## <a name="get-started-with-service-bus"></a>Introdução ao Service Bus
Este guia assume que já tem um espaço de `basicqueue`nome service Bus contendo uma fila chamada . Se não o fizer, pode criar o espaço de [nome e a fila](service-bus-create-namespace-portal.md) utilizando o portal [Azure](https://portal.azure.com). Para obter mais informações sobre como criar espaços de nome sinuosos e filas de ônibus de serviço, consulte [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Filas e tópicos divididos também suportam amqp. Para mais informações, consulte as entidades de [mensagens divididas](service-bus-partitioning.md) e o [suporte amqP 1.0 para filas e tópicos divididos](service-bus-partitioned-queues-and-topics-amqp-overview.md)do Bus de Serviço.
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Descarregar a biblioteca de clientes AMQP 1.0 JMS
Para obter informações sobre onde descarregar a versão mais recente da biblioteca cliente Apache [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html)Qpid JMS AMQP 1.0, visite .

Deve adicionar os seguintes quatro ficheiros JAR do arquivo de distribuição Apache Qpid JMS AMQP 1.0 ao Java CLASSPATH ao construir e executar aplicações JMS com Bus de Serviço:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-cliente-[versão].jar

> [!NOTE]
> Os nomes e versões jMS JAR podem ter mudado. Para mais detalhes, consulte [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Codificar aplicações Java
### <a name="java-naming-and-directory-interface-jndi"></a>Java Naming and Directy Interface (JNDI)
O JMS utiliza a Java Naming and Directory Interface (JNDI) para criar uma separação entre nomes lógicos e nomes físicos. Dois tipos de objetos JMS são resolvidos usando O JNDI: ConnectionFactory e Destination. O JNDI utiliza um modelo de fornecedor no qual pode ligar diferentes serviços de diretório saem para lidar com os deveres de resolução de nomes. A biblioteca Apache Qpid JMS AMQP 1.0 vem com um simples fornecedor jndi baseado em ficheiros de propriedade que é configurado usando um ficheiro de propriedades do seguinte formato:

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

O **ConnectionString** referenciado no disponível nas "Políticas de Acesso Partilhado" no [portal Azure](https://portal.azure.com) sob string de **conexão primária**
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

#### <a name="configure-producer-and-consumer-destination-queues"></a>Configure Filas de Destino de Produtor e Consumidor
A entrada utilizada para definir um destino no ficheiro de propriedades Qpid o fornecedor JNDI é do seguinte formato:

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

### <a name="write-the-jms-application"></a>Escreva o pedido JMS
Não existem APIs ou opções especiais necessárias ao utilizar JMS com ônibus de serviço. No entanto, existem algumas restrições que serão abordadas mais tarde. Como em qualquer aplicação JMS, a primeira coisa necessária é a configuração do ambiente JNDI, para ser capaz de resolver uma **ConnectionFactory** e destinos.

#### <a name="configure-the-jndi-initialcontext"></a>Configure o Contexto Inicial do JNDI
O ambiente JNDI é configurado através da passagem de um hashtable de informação de configuração para o construtor da classe javax.naming.InitialContext. Os dois elementos necessários no hashtable são o nome de classe da Fábrica de Contexto Inicial e do URL do Fornecedor. O código seguinte mostra como configurar o ambiente JNDI para usar o ficheiro de propriedades Qpid baseado em JNDI Provider com um ficheiro de propriedades chamado **servicebus.properties**.

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
O programa de exemplo seguinte envia Mensagens de Texto JMS para uma fila de ônibus de serviço com o nome lógico JNDI da FILA, e recebe as mensagens de volta.

Todos vocês podem aceder a todas as informações de código de origem e configuração do [Azure Service Bus Samples JMS Queue quickstart](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

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
Passe a Cadeia de **Ligação** das Políticas de Acesso Partilhado para executar a aplicação.
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

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Disposição AMQP e mapeamento de operação de ônibus de serviço
Eis como uma disposição AMQP se traduz numa operação de ônibus de serviço:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Tópicos JMS vs. Tópicos de Autocarro de Serviço
A utilização de tópicos e subscrições de ônibus de serviço Azure através do Java Message Service (JMS) A API fornece capacidades básicas de envio e receção. É uma escolha conveniente quando se analisam aplicações de outros corretores de mensagens com APIs compatíveis com JMS, embora os tópicos do Bus de Serviço sofra dos Tópicos JMS e exijam alguns ajustes. 

Os tópicos do Azure Service Bus direcionam mensagens para subscrições como, partilhadas e duráveis que são geridas através da interface Azure Resource Management, das ferramentas da linha de comando Azure ou através do portal Azure. Cada subscrição permite até 2000 regras de seleção, cada uma das quais pode ter uma condição de filtro e, para filtros SQL, também uma ação de transformação de metadados. Cada partida de condição do filtro seleciona a mensagem de entrada a copiar para a subscrição.  

Receber mensagens de subscrições é idêntico a receber mensagens de filas. Cada subscrição tem uma fila de cartas mortas associada e a capacidade de encaminhar automaticamente mensagens para outra fila ou tópicos. 

Os Tópicos JMS permitem que os clientes criem de forma dinâmica assinantes não duráveis e duráveis que permitem opcionalmente filtrar mensagens com selecionadores de mensagens. Estas entidades não partilhadas não são apoiadas pela Service Bus. A sintaxe da regra do filtro SQL para o Bus de Serviço é, no entanto, semelhante à sintaxe do seletor de mensagens suportada pelo JMS. 

O lado da editora JMS Topic é compatível com o Service Bus, como mostra esta amostra, mas os subscritores dinâmicos não são. As seguintes APIs relacionadas com topologia não são suportadas com o Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Características e restrições não suportadas
Existem as seguintes restrições ao utilizar o JMS sobre a AMQP 1.0 com o Service Bus, nomeadamente:

* Apenas uma **mensagem O produtor** ou **mensagemÉ** permitido por **Sessão**. Se precisar de criar vários **MessageProducers** ou **MessageConsumers** numa aplicação, crie uma **Sessão** dedicada para cada um deles.
* As subscrições de tópicos voláteis não são atualmente suportadas.
* **Os selecionadores** de mensagens não são atualmente suportados.
* As transações distribuídas não são suportadas (mas as sessões transacionadas são suportadas).

Além disso, o Azure Service Bus divide o plano de controlo do plano de dados e, portanto, não suporta várias das funções dinâmicas de topologia da JMS:

| Método não suportado          | Substituir                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| criarO Assinante Durável     | criar uma subscrição tópico poroting o seletor de mensagens                                 |
| criarDurableConsumidor       | criar uma subscrição tópico poroting o seletor de mensagens                                 |
| criarSharedConsumer        | Os tópicos de ônibus de serviço são sempre compartilháveis, ver acima                                       |
| criarSharedDurableConsumer | Os tópicos de ônibus de serviço são sempre compartilháveis, ver acima                                       |
| criarTópico temporário        | criar um tópico através da gestão API/ferramentas/portal com *autoDeleteOnIdle* definido para um período de expiração |
| criarTópico                 | criar um tópico através da gestão API/ferramentas/portal                                           |
| cancelar a subscrição                 | eliminar a gestão de tópicos API/ferramentas/portal                                             |
| criar Browser               | sem apoio. Utilize a funcionalidade Peek() da API do autocarro de serviço                         |
| criar Fila                 | criar uma fila através da gestão API/ferramentas/portal                                           | 
| criar Fila Temporária        | criar uma fila através da gestão API/tools/portal com *autoDeleteOnIdle* definido para um período de expiração |
| receberNoWait               | utilizar o método de receção() fornecido pelo Service Bus SDK e especificar um intervalo de tempo muito baixo ou nulo |

## <a name="summary"></a>Resumo
Este guia de como fazer mostrou como usar funcionalidades de mensagens mediadas service Bus (filas e tópicos de publicação/subscrição) de Java usando a popular API JMS e AMQP 1.0.

Também pode utilizar o Service Bus AMQP 1.0 de outras línguas, incluindo .NET, C, Python e PHP. Os componentes construídos utilizando estas diferentes línguas podem trocar mensagens de forma fiável e com total fidelidade utilizando o suporte AMQP 1.0 no Bus de Serviço.

## <a name="next-steps"></a>Passos seguintes
* [Suporte AMQP 1.0 em Ônibus de Serviço Azure](service-bus-amqp-overview.md)
* [Como utilizar a AMQP 1.0 com o Bus de Serviço .NET API](service-bus-dotnet-advanced-message-queuing.md)
* [Guia de desenvolvedor amqp 1.0 do ônibus de serviço](service-bus-amqp-dotnet.md)
* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Centro de Programadores do Java](https://azure.microsoft.com/develop/java/)

