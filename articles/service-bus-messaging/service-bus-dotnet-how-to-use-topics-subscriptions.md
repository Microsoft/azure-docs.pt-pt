---
title: Envie mensagens para tópicos do Azure Service Bus usando o azure-messaging-servicebus
description: Este quickstart mostra-lhe como enviar mensagens para tópicos da Azure Service Bus usando o pacote azure-messaging-servicebus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 03/16/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 79eb7783fd3daf546539dd5b9048f4e9f484374f
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279804"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Envie mensagens para um tópico do Azure Service Bus e receba mensagens de subscrições ao tema (.NET)
Neste tutorial, cria-se uma aplicação C# para fazer as seguintes tarefas:

1. Envie mensagens para um tópico de Serviço Bus. 

    Um tópico de ônibus de serviço fornece um ponto final para aplicações de remetente para enviar mensagens. Um tópico pode ter uma ou mais subscrições. Cada subscrição de um tópico recebe uma cópia da mensagem enviada para o tópico. Para mais informações sobre os tópicos do Service Bus, veja [o que é a Azure Service Bus?](service-bus-messaging-overview.md) 
1. Receba mensagens de uma subscrição do tópico. 

    :::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="Tópicos e subscrições do Service Bus":::

    > [!Important]
    > Este quickstart utiliza o novo pacote **Azure.Messaging.ServiceBus.** Se estiver a utilizar o antigo pacote Microsoft.Azure.ServiceBus, consulte [Enviar e receber mensagens utilizando o pacote Microsoft.Azure.ServiceBus](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Siga os passos neste [Quickstart](service-bus-quickstart-topics-subscriptions-portal.md) para criar um tópico de Service Bus e subscrições do tema. 

    > [!NOTE]
    > Você usará a cadeia de conexão para o espaço de nome, o nome do tópico, e o nome de uma das subscrições do tópico neste tutorial.  
- [Visual Studio 2019](https://www.visualstudio.com/vs). 
 
## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Nesta secção, irá criar uma aplicação de consola .NET Core no Visual Studio, adicionar código para enviar mensagens para o tópico Service Bus que criou. 

### <a name="create-a-console-application"></a>Criar uma aplicação de consola
Crie uma aplicação de consola .NET Core utilizando o Visual Studio. 

1. Lançamento Visual Studio.  
1. Se vir a página **'Iniciar',** **selecione Criar um novo projeto**. 
1. Na página **criar um novo projeto,** siga estes passos: 
    1. Para a linguagem de programação, selecione **C#**. 
    1. Para o tipo de projeto, selecione **Consola.** 
    1. Selecione **App de Consola (.NET Core)** da lista de modelos. 
    1. Em seguida, selecione **Seguinte**. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project.png" alt-text="Criar um projeto de aplicativo de consola":::
1. Na **página Configure a sua nova** página de projeto, siga estes passos: 
    1. Para **o nome do projeto,** insira um nome para o projeto. 
    1. Para **localização,** selecione uma localização para os ficheiros do projeto e da solução. 
    1. Para **o nome Solução,** insira um nome para a solução. Uma solução visual Studio pode ter um ou mais projetos. Neste arranque rápido, a solução terá apenas um projeto. 
    1. Selecione **Create** (Criar) para criar o projeto. 
            
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project-2.png" alt-text="Insira o nome e a localização do projeto e da solução":::    


### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Service Bus
1. Clique com o botão direito do rato no projeto recém-criado e selecione **Gerir Pacotes NuGet**.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/manage-nuget-packages-menu.png" alt-text="Gerir o menu de Pacotes NuGet":::        
1. Mude para o separador **Procurar.**
1. Procure e selecione **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)**.
1. Selecione **Instalar** para concluir a instalação.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-service-bus-package.png" alt-text="Selecione pacote de nuget de serviço de serviço.":::
5. Se vir a caixa de diálogo **'Alterações de pré-visualização',** selecione **OK** para continuar. 
1. Se vir a página **de Aceitação da Licença,** selecione **I Accept** to continue. 
    

### <a name="add-code-to-send-messages-to-the-topic"></a>Adicionar código para enviar mensagens para o tópico 

1. Na janela **Solution Explorer,** clique duas vezes **no Programa.cs** para abrir o ficheiro no editor. 
1. Adicione as `using` seguintes declarações no topo da definição de espaço de nome, antes da declaração de classe:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. Na `Program` classe, acima da `Main` função, declarem as seguintes variáveis:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<SERVICE BUS TOPIC NAME>";
        static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    ```

    Substitua os seguintes valores:
    - `<NAMESPACE CONNECTION STRING>` com a cadeia de ligação ao seu espaço de nomes de Service Bus
    - `<TOPIC NAME>` com o nome do tópico
    - `<SUBSCRIPTION NAME>` com o nome da subscrição

### <a name="send-a-single-message-to-the-topic"></a>Envie uma única mensagem para o tema
Adicione um método nomeado `SendMessageToTopicAsync` à classe acima ou `Program` abaixo do `Main` método. Este método envia uma única mensagem para o tópico.

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

Este método faz os seguintes passos: 
1. Cria um objeto [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) utilizando a cadeia de ligação ao espaço de nomes. 
1. Utiliza o `ServiceBusClient` objeto para criar um objeto [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) para o tópico de ônibus de serviço especificado. Este passo utiliza o método [ServiceBusClient.CreateSender.](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender)
1. Em seguida, o método envia uma única mensagem de teste para o tópico Service Bus utilizando o método [ServiceBusSender.SendMessageAsync.](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessageasync) 

### <a name="send-a-batch-of-messages-to-the-topic"></a>Envie um lote de mensagens para o tema
1. Adicione um método nomeado `CreateMessages` para criar uma fila (.NET, não fila de ônibus de serviço) de mensagens para a `Program` classe. Normalmente, obtém estas mensagens de diferentes partes da sua aplicação. Aqui, criamos uma fila de mensagens de amostra. Se não estiver familiarizado com as filas .NET, consulte [Queue.Enqueue](/dotnet/api/system.collections.queue.enqueue).

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
1. Adicione um método chamado `SendMessageBatchAsync` classe e adicione o seguinte `Program` código. Este método requer uma fila de mensagens e prepara um ou mais lotes para enviar para o tópico do Service Bus. 

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

    Aqui estão os passos importantes do código:
    1. Cria um objeto [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) utilizando a cadeia de ligação ao espaço de nomes. 
    1. Invoca o método [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) no `ServiceBusClient` objeto para criar um objeto [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) para o tópico de Service Bus especificado. 
    1. Invoca o método do ajudante `GetMessages` para obter uma fila de mensagens a enviar para o tópico do Service Bus. 
    1. Cria um [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) utilizando o [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync).
    1. Adicione mensagens ao lote utilizando o [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage). À medida que as mensagens são adicionadas ao lote, são removidas da fila .NET. 
    1. Envia o lote de mensagens para o tópico Service Bus utilizando o método [ServiceBusSender.SendMessagesAsync.](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync)

### <a name="update-the-main-method"></a>Atualizar o método principal
Substitua o `Main()` método pelo seguinte método **assíco.** `Main` Chama os métodos de envio para enviar uma única mensagem e um lote de mensagens para o tópico.  

```csharp
    static async Task Main()
    {
        // send a single message to the topic
        await SendMessageToTopicAsync();

        // send a batch of messages to the topic
        await SendMessageBatchToTopicAsync();
    }
```

### <a name="test-the-app-to-send-messages-to-the-topic"></a>Teste a app para enviar mensagens para o tópico
1. Execute a aplicação. Deverá ver o seguinte resultado:

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

    Aqui estão os passos importantes do código:
    1. Cria um objeto [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) utilizando a cadeia de ligação ao espaço de nomes. 
    1. Invoca o método [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) no `ServiceBusClient` objeto para criar um objeto [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) para o tópico de Service Bus especificado e a combinação de subscrição. 
    1. Especifica os manipuladores para os [eventos ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) e [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) do `ServiceBusProcessor` objeto. 
    1. Começa a processar mensagens invocando o [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) no `ServiceBusProcessor` objeto. 
    1. Quando o utilizador pressiona uma tecla para terminar o processamento, invoca o [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) no `ServiceBusProcessor` objeto. 
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
- [Amostras .NET para Azure Service Bus em GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Referência da API .NET](/dotnet/api/azure.messaging.servicebus?preserve-view=true)