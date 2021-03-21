---
title: Introdução aos tópicos e subscrições do Azure Service Bus | Microsoft Docs
description: Grave uma aplicação da consola C# .NET Core que utilize tópicos e subscrições das mensagens do Service Bus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 09/02/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 28e94cdb0df0a18b41f4c8a0ded362b50df6dcac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652977"
---
# <a name="get-started-with-service-bus-topics"></a>Introdução aos tópicos do Service Bus
Este tutorial contém os seguintes passos:

1. Escreva uma aplicação de consola .NET Core para enviar um conjunto de mensagens para o tópico.
2. Escreva uma aplicação de consola .NET Core para receber essas mensagens da subscrição.

> [!WARNING]
> Este quickstart utiliza o antigo pacote Microsoft.Azure.ServiceBus. Para obter um arranque rápido que utilize o mais recente pacote Azure.Messaging.ServiceBus, consulte [Enviar e receber mensagens utilizando o pacote Azure.Messaging.ServiceBus](service-bus-dotnet-how-to-use-topics-subscriptions.md). Para mover a sua aplicação de usar a antiga biblioteca para uma nova, consulte o [Guia para migrar do Microsoft.Azure.ServiceBus para Azure.Messaging.ServiceBus](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/MigrationGuide.md). 

## <a name="prerequisites"></a>Pré-requisitos

1. Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Siga os passos no [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrições do tópico](service-bus-quickstart-topics-subscriptions-portal.md) para fazer as seguintes tarefas:
    1. Crie um espaço de nomes de **autocarro de serviço.**
    2. Obtenha a **cadeia de ligação.**
    3. Crie um **tópico** no espaço de nomes.
    4. Crie **uma subscrição** do tópico no espaço de nomes.
3. [Visual Studio 2017 Atualização 3 (versão 15.3, 26730.01)](https://www.visualstudio.com/vs) ou posterior.
4. [SDK NET Core](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.
 
## <a name="send-messages-to-the-topic"></a>Enviar mensagens para o tópico

Para enviar mensagens para o tópico, grave uma aplicação de consola C# com o Visual Studio.

### <a name="create-a-console-application"></a>Criar uma aplicação de consola

Abra o Visual Studio e crie um novo projeto de **Console App (.NET Core) (Aplicação de Consola (.NET Core))**.

### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Service Bus

1. Clique com o botão direito do rato no projeto recém-criado e selecione **Gerir Pacotes NuGet**.
2. Clique no separador **Procurar**, procure **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** e selecione o item **Microsoft.Azure.ServiceBus**. Clique em **Instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.
   
    ![Selecionar um pacote NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Escrever código para enviar mensagens para o tópico

1. Em Program.cs, adicione as seguintes `using` declarações na parte superior da definição de espaço de nomes, antes da declaração de classe:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Na classe `Program`, declare as variáveis seguintes. Defina a variável `ServiceBusConnectionString` para a cadeia de ligação que obteve ao criar o espaço de nomes e defina `TopicName` para o nome que utilizou ao criar o tópico:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. Substitua o `Main()` método pelo seguinte método **assíncão** `Main` que envia mensagens assíncroneas utilizando o método SendMessagesAsync que irá adicionar no passo seguinte. 

    ```csharp
    public static async Task Main(string[] args)
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```
5. Imediatamente depois do método `Main`, adicione o seguinte método `SendMessagesAsync()` que realiza o trabalho de enviar o número de mensagens especificado pelo `numberOfMessagesToSend` (atualmente definido para 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. O ficheiro Program.cs do remetente deve ter o seguinte aspeto.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            public static async Task Main(string[] args)
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);
    
                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");
    
                // Send messages.
                await SendMessagesAsync(numberOfMessages);
    
                Console.ReadKey();
    
                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. Execute o programa e verifique o portal do Azure: clique no nome do seu tópico n janela **Descrição geral** do espaço de nomes. É apresentado o ecrã **Informações Básicas** do tópico. Na subscrição apresentada na parte inferior da janela, tenha em atenção que o valor da **Contagem de Mensagens** da subscrição é agora **10**. Sempre que executar a aplicação do remetente sem recuperar as mensagens (conforme descrito na próxima secção), esse valor aumenta 10. Note também que o tamanho atual do tópico aumenta o valor **Atual** na janela **Aspetos essenciais**, sempre que a aplicação adicionar uma mensagem ao tópico.
   
      ![Tamanho da mensagem][topic-message]

## <a name="receive-messages-from-the-subscription"></a>Receber mensagens da subscrição

Para receber as mensagens enviadas, crie outra aplicação de consola .NET Core e instale o pacote **Microsoft.Azure.ServiceBus** NuGet, semelhante à aplicação remetente anterior.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Escrever código para receber mensagens da subscrição

1. Em Program.cs, adicione as seguintes `using` declarações na parte superior da definição de espaço de nomes, antes da declaração de classe:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Na classe `Program`, declare as variáveis seguintes. Defina a variável `ServiceBusConnectionString` para a cadeia de ligação que obteve ao criar o espaço de nomes, defina `TopicName` para o nome que utilizou ao criar o tópico e defina `SubscriptionName` para o nome que utilizou ao criar a subscrição para o tópico:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. Substitua o `Main()` método pelo seguinte método **assíco.** `Main` Chama o `RegisterOnMessageHandlerAndReceiveMessages()` método que vai adicionar no próximo passo. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();    
    }
   ```
5. Imediatamente depois do método `Main()`, adicione o seguinte método que regista o processador de mensagens e recebe as mensagens enviadas pela aplicação remetente:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Diretamente depois do método anterior, adicione o seguinte método `ProcessMessagesAsync()` para processar as mensagens recebidas:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Por fim, adicione o seguinte método para processar quaisquer exceções que possam ocorrer:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. O ficheiro Program.cs do recetor deve ter o seguinte aspeto:
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            public static async Task Main(string[] args)
            {    
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);
        
                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");
        
                // Register subscription message handler and receive messages in a loop
                RegisterOnMessageHandlerAndReceiveMessages();
        
                Console.ReadKey();
        
                await subscriptionClient.CloseAsync();    
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. Execute o programa e consulte novamente o portal. Note que os valores **de Contagem de Mensagens** e **Correntes** são agora **0**.
   
    ![Comprimento do tópico][topic-message-receive]

Parabéns! Através da biblioteca .NET Standard, criou um tópico e uma subscrição, enviou 10 mensagens e recebeu essas mensagens.

> [!NOTE]
> Você pode gerir os recursos de Service Bus com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nomes de Service Bus e administram as entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes

Consulte o [repositório do GitHub com exemplos](https://github.com/Azure/azure-service-bus/tree/master/samples) do Service Bus que demonstram algumas das funcionalidades mais avançadas das mensagens do Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
