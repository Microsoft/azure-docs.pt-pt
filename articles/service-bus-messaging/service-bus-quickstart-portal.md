---
title: Usar o portal do Azure para criar uma fila do barramento de serviço
description: Neste guia de início rápido, você aprende a criar uma fila do barramento de serviço usando o portal do Azure. Em seguida, você usa um aplicativo cliente de exemplo para enviar mensagens e receber mensagens da fila.
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 0c88db652efe54a497af094160fb426be7c43d16
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260842"
---
# <a name="quickstart-use-azure-portal-to-create-a-service-bus-queue"></a>Início rápido: usar portal do Azure para criar uma fila do barramento de serviço
Este guia de início rápido descreve como enviar e receber mensagens de e para uma fila do barramento de serviço, usando o [portal do Azure][Azure portal] para criar um namespace de mensagens e uma fila dentro desse namespace e para obter as credenciais de autorização nesse namespace. O procedimento, em seguida, mostra como enviar e receber mensagens desta fila através da [biblioteca .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem instalada:

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita][] antes de começar.
- [Visual Studio 2017 Atualização 3 (versão 15.3, 26730.01)](https://www.visualstudio.com/vs) ou posterior. Você usa o Visual Studio para criar um exemplo que envia mensagens para e recebe a mensagem de uma fila. O exemplo é testar a fila que você criou usando o PowerShell. 
- [SDK NET Core](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="send-and-receive-messages"></a>Enviar e receber mensagens

> [!NOTE]
> O exemplo usado nesta seção para enviar e receber mensagens é um exemplo do .NET. Para obter exemplos de envio/recebimento de mensagens usando outras linguagens de programação, consulte [exemplos de barramento de serviço](service-bus-samples.md). 
> 
> Para obter instruções passo a passo para enviar/receber mensagens usando várias linguagens de programação, consulte o início rápido a seguir:
> - [.NET](service-bus-dotnet-get-started-with-queues.md)
> - [Java](service-bus-java-how-to-use-queues.md)
> - [Node. js usando o pacote do Azure/Service-Bus](service-bus-nodejs-how-to-use-queues-new-package.md)
> - [Node. js usando o pacote Azure-SB](service-bus-nodejs-how-to-use-queues.md)
> - [PHP](service-bus-php-how-to-use-queues.md)
> - [Python](service-bus-python-how-to-use-queues.md)
> - [Ruby](service-bus-ruby-how-to-use-queues.md)

Depois de aprovisionar a fila e o espaço de nomes e se tiver as credenciais necessárias, está pronto para enviar e receber mensagens. Pode examinar o código nesta [pasta de exemplo do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters).

Para executar o código, faça o seguinte:

1. Clone o [repositório do GitHub do Service Bus](https://github.com/Azure/azure-service-bus/) ao emitir o comando seguinte:

   ```
   git clone https://github.com/Azure/azure-service-bus.git
   ```
3. Navegue para a pasta `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart` de exemplo.
4. Copie a cadeia de conexão e o nome da fila obtidos na seção obter as credenciais de gerenciamento.
5.  Na linha de comandos, escreva o seguinte comando:

    ```
    dotnet build
    ```
6.  Navegue para a pasta `bin\Debug\netcoreapp2.0`.
7.  Escreva o seguinte comando para executar o programa. Não se esqueça de substituir `myConnectionString` pelo valor obtido anteriormente e `myQueueName` pelo nome da fila que criou:

    ```shell
    dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
    ``` 
8. Observe as 10 mensagens que estão a ser enviadas para a fila e subsequentemente recebidas da fila:

   ![saída do programa](./media/service-bus-quickstart-portal/dotnet.png)

## <a name="clean-up-resources"></a>Limpar recursos

Pode utilizar o portal para remover o grupo de recursos, o espaço de nomes e a fila.

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

Esta secção contém mais detalhes sobre o que faz o código de exemplo. 

### <a name="get-connection-string-and-queue"></a>Obter a cadeia de ligação e a fila

A cadeia de conexão e o nome da fila são passados para o método `Main()` como argumentos de linha de comando. `Main()` declara duas variáveis de cadeia para armazenar estes valores:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
Em seguida, o método `Main()` inicia o ciclo de mensagens assíncronas, `MainAsync()`.

### <a name="message-loop"></a>Ciclo de mensagens

O método MainAsync () cria um cliente de fila com os argumentos de linha de comando, chama um manipulador de mensagens de recebimento chamado `RegisterOnMessageHandlerAndReceiveMessages()`e envia o conjunto de mensagens:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

O método `RegisterOnMessageHandlerAndReceiveMessages()` define simplesmente algumas opções de processador de mensagens e, em seguida, chama o método `RegisterMessageHandler()` do cliente de fila, que inicia a receção:

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>Enviar mensagens

As operações de criação e envio de mensagem ocorrem no método `SendMessagesAsync()`:

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>Processar mensagens

O método `ProcessMessagesAsync()` reconhece, processa e conclui a receção das mensagens:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```
> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um espaço de nomes do Service Bus e outros recursos necessários para enviar e receber mensagens numa fila. Para saber mais sobre como escrever código para enviar e receber mensagens, continue nos tutoriais na seção **Enviar e receber mensagens** . 

> [!div class="nextstepaction"]
> [Enviar e receber mensagens](service-bus-dotnet-get-started-with-queues.md)


[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
