---
title: Use AMQP com o Serviço de Mensagens Java API e ônibus de serviço Azure
description: Utilize o Serviço de Mensagens Java (JMS) com o ônibus de serviço Azure e o Protocolo avançado de Filamento de Mensagens (AMQP) 1.0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 97031abaedaa3e5595e290fa0292646feb744d47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90086696"
---
# <a name="use-the-java-message-service-with-azure-service-bus-and-amqp-10"></a>Utilize o Serviço de Mensagens Java com ônibus de serviço Azure e AMQP 1.0

> [!WARNING]
> Este artigo atende a *suporte limitado* para o Java Message Service (JMS) 1.1 API e existe apenas para o nível padrão do Azure Service Bus.
>
> O suporte total para o Java Message Service 2.0 API está disponível apenas no [nível premium do Azure Service Bus na pré-visualização.](how-to-use-java-message-service-20.md) Recomendamos que utilize este nível.
>

Este artigo explica como usar funcionalidades de mensagens Service Bus a partir de aplicações Java usando a popular norma JMS API. Estas funcionalidades de mensagens incluem filas e publicação ou subscrição de tópicos. Um [artigo de acompanhante](service-bus-amqp-dotnet.md) explica como fazer o mesmo utilizando o Azure Service Bus .NET API. Pode utilizar estes dois artigos em conjunto para aprender sobre mensagens trans plataformas utilizando o Protocolo avançado de Fila de Mensagens (AMQP) 1.0.

AMQP 1.0 é um protocolo de mensagens eficiente, fiável e de nível de fio que você pode usar para construir aplicações de mensagens robustas e inter-plataformas.

Suporte para AMQP 1.0 em Service Bus significa que você pode usar as funcionalidades de mensagens intermediadas de uma série de plataformas usando um protocolo binário eficiente. Também é possível construir aplicações compostas por componentes construídos utilizando uma mistura de idiomas, quadros e sistemas operativos.

## <a name="get-started-with-service-bus"></a>Introdução ao Service Bus

Este artigo pressupõe que já tem um espaço de nomes de Service Bus que contém uma fila chamada `basicqueue` . Se não o fizer, pode [criar o espaço de nome e a fila](service-bus-create-namespace-portal.md) utilizando o portal [Azure](https://portal.azure.com). Para obter mais informações sobre como criar espaços e filas de nomes de Service Bus, consulte [Começar com as filas do Service Bus.](service-bus-dotnet-get-started-with-queues.md)

> [!NOTE]
> Filas e tópicos divididos também apoiam AMQP. Para obter mais informações, consulte [as entidades de mensagens Partitioned](service-bus-partitioning.md) e [o suporte amQP 1.0 para filas e tópicos divididos do Service Bus.](./service-bus-amqp-protocol-guide.md)
> 
> 

## <a name="download-the-amqp-10-jms-client-library"></a>Descarregue a biblioteca de clientes AMQP 1.0 JMS

Para obter informações sobre onde descarregar a versão mais recente da biblioteca cliente Apache Qpid JMS AMQP 1.0, consulte o [site de descarregamento Apache Qpid](https://qpid.apache.org/download.html).

Deve adicionar os seguintes ficheiros JAR do arquivo de distribuição Apache Qpid JMS AMQP 1.0 à variável ambiente Java CLASSPATH quando constrói e executou aplicações JMS com Service Bus:

* geronimo-jms \_ 1.1 \_ spec-1.0.jar
* qpid-jms-cliente-[versão].jar

> [!NOTE]
> Os nomes e versões JMS JAR podem ter mudado. Para mais informações, consulte [Qpid JMS AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="code-java-applications"></a>Aplicações code Java

### <a name="java-naming-and-directory-interface"></a>Interface java naming e diretório

JMS usa a Interface de Nomeação e Diretório Java (JNDI) para criar uma separação entre nomes lógicos e nomes físicos. Dois tipos de objetos JMS são resolvidos utilizando jNDI: **ConnectionFactory** e **Destination**. A JNDI utiliza um modelo de fornecedor no qual pode ligar diferentes serviços de diretório para lidar com os deveres de resolução de nomes. A biblioteca Apache Qpid JMS AMQP 1.0 vem com um fornecedor JNDI baseado em ficheiros de propriedade simples que é configurado usando um ficheiro de propriedades do seguinte formato:

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

#### <a name="set-up-jndi-context-and-configure-the-connectionfactory-object"></a>Configurar o contexto JNDI e configurar o objeto ConnectionFactory

A cadeia de ligação referenciada é a que está disponível nas Políticas de Acesso Partilhado no [portal Azure](https://portal.azure.com) sob **a Cadeia de Ligação Primária**.

```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Configure as filas de produtores e de destino ao consumidor

A entrada utilizada para definir um destino no ficheiro de propriedades Qpid JNDI é do seguinte formato.

Para criar uma fila de destino para o produtor:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create producer
MessageProducer producer = session.createProducer(queue);
```

Para criar uma fila de destino para o consumidor:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Escreva a aplicação JMS

Não são necessárias APIs ou opções especiais quando utilizar o JMS com o Service Bus. Há algumas restrições que serão abrangidas mais tarde. Como em qualquer aplicação JMS, a primeira coisa necessária é a configuração do ambiente JNDI para ser capaz de resolver um objeto e destinos **ConnectionFactory.**

#### <a name="configure-the-jndi-initialcontext-object"></a>Configure o objeto JNDI InitialContext

O ambiente JNDI é configurado passando uma tabela de haxixe de informação de configuração para o construtor da classe tialContext javax.naming.Ini. Os dois elementos necessários na tabela de haxixe são o nome de classe da Fábrica de Contexto Inicial e do URL do fornecedor. O código que se segue mostra como configurar o ambiente JNDI para utilizar o fornecedor JNDI baseado em ficheiros Qpid com um ficheiro de propriedades chamado **servicebus.properties**.

```java
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-that-uses-a-service-bus-queue"></a>Uma aplicação JMS simples que usa uma fila de ônibus de serviço

O seguinte programa de exemplo envia mensagens de texto JMS para uma fila de ônibus de serviço com o nome lógico JNDI de QUEUE e recebe as mensagens de volta.

Pode aceder a todas as informações de código de origem e configuração a partir do [Azure Service Bus que mostra o arranque rápido da fila JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart).

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
 * This sample demonstrates how to send messages from a JMS queue producer into
 * an Azure Service Bus queue and receive them with a JMS message consumer.
 * JMS queue. 
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
        
        // Set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // We create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter.
        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create session, no transaction, client ack
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
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // Create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // Received message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // Wait on the main thread until all sent messages have been received
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

            // Parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // Get overrides from the environment
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
A seguinte saída é do formulário que executa a aplicação:

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

Eis como uma disposição amQP se traduz numa operação de autocarro de serviço:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Tópicos JMS vs. Service Bus tópicos

A utilização de tópicos e subscrições do Service Bus através da API JMS fornece capacidades básicas de envio e receção. É uma escolha conveniente quando você porta aplicações de outros corretores de mensagens com APIs compatíveis com JMS, mesmo que os tópicos de Service Bus diferem dos tópicos JMS e exijam alguns ajustes.

Os tópicos do Service Bus encaminham mensagens para subscrições nomeadas, partilhadas e duráveis que são geridas através da interface Azure Resource Management, das ferramentas da linha de comando Azure ou do portal Azure. Cada subscrição permite até 2.000 regras de seleção, cada uma das quais pode ter uma condição de filtro e, para filtros SQL, também uma ação de transformação de metadados. Cada correspondência de condição de filtro seleciona a mensagem de entrada a ser copiada na subscrição.  

Receber mensagens de subscrições é idêntico a receber mensagens de filas. Cada subscrição tem uma fila de letras mortas associada e a capacidade de encaminhar automaticamente mensagens para outra fila ou tópicos.

Os tópicos JMS permitem aos clientes criar dinâmicas subscritores não duradouros e duráveis que, opcionalmente, permitam filtrar mensagens com seletores de mensagens. Estas entidades não partilhadas não são apoiadas pela Service Bus. A sintaxe da regra do filtro SQL para o Service Bus é semelhante à sintaxe do seletor de mensagens suportada por JMS.

O lado da editora de tópicos JMS é compatível com o Service Bus, como mostrado nesta amostra, mas os subscritores dinâmicos não são. As seguintes APIs relacionadas com topologia não são suportadas com o Service Bus.

## <a name="unsupported-features-and-restrictions"></a>Funcionalidades e restrições não suportadas

Existem as seguintes restrições quando utiliza o JMS em AMQP 1.0 com o Service Bus, nomeadamente:

* Apenas é permitido um **mensagemProdutor** ou **MensagemConsumer** por sessão. Se precisar de criar vários objetos **MessageProducer** ou **MessageConsumer** numa aplicação, crie uma sessão dedicada para cada um deles.
* As subscrições de tópicos voláteis não são suportadas atualmente.
* **MensagensOs** objetos de controlo não são suportados atualmente.
* As transações distribuídas não são suportadas, mas as sessões transacionadas são apoiadas.

A Service Bus divide o plano de controlo do plano de dados, por isso não suporta várias das funções dinâmicas de topologia da JMS.

| Método não suportado          | Substituir                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Crie uma subscrição de tópico que envie o seletor de mensagens.                                |
| criarDurableConsumer       | Crie uma subscrição de tópico que envie o seletor de mensagens.                                |
| createPartindo        | Os tópicos do Service Bus são sempre partilháveis. Consulte a secção "Tópicos JMS vs. Service Bus tópicos".                                    |
| createSharedDurableConsumer | Os tópicos do Service Bus são sempre partilháveis. Consulte a secção "Tópicos JMS vs. Service Bus tópicos".                                      |
| createSteporaryTopic        | Crie um tópico através da API de gestão, ferramentas ou o portal com *AutoDeleteOnIdle* definido para um período de expiração. |
| criarTopic                 | Crie um tópico através da API de gestão, ferramentas ou portal.                                         |
| cancelar a subscrição                 | Elimine a API de gestão de tópicos, ferramentas ou portal.                                            |
| criarRowser               | Sem apoio. Utilize a funcionalidade Peek() da API do Service Bus.                         |
| criarQueue                 | Crie uma fila através da API de gestão, ferramentas ou portal.                                           | 
| createSteporaryQueue        | Crie uma fila através da API de gestão, ferramentas ou o portal com *AutoDeleteOnIdle* definido para um período de expiração. |
| receberNoWait               | Utilize o método de receção fornecido pelo Service Bus SDK e especifique um tempo limite muito baixo ou nulo. |

## <a name="summary"></a>Resumo

Este artigo mostrou-lhe como usar funcionalidades de mensagens intermediadas do Service Bus, tais como filas e publicar ou subscrever tópicos, de Java, utilizando os populares JMS API e AMQP 1.0.

Também pode utilizar o Service Bus AMQP 1.0 de outras línguas, tais como .NET, C, Python e PHP. Os componentes construídos utilizando estes diferentes idiomas podem trocar mensagens de forma fiável e plena fidelidade utilizando o suporte AMQP 1.0 em Service Bus.

## <a name="next-steps"></a>Passos seguintes

* [Suporte AMQP 1.0 em Azure Service Bus](service-bus-amqp-overview.md)
* [Utilize AMQP 1.0 com o Service Bus .NET API](./service-bus-amqp-dotnet.md)
* [Guia do desenvolvedor do Service Bus AMQP 1.0](service-bus-amqp-dotnet.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Centro de desenvolvimento de Java](https://azure.microsoft.com/develop/java/)