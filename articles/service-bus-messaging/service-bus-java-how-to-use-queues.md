---
title: Use filas de autocarros da Azure Service com Java (azure-messaging-servicebus)
description: Neste tutorial, aprende a usar a Java para enviar mensagens e receber mensagens de uma fila de autocarros da Azure Service. Usa o novo pacote de serviços de mensagens azure.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: a91ed2a358a9595a4d22dd629b8d470423b786d6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95909531"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Envie mensagens para e receba mensagens das filas de autocarros da Azure Service (Java)
Neste arranque rápido, irá criar uma aplicação Java para enviar mensagens e receber mensagens de uma fila de autocarros da Azure Service. 

> [!IMPORTANT]
> Este quickstart utiliza o novo pacote azure-messaging-servicebus, que está em **pré-visualização**. Para obter um arranque rápido que utilize o pacote de serviços azure-servicebus atualmente disponível (GA), consulte [Enviar e receber mensagens usando o azure-servicebus](service-bus-java-how-to-use-queues-legacy.md).

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Se não tiver uma fila para trabalhar, siga os passos no [portal Use Azure para criar um](service-bus-quickstart-portal.md) artigo de fila de Service Bus para criar uma fila. Note a **cadeia de ligação** para o seu espaço de nomes de Service Bus e o nome da **fila** que criou.
- Instale [o Azure SDK para Java][Azure SDK for Java]. Se estiver a utilizar o Eclipse, pode instalar o [Kit de Ferramentas Azure para Eclipse][Azure Toolkit for Eclipse] que inclui o Azure SDK para Java. Em seguida, pode adicionar as **Bibliotecas Microsoft Azure para Java** ao seu projeto. Se estiver a utilizar o IntelliJ, consulte [instalar o Kit de Ferramentas Azure para IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Nesta secção, irá criar um projeto de consola Java e adicionar código para enviar mensagens para a fila que criou anteriormente. 

### <a name="create-a-java-console-project"></a>Criar um projeto de consola Java
Crie um projeto Java utilizando o Eclipse ou uma ferramenta à sua escolha. 

### <a name="configure-your-application-to-use-service-bus"></a>Configure a sua aplicação para utilizar o Service Bus
Adicione uma referência à biblioteca Azure Service Bus. A biblioteca de clientes Java para o Service Bus está disponível no [Repositório Central maven.](https://search.maven.org/search?q=a:azure-messaging-servicebus) Pode fazer referência a esta biblioteca utilizando a seguinte declaração de dependência dentro do seu ficheiro de projeto Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0-beta.7</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Adicione código para enviar mensagens para a fila
1. Adicione as `import` seguintes declarações no tópico do ficheiro Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
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

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString());
        };

        // handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
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
Received message: Hello, World!
Received message: First message in the batch
Received message: Second message in the batch
Received message: Three message in the batch
Stopping and closing the processor
```

Na página **'Visão Geral'** do espaço de nomes do Service Bus no portal Azure, pode ver a contagem de mensagens **recebidas** e **de saída.** Pode ter de esperar um minuto ou assim e, em seguida, refrescar a página para ver os valores mais recentes. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Contagem de mensagens de entrada e saída" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Selecione a fila nesta página **de visão geral** para navegar na página **de Fila de autocarros** de serviço. Vê a **mensagem de entrada** e **saída** contar também nesta página. Também vê outras informações, como o **tamanho atual** da fila, **o tamanho máximo,** **a contagem de mensagens ativas,** e assim por diante. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Detalhes da fila" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Passos Seguintes
Consulte a seguinte documentação e amostras:

- [Biblioteca de clientes Azure Service Bus para Java - Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Exemplos no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus/src/samples)
- [Referência java API](https://docs.microsoft.com/java/api/overview/azure/servicebus?view=azure-java-preview&preserve-view=true)

Ver [mais amostras no GitHub.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus) 

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
