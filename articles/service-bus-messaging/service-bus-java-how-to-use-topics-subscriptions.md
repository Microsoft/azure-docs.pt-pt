---
title: Use tópicos e subscrições do Azure Service Bus com a Java (azure-messaging-servicebus)
description: Neste quickstart, você escreve código Java usando o pacote azure-messaging-servicebus para enviar mensagens para um tópico Azure Service Bus e, em seguida, receber mensagens de subscrições para esse tópico.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: ab0ba2bb2814ba12a49f4044cbbb7a0ce56f5fc8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452599"
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
Adicione uma referência à biblioteca Azure Service Bus. A biblioteca de clientes Java para o Service Bus está disponível no [Repositório Central maven.](https://search.maven.org/search?q=a:azure-messaging-servicebus) Pode fazer referência a esta biblioteca utilizando a seguinte declaração de dependência dentro do seu ficheiro de projeto Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>Adicionar código para enviar mensagens para o tópico
1. Adicione as `import` seguintes declarações no tópico do ficheiro Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
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

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // Consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString() + " from the subscription: " + subName);
        };

        // Consumer that handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
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
Executar o programa para ver a saída semelhante à seguinte saída:

```console
Sent a batch of messages to the topic: mytopic
Starting the processor
Received message: First message from the subscription: mysub
Received message: Second message from the subscription: mysub
Received message: Third message from the subscription: mysub
Stopping and closing the processor
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
- [Exemplos no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus/src/samples)
- [Referência java API](https://docs.microsoft.com/java/api/overview/azure/servicebus?view=azure-java-preview&preserve-view=true)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

