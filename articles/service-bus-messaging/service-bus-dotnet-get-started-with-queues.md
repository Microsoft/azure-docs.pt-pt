---
title: Começa com as filas de autocarros da Azure Service (Azure.Messaging.ServiceBus)
description: Neste tutorial, cria uma aplicação .NET Core C# para enviar mensagens e receber mensagens de uma fila de Autocarros de Serviço.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ec3f53e6f69614028c013efa5f0e6852cbc3f8ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98631645"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>Enviar mensagens para e receber mensagens das filas do Azure Service Bus (.NET)
Neste tutorial, cria uma aplicação de consola .NET Core para enviar mensagens e receber mensagens de uma fila de Autocarros de Serviço utilizando o pacote **Azure.Messaging.ServiceBus.** 

> [!Important]
> Este quickstart utiliza o novo pacote Azure.Messaging.ServiceBus. Para obter um arranque rápido que utilize o antigo pacote Microsoft.Azure.ServiceBus, consulte [Enviar e receber eventos utilizando o pacote Microsoft.Azure.ServiceBus](service-bus-dotnet-get-started-with-queues-legacy.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Se não tiver uma fila para trabalhar, siga os passos no [portal Use Azure para criar um](service-bus-quickstart-portal.md) artigo de fila de Service Bus para criar uma fila. Note a **cadeia de ligação** para o seu espaço de nomes de Service Bus e o nome da **fila** que criou.

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Neste arranque rápido, irá criar uma aplicação de consola C# .NET Core para enviar mensagens para a fila.

### <a name="create-a-console-application"></a>Criar uma aplicação de consola
Lançar Visual Studio e criar um novo projeto **de App de Consola (.NET Core)** para C#. 

### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Service Bus

1. Clique com o botão direito do rato no projeto recém-criado e selecione **Gerir Pacotes NuGet**.
1. Selecione **Procurar**. Procure e selecione **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)**.
1. Selecione **instalar** para concluir a instalação e, em seguida, fechar o Gestor de Pacotes NuGet.

### <a name="add-code-to-send-messages-to-the-queue"></a>Adicione código para enviar mensagens para a fila

1. No *Programa.cs*, adicione as `using` seguintes declarações no topo da definição de espaço de nome, antes da declaração de classe:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. Na `Program` classe, declare as seguintes variáveis:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    Introduza a sua cadeia de ligação para o espaço de nome como `connectionString` variável. Insira o nome da sua fila.

1. Imediatamente após o `Main()` método, adicione o seguinte `SendMessagesAsync()` método que faz o trabalho de enviar uma mensagem:

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
            }
        }
    ```
1. Adicione um método nomeado `CreateMessages` para criar uma fila (.fila NET) de mensagens à `Program` classe. Normalmente, obtém estas mensagens de diferentes partes da sua aplicação. Aqui, criamos uma fila de mensagens de amostra.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Second message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Third message in the batch"));
            return messages;
        }
    ```
1. Adicione um método nomeado `SendMessageBatchAsync` à classe e adicione o seguinte `Program` código. Este método requer uma fila de mensagens e prepara um ou mais lotes para enviar para a fila do Service Bus. 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
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

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. Substitua o `Main()` método pelo seguinte método **assíco.** `Main` Chama os métodos de envio para enviar uma única mensagem e um lote de mensagens para a fila. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. Execute a aplicação. Devia ver as seguintes mensagens. 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. No portal Azure, siga estes passos:
    1. Navegue para o seu espaço de nomes de Service Bus. 
    1. Na página **'Vista Geral',** selecione a fila no painel de baixo-médio. 
    1. Observe os valores na secção **Essencial.**

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="Mensagens recebidas com contagem e tamanho" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    Atenção aos seguintes valores:
    - O valor **da contagem de mensagens Ative** para a fila é agora de **4**. Cada vez que executam esta aplicação de remetente sem recuperar as mensagens, este valor aumenta em 4.
    - O tamanho atual da fila aumenta o valor **CURRENT** em **Essentials**  cada vez que a aplicação adiciona mensagens à fila.
    - Na tabela **mensagens** na secção **Métricas inferiores,** pode ver que há quatro mensagens recebidas para a fila. 

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
Nesta secção, irá adicionar código para obter mensagens da fila.

1. Adicione os seguintes métodos à `Program` classe que lida com mensagens e quaisquer erros. 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Adicione um método nomeado `ReceiveMessagesAsync` à classe e adicione o seguinte código para receber `Program` mensagens. 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

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
1. Adicione uma chamada ao `ReceiveMessagesAsync` método a partir do `Main` método. Comente o `SendMessagesAsync` método se quiser testar apenas receber mensagens. Se não o fizeres, vês mais quatro mensagens enviadas para a fila. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>Executar a aplicação
Execute a aplicação. Aguarde um minuto e, em seguida, prima qualquer tecla para parar de receber mensagens. Deve ver a seguinte saída (barra espacial para a chave). 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

Verifique o portal de novo. 

- A **contagem de mensagens Ative** e os valores **CURRENT** são agora **0**.
- Na tabela **mensagens** na secção **Métricas inferiores,** pode ver que existem oito mensagens recebidas e oito mensagens de saída para a fila. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="Mensagens ativas e tamanho após receber" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte documentação e amostras:

- [Biblioteca de clientes Azure Service Bus para .NET - Readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Amostras no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Referência da API .NET](/dotnet/api/azure.messaging.servicebus?preserve-view=true)