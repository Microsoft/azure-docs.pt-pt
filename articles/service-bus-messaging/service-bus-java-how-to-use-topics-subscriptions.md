---
title: Use tópicos e subscrições do Azure Service Bus com a Java (azure-messaging-servicebus)
description: Neste quickstart, você escreve código Java usando o pacote azure-messaging-servicebus para enviar mensagens para um tópico Azure Service Bus e, em seguida, receber mensagens de subscrições para esse tópico.
ms.devlang: Java
ms.topic: quickstart
ms.date: 02/13/2021
ms.openlocfilehash: c5b930fb2c87a09a1f4801365936c62a7cf79f1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516180"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Envie mensagens para um tópico do Azure Service Bus e receba mensagens de subscrições ao tema (Java)
Neste quickstart, você escreve código Java usando o pacote azure-messaging-servicebus para enviar mensagens para um tópico Azure Service Bus e, em seguida, receber mensagens de subscrições para esse tópico.

> [!IMPORTANT]
> Este quickstart utiliza o novo pacote de serviços de mensagens azure. Para obter um arranque rápido que utilize o antigo pacote de serviços azure, consulte [Enviar e receber mensagens usando o azure-servicebus](service-bus-java-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Siga os passos no [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrições do tema](service-bus-quickstart-topics-subscriptions-portal.md). Note a cadeia de ligação, o nome do tópico e um nome de subscrição. Usará apenas uma subscrição para este arranque rápido. 
- Instale [o Azure SDK para Java][Azure SDK for Java]. Se estiver a utilizar o Eclipse, pode instalar o [Kit de Ferramentas Azure para Eclipse][Azure Toolkit for Eclipse] que inclui o Azure SDK para Java. Em seguida, pode adicionar as **Bibliotecas Microsoft Azure para Java** ao seu projeto. Se estiver a utilizar o IntelliJ, consulte [instalar o Kit de Ferramentas Azure para IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Nesta secção, irá criar um projeto de consola Java e adicionar código para enviar mensagens ao tópico que criou. 

### <a name="create-a-java-console-project"></a>Criar um projeto de consola Java
Crie um projeto Java utilizando o Eclipse ou uma ferramenta à sua escolha. 

### <a name="configure-your-application-to-use-service-bus"></a>Configure a sua aplicação para utilizar o Service Bus
Adicione referências às bibliotecas Azure Core e Azure Service Bus. 

Se estiver a utilizar o Eclipse e tiver criado uma aplicação de consola Java, converta o seu projeto Java num Maven: clique à direita no projeto na janela Do Explorador de **Pacotes,** selecione **Configure**  ->  **Convert to Maven project**. Em seguida, adicione dependências a estas duas bibliotecas, como mostra o exemplo seguinte.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>Adicionar código para enviar mensagens para o tópico
1. Adicione as `import` seguintes declarações no tópico do ficheiro Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Na classe, defina variáveis para manter a cadeia de ligação e o nome do tópico como mostrado abaixo: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    `<NAMESPACE CONNECTION STRING>`Substitua-a pelo fio de ligação ao seu espaço de nomes de Service Bus. E, `<TOPIC NAME>` substitua-me pelo nome do tópico.
3. Adicione um método nomeado `sendMessage` na classe para enviar uma mensagem para o tópico. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
    }
    ```
1. Adicione um método nomeado `createMessages` na classe para criar uma lista de mensagens. Normalmente, obtém estas mensagens de diferentes partes da sua aplicação. Aqui, criamos uma lista de mensagens de amostra.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Adicione um método nomeado `sendMessageBatch` método para enviar mensagens para o tópico que criou. Este método cria um `ServiceBusSenderClient` para o tópico, invoca o `createMessages` método para obter a lista de mensagens, prepara um ou mais lotes, e envia os lotes para o tópico. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
Nesta secção, irá adicionar código para obter mensagens de uma subscrição do tópico. 

1. Adicione um método nomeado `receiveMessages` para receber mensagens da subscrição. Este método cria um `ServiceBusProcessorClient` para a subscrição especificando um manipulador para processar mensagens e outro para manipulação de erros. Em seguida, inicia o processador, aguarda por alguns segundos, imprime as mensagens que são recebidas e, em seguida, para e fecha o processador.

    > [!IMPORTANT]
    > Substitua `ServiceBusTopicTest` no código pelo nome da sua `ServiceBusTopicTest::processMessage` classe. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(ServiceBusTopicTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }  
    ```
2. Adicione o `processMessage` método para processar uma mensagem recebida da assinatura Service Bus. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Adicione o `processError` método para lidar com mensagens de erro.

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
    }  
    ```
1. Atualize o `main` método para `sendMessage` `sendMessageBatch` invocar, e `receiveMessages` métodos e lançar `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Executar a aplicação
Executar o programa para ver a saída semelhante à seguinte saída:

```console
Sent a single message to the topic: mytopic
Sent a batch of messages to the topic: mytopic
Starting the processor
Processing message. Session: e0102f5fbaf646988a2f4b65f7d32385, Sequence #: 1. Contents: Hello, World!
Processing message. Session: 3e991e232ca248f2bc332caa8034bed9, Sequence #: 2. Contents: First message
Processing message. Session: 56d3a9ea7df446f8a2944ee72cca4ea0, Sequence #: 3. Contents: Second message
Processing message. Session: 7bd3bd3e966a40ebbc9b29b082da14bb, Sequence #: 4. Contents: Third message
```

Na página **'Visão Geral'** do espaço de nomes do Service Bus no portal Azure, pode ver a contagem de mensagens **recebidas** e **de saída.** Pode ter de esperar um minuto ou assim e, em seguida, refrescar a página para ver os valores mais recentes. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Contagem de mensagens de entrada e saída" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Mude para o separador **Tópicos** no painel de fundo médio e selecione o tópico para ver a página **Tópico do Autocarro de Serviço** para o seu tópico. Nesta página, deverá ver quatro mensagens recebidas e quatro de saída na tabela **Mensagens.** 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Mensagens de entrada e saída" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Se comentar a `receiveMessages` chamada no método e executar `main` novamente a aplicação, na página **De Tópico de Autocarro de Serviço,** vê 8 mensagens recebidas (4 novas) mas quatro mensagens de saída. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Página de tópico atualizada" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

Nesta página, se selecionar uma subscrição, você chega à página **de Subscrição de Serviço Bus.** Pode ver a contagem de mensagens ativas, a contagem de mensagens mortas e muito mais nesta página. Neste exemplo, há quatro mensagens ativas que ainda não foram recebidas por um recetor. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Contagem de mensagens ativas" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte documentação e amostras:

- [Biblioteca de clientes Azure Service Bus para Java - Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Amostras no GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Referência da API do Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage