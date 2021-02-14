---
title: Use filas de autocarros da Azure Service com Java (azure-messaging-servicebus)
description: Neste tutorial, aprende a usar a Java para enviar mensagens e receber mensagens de uma fila de autocarros da Azure Service. Usa o novo pacote de serviços de mensagens azure.
ms.devlang: Java
ms.topic: quickstart
ms.date: 02/13/2021
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: bfe7835bea4415085279fb77eb85d67ed3f5f0f3
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518611"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Envie mensagens para e receba mensagens das filas de autocarros da Azure Service (Java)
Neste arranque rápido, irá criar uma aplicação Java para enviar mensagens e receber mensagens de uma fila de autocarros da Azure Service. 

> [!IMPORTANT]
> Este quickstart utiliza o novo pacote de serviços de mensagens azure. Para obter um arranque rápido que utilize o antigo pacote de serviços azure, consulte [Enviar e receber mensagens usando o azure-servicebus](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Se não tiver uma fila para trabalhar, siga os passos no [portal Use Azure para criar um](service-bus-quickstart-portal.md) artigo de fila de Service Bus para criar uma fila. Note a **cadeia de ligação** para o seu espaço de nomes de Service Bus e o nome da **fila** que criou.
- Instale [o Azure SDK para Java][Azure SDK for Java]. Se estiver a utilizar o Eclipse, pode instalar o [Kit de Ferramentas Azure para Eclipse][Azure Toolkit for Eclipse] que inclui o Azure SDK para Java. Em seguida, pode adicionar as **Bibliotecas Microsoft Azure para Java** ao seu projeto. Se estiver a utilizar o IntelliJ, consulte [instalar o Kit de Ferramentas Azure para IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Nesta secção, irá criar um projeto de consola Java e adicionar código para enviar mensagens para a fila que criou anteriormente. 

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

### <a name="add-code-to-send-messages-to-the-queue"></a>Adicione código para enviar mensagens para a fila
1. Adicione as `import` seguintes declarações no tópico do ficheiro Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Na classe, defina variáveis para manter o fio de ligação e o nome da fila, como mostrado abaixo: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    `<NAMESPACE CONNECTION STRING>`Substitua-a pelo fio de ligação ao seu espaço de nomes de Service Bus. E, `<QUEUE NAME>` substitua-me pelo nome da fila.
3. Adicione um método nomeado `sendMessage` na classe para enviar uma mensagem para a fila. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
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
1. Adicione um método nomeado `sendMessageBatch` método para enviar mensagens para a fila que criou. Este método cria um `ServiceBusSenderClient` para a fila, invoca o `createMessages` método para obter a lista de mensagens, prepara um ou mais lotes e envia os lotes para a fila. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
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
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
Nesta secção, irá adicionar código para obter mensagens da fila. 

1. Adicione um método nomeado `receiveMessages` para receber mensagens da fila. Este método cria um `ServiceBusProcessorClient` para a fila especificando um manipulador para processar mensagens e outro para manusear erros. Em seguida, inicia o processador, aguarda por alguns segundos, imprime as mensagens que são recebidas e, em seguida, para e fecha o processador.

    > [!IMPORTANT]
    > Substitua `QueueTest` no código pelo nome da sua `QueueTest::processMessage` classe. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(QueueTest::processMessage)
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
2. Atualize o `main` método para `sendMessage` `sendMessageBatch` invocar, e `receiveMessages` métodos e lançar `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Executar a aplicação
Quando executar a aplicação, vê as seguintes mensagens na janela da consola. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Processing message. Session: 88d961dd801f449e9c3e0f8a5393a527, Sequence #: 1. Contents: Hello, World!
Processing message. Session: e90c8d9039ce403bbe1d0ec7038033a0, Sequence #: 2. Contents: First message
Processing message. Session: 311a216a560c47d184f9831984e6ac1d, Sequence #: 3. Contents: Second message
Processing message. Session: f9a871be07414baf9505f2c3d466c4ab, Sequence #: 4. Contents: Third message
Stopping and closing the processor
```

Na página **'Visão Geral'** do espaço de nomes do Service Bus no portal Azure, pode ver a contagem de mensagens **recebidas** e **de saída.** Pode ter de esperar um minuto ou assim e, em seguida, refrescar a página para ver os valores mais recentes. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Contagem de mensagens de entrada e saída" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Selecione a fila nesta página **de visão geral** para navegar na página **de Fila de autocarros** de serviço. Vê a **mensagem de entrada** e **saída** contar também nesta página. Também vê outras informações, como o **tamanho atual** da fila, **o tamanho máximo,** **a contagem de mensagens ativas,** e assim por diante. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Detalhes da fila" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Passos Seguintes
Consulte a seguinte documentação e amostras:

- [Biblioteca de clientes Azure Service Bus para Java - Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Amostras no GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Referência da API do Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage