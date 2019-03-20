---
title: Tutorial – Atualizar variedade de inventário de comércio a retalho com canais de publicação/subscrição e filtros de tópico com o Azure PowerShell | Microsoft Docs
description: Neste tutorial, aprende a enviar e receber mensagens de um tópico e subscrição, e como adicionar e utilizar regras de filtro através do Azure PowerShell
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 21dcf522f00f1991ecb2a92d6dc0925baadbdcc6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081275"
---
# <a name="tutorial-update-inventory-using-powershell-and-topicssubscriptions"></a>Tutorial: Atualizar inventário com o PowerShell e tópicos/subscrições

O Microsoft Azure Service Bus é um serviço de mensagens na cloud multi-inquilino que envia informações entre aplicações e serviços. As operações assíncronas permitem o envio flexível de mensagens, juntamente com mensagens FIFO (first in, first out) e funcionalidades de publicação/subscrição. 

Este tutorial mostra como enviar e receber mensagens de e para uma fila do Service Bus através do PowerShell, para criar um espaço de nomes de mensagens e uma fila dentro desse espaço de nomes e obter as credenciais de autorização nesse espaço de nomes. O procedimento, em seguida, mostra como enviar e receber mensagens desta fila com a [biblioteca .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um tópico e uma ou mais subscrições do Service Bus para esse tópico com o Azure PowerShell
> * Adicionar filtros de tópico com o PowerShell
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar que chegaram nas subscrições previstas
> * Receber mensagens das subscrições

Um exemplo deste cenário é uma atualização da variedade de inventário para várias lojas de comércio a retalho. Neste cenário, cada loja ou grupo de lojas recebe mensagens dirigidas a elas para atualizar a respetiva variedade. Este tutorial mostra como implementar este cenário com subscrições e filtros. Primeiro, cria um tópico com três subscrições, adiciona algumas regras e filtros e, em seguida, envia e recebe mensagens do tópico e subscrições.

![tópico](./media/service-bus-tutorial-topics-subscriptions-powershell/about-service-bus-topic.png)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem instalada:

1. [Visual Studio 2017 Atualização 3 (versão 15.3, 26730.01)](https://www.visualstudio.com/vs) ou posterior.
2. [SDK NET Core](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

Este tutorial exige que execute a versão mais recente do Azure PowerShell. Se precisar de instalar ou atualizar, veja [Instalar e Configurar o Azure PowerShell][].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Emita os seguintes comandos para iniciar sessão no Azure. Estes passos não são necessários se estiver a executar comandos do PowerShell na Cloud Shell: 

1. Instalar o módulo do PowerShell do Service Bus:

   ```azurepowershell-interactive
   Install-Module AzureRM.ServiceBus
   ```

2. Execute o seguinte comando para iniciar sessão no Azure:

   ```azurepowershell-interactive
   Login-AzureRmAccount
   ```

4. Defina o contexto de subscrição atual ou veja a subscrição ativa atualmente:

   ```azurepowershell-interactive
   Select-AzureRmSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzureRmContext
   ```

## <a name="provision-resources"></a>Recursos de aprovisionamento

Depois de iniciar sessão no Azure, dê os seguintes comandos para aprovisionar os recursos do Service Bus. Não se esqueça de substituir todos os marcadores de posição pelos valores apropriados:

```azurepowershell-interactive
# Create a resource group 
New-AzureRmResourceGroup –Name my-resourcegroup –Location westus2

# Create a Messaging namespace
New-AzureRmServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location westus2

# Create a queue 
New-AzureRmServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Depois da execução do cmdlet `Get-AzureRmServiceBusKey`, copie e cole a cadeia de ligação e o nome de fila selecionado numa localização temporária, como o Bloco de notas. Vai precisar deles no próximo passo.

## <a name="send-and-receive-messages"></a>Enviar e receber mensagens

Depois de criar a fila e o espaço de nomes e se tiver as credenciais necessárias, está pronto para enviar e receber mensagens. Pode examinar o código nesta [pasta de exemplo do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Para executar o código, faça o seguinte:

1. Clone o [repositório do GitHub do Service Bus](https://github.com/Azure/azure-service-bus/) ao emitir o seguinte comando:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Abrir uma linha de comandos do PowerShell.

3. Navegue para a pasta de exemplo `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Se ainda não o tiver feito, obtenha a cadeia de ligação através do seguinte cmdlet do PowerShell. Não se esqueça de substituir `my-resourcegroup` e `namespace-name` pelos seus valores específicos: 

   ```azurepowershell-interactive
   Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```
5. Na linha de comandos do PowerShell, escreva o seguinte comando:

   ```shell
   dotnet build
   ```
6. Navegue para a pasta `\bin\Debug\netcoreapp2.0`.
7. Escreva o seguinte comando para executar o programa. Não se esqueça de substituir `myConnectionString` pelo valor obtido anteriormente e `myQueueName` pelo nome da fila que criou:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 
8. Observe as 10 mensagens que estão a ser enviadas para a fila e subsequentemente recebidas da fila:

   ![saída do programa](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Limpar recursos

Execute o seguinte comando para remover o grupo de recursos, o espaço de nomes e todos os recursos relacionados:

```powershell-interactive
Remove-AzureRmResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

Esta secção contém mais detalhes sobre o que faz o código de exemplo. 

### <a name="get-connection-string-and-queue"></a>Obter a cadeia de ligação e a fila

Os nomes da fila e da cadeia de ligação são transmitidos para o método `Main()` como argumentos da linha de comandos. `Main()` declara duas variáveis de cadeia para armazenar estes valores:

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

O método `MainAsync()` cria um cliente de fila com os argumentos da linha de comandos, chama um processador de mensagens de receção com o nome `RegisterOnMessageHandlerAndReceiveMessages()` e envia o conjunto de mensagens:

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

O método `RegisterOnMessageHandlerAndReceiveMessages()` define algumas opções de processador de mensagens e, em seguida, chama o método `RegisterMessageHandler()` do cliente de fila, que inicia a receção:

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

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprovisionou recursos com o Azure PowerShell e enviou e recebeu mensagens de um tópico do Service Bus e respetivas subscrições. Aprendeu a:

> [!div class="checklist"]
> * Criar um tópico e uma ou mais subscrições do Service Bus para esse tópico com o portal do Azure
> * Adicionar filtros de tópico com o código .NET
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar que chegaram nas subscrições previstas
> * Receber mensagens das subscrições

Para ver mais exemplos sobre o envio e receção de mensagens, comece com os [exemplos do Service Bus do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Avance para o próximo tutorial para saber mais sobre a utilização de capacidades de publicação/subscrição do Service Bus.

> [!div class="nextstepaction"]
> [Atualizar inventário com o PowerShell e tópicos/subscrições](service-bus-tutorial-topics-subscriptions-cli.md)

[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Instalar e Configurar o Azure PowerShell]: /powershell/azure/azurerm/install-azurerm-ps