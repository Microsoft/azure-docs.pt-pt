---
title: Envie mensagens para tópicos do Azure Service Bus usando o azure-messaging-servicebus
description: Este quickstart mostra-lhe como enviar mensagens para tópicos da Azure Service Bus usando o pacote azure-messaging-servicebus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5ddfca1186025f4118013815af7bbf7f500951ce
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95809261"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Envie mensagens para um tópico do Azure Service Bus e receba mensagens de subscrições ao tema (.NET)
Este tutorial mostra-lhe como criar uma aplicação de consola .NET Core que envia mensagens para um tópico de Service Bus e recebe mensagens de uma subscrição do tópico. 

> [!Important]
> Este quickstart utiliza o novo pacote **Azure.Messaging.ServiceBus.** Para obter um arranque rápido que utilize o antigo pacote Microsoft.Azure.ServiceBus, consulte [Enviar e receber mensagens utilizando o pacote Microsoft.Azure.ServiceBus](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Siga os passos no [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrições do tema](service-bus-quickstart-topics-subscriptions-portal.md). Note a cadeia de ligação, o nome do tópico e um nome de subscrição. Usará apenas uma subscrição para este arranque rápido. 

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Nesta secção, irá criar uma aplicação de consola .NET Core no Visual Studio, adicionar código para enviar mensagens ao tópico que criou. 

### <a name="create-a-console-application"></a>Criar uma aplicação de consola
Lançar Visual Studio e criar um novo projeto **de App de Consola (.NET Core)** para C#. 

### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Service Bus

1. Clique com o botão direito do rato no projeto recém-criado e selecione **Gerir Pacotes NuGet**.
1. Selecione **Procurar**. Procure e selecione **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)**.
1. Selecione **instalar** para concluir a instalação e, em seguida, fechar o Gestor de Pacotes NuGet.

### <a name="add-code-to-send-messages-to-the-topic"></a>Adicionar código para enviar mensagens para o tópico 

1. Em Program.cs, adicione as seguintes `using` declarações na parte superior da definição de espaço de nomes, antes da declaração de classe:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. Na `Program` classe, declare as seguintes variáveis:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<TOPIC NAME>";
        static string subscriptionName = "<SUBSCRIPTION NAME>";
    ```

    Substitua os seguintes valores:
    - `<NAMESPACE CONNECTION STRING>` com a cadeia de ligação ao seu espaço de nomes de Service Bus
    - `<TOPIC NAME>` com o nome do tópico
    - `<SUBSCRIPTION NAME>` com o nome da subscrição
2. Adicione um método chamado `SendMessageToTopicAsync` que envia uma mensagem para o tópico. 

    ```csharp
        static async Task SendMessageToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the topic
                ServiceBusSender sender = client.CreateSender(topicName);
                await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
                Console.WriteLine($"Sent a single message to the topic: {topicName}");
            }
        }
    ```
1. Adicione um método nomeado `CreateMessages` para criar uma fila (.fila NET) de mensagens à `Program` classe. Normalmente, obtém estas mensagens de diferentes partes da sua aplicação. Aqui, criamos uma fila de mensagens de amostra.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. Adicione um método nomeado `SendMessageBatchAsync` à classe e adicione o seguinte `Program` código. Este método requer uma fila de mensagens e prepara um ou mais lotes para enviar para o tópico do Service Bus. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }

                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);

                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```
1. Substitua o `Main()` método pelo seguinte método **assíco.** `Main` Chama os métodos de envio para enviar uma única mensagem e um lote de mensagens para o tópico.  

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();
        }
    ```
5. Execute a aplicação. Deverá ver o seguinte resultado:

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. No portal Azure, siga estes passos:
    1. Navegue para o seu espaço de nomes de Service Bus. 
    1. Na página **'Vista Geral',** no painel de baixo-médio, mude para o **separador Tópicos** e selecione o tópico 'Service Bus'. No exemplo seguinte, `mytopic` é.
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Selecione tópico":::
    1. Na página **'Tópico',** na tabela **mensagens** na secção **Métricas inferiores,** pode ver que existem quatro mensagens recebidas para o tópico. Se não vir o valor, aguarde alguns minutos e refresque a página para ver o gráfico atualizado. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="Mensagens enviadas para o tópico" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Selecione a subscrição no painel inferior. No exemplo seguinte, é **S1.** Na página **de Subscrição de Autocarros** de Serviço, vê a **contagem de mensagens Ative** como **4**. A subscrição recebeu as quatro mensagens que enviou para o tema, mas ainda nenhum recetor as escolheu. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Mensagens recebidas na subscrição" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição

1. Adicione os seguintes métodos à `Program` classe que lida com mensagens e quaisquer erros. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Adicione o seguinte método `ReceiveMessagesFromSubscriptionAsync` à `Program` classe.

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```
1. Adicione uma chamada ao `ReceiveMessagesFromSubscriptionAsync` método ao `Main` método. Comente o `SendMessagesToTopicAsync` método se quiser testar apenas receber mensagens. Se não o fizeres, vês mais quatro mensagens enviadas para o tema. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>Executar a aplicação
Execute a aplicação. Aguarde um minuto e, em seguida, prima qualquer tecla para parar de receber mensagens. Deve ver a seguinte saída (barra espacial para a chave). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

Verifique o portal de novo. 

- Na página **'Tópico',** na tabela **Mensagens,** vê-se oito mensagens recebidas e oito mensagens de saída. Se não vir estes números, aguarde alguns minutos e refresque a página para ver o gráfico atualizado. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Mensagens enviadas e recebidas" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- Na página **de Subscrição de Autocarros** de Serviço, vê a contagem de **mensagens Ative** como zero. É porque um recetor recebeu mensagens desta subscrição e completou as mensagens. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Contagem de mensagens ativas na subscrição no final" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte documentação e amostras:

- [Biblioteca de clientes Azure Service Bus para .NET - Readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Exemplos no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Referência da API .NET](https://docs.microsoft.com/dotnet/api/azure.messaging.servicebus?view=azure-dotnet-preview&preserve-view=true)

