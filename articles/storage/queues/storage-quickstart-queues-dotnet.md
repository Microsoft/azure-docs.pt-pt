---
title: 'Início rápido: biblioteca de armazenamento de fila do Azure V12-.NET'
description: Saiba como usar a biblioteca .NET V12 da fila do Azure para criar uma fila e adicionar mensagens à fila. Em seguida, você aprende a ler e excluir mensagens da fila. Você também aprenderá como excluir uma fila.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/22/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 71a714124cecfc4f985d448371042c8aff092a11
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463845"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Início rápido: biblioteca de cliente de armazenamento de fila do Azure V12 para .NET

Introdução à biblioteca de cliente de armazenamento de filas do Azure versão 12 para .NET. O armazenamento de filas do Azure é um serviço para armazenar grandes números de mensagens para recuperação e processamento posteriores. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

> [!NOTE]
> Para começar a usar a versão anterior do SDK, consulte [início rápido: usar o SDK do armazenamento do Azure v11 para .net para gerenciar uma fila](storage-quickstart-queues-dotnet-legacy.md).

Use a biblioteca de cliente de armazenamento de filas do Azure V12 para .NET para:

* Criar uma fila
* Adicionar mensagens a uma fila
* Inspecionar mensagens em uma fila
* Atualizar uma mensagem em uma fila
* Receber mensagens de uma fila
* Excluir mensagens de uma fila
* Eliminar uma fila

[Documentação de referência de API](/dotnet/api/azure.storage.queues) | [amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples) de | do pacote de [código-fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues) [(NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0) | 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) atual para seu sistema operacional. Certifique-se de obter o SDK e não o tempo de execução.

## <a name="setting-up"></a>Configurando

Esta seção orienta você pela preparação de um projeto para trabalhar com a biblioteca de cliente de armazenamento de filas do Azure V12 para .NET.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo .NET Core chamado *QueuesQuickstartV12*.

1. Em uma janela de console (como cmd, PowerShell ou bash), use o comando `dotnet new` para criar um novo aplicativo de console com o nome *QueuesQuickstartV12*. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de origem: *Program.cs*.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Alterne para o diretório *QueuesQuickstartV12* recém-criado.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale o pacote da biblioteca de cliente do armazenamento de filas do Azure para .NET usando o comando `dotnet add package`.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Configurar a estrutura do aplicativo

No diretório do projeto:

1. Abra o arquivo *Program.cs* em seu editor
1. Remover a instrução `Console.WriteLine("Hello World!");`
1. Adicionar `using` diretivas
1. Atualizar a declaração do método de `Main` para [dar suporte ao código assíncrono](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7-1#async-main)



Este é o código:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de Filas do Azure é um serviço para alojar grandes quantidades de mensagens. Uma mensagem da fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processar de forma assíncrona. O armazenamento de filas oferece três tipos de recursos:

* A conta de armazenamento
* Uma fila na conta de armazenamento
* Mensagens dentro da fila

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de filas](./media/storage-queues-introduction/queue1.png)

Use as seguintes classes .NET para interagir com estes recursos:

* [QueueServiceClient](/dotnet/api/azure.storage.queues.queueserviceclient): o `QueueServiceClient` permite que você gerencie todas as filas em sua conta de armazenamento.
* [QueueClient](/dotnet/api/azure.storage.queues.queueclient): a classe `QueueClient` permite que você gerencie e manipule uma fila individual e suas mensagens.
* [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage): a classe `QueueMessage` representa os objetos individuais retornados ao chamar [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) em uma fila.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código de exemplo mostram como fazer as seguintes ações com a biblioteca de cliente de armazenamento de filas do Azure para .NET:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar uma fila](#create-a-queue)
* [Adicionar mensagens a uma fila](#add-messages-to-a-queue)
* [Inspecionar mensagens em uma fila](#peek-at-messages-in-a-queue)
* [Atualizar uma mensagem em uma fila](#update-a-message-in-a-queue)
* [Receber mensagens de uma fila](#receive-messages-from-a-queue)
* [Excluir mensagens de uma fila](#delete-messages-from-a-queue)
* [Excluir uma fila](#delete-a-queue)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código a seguir recupera a cadeia de conexão para a conta de armazenamento. A cadeia de conexão é armazenada na variável de ambiente criada na seção [configurar sua cadeia de conexão de armazenamento](#configure-your-storage-connection-string) .

Adicione este código dentro do método `Main`:

```csharp
Console.WriteLine("Azure Queue storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Criar uma fila

Escolha um nome para a nova fila. O código a seguir acrescenta um valor de GUID ao nome da fila para garantir que seja exclusivo.

> [!IMPORTANT]
> Os nomes de fila podem conter apenas letras minúsculas, números e hifens e devem começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen. O nome também deve ter entre 3 e 63 caracteres de comprimento. Para obter mais informações sobre como nomear filas, consulte [nomeando filas e metadados](/rest/api/storageservices/naming-queues-and-metadata).


Crie uma instância da classe [QueueClient](/dotnet/api/azure.storage.queues.queueclient) . Em seguida, chame o método [createasync](/dotnet/api/azure.storage.queues.queueclient.createasync) para criar a fila em sua conta de armazenamento.

Adicione este código ao final do método de `Main`:

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Adicionar mensagens a uma fila

O trecho de código a seguir adiciona mensagens à fila de forma assíncrona chamando o método [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) . Ele também salva um [SendReceipt](/dotnet/api/azure.storage.queues.models.sendreceipt) retornado de uma chamada `SendMessageAsync`. O recibo é usado para atualizar a mensagem posteriormente no programa.

Adicione este código ao final do método de `Main`:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Inspecionar mensagens em uma fila

Inspecione as mensagens na fila chamando o método [PeekMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) . O método `PeekMessagesAsync` recupera uma ou mais mensagens da frente da fila, mas não altera a visibilidade da mensagem.

Adicione este código ao final do método de `Main`:

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Atualizar uma mensagem em uma fila

Atualize o conteúdo de uma mensagem chamando o método [UpdateMessageAsync](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) . O método `UpdateMessageAsync` pode alterar o tempo limite e o conteúdo da visibilidade de uma mensagem. O conteúdo da mensagem deve ser uma cadeia de caracteres codificada em UTF-8 que tenha até 64 KB de tamanho. Junto com o novo conteúdo da mensagem, passe os valores do `SendReceipt` que foi salvo anteriormente no código. Os valores de `SendReceipt` identificam a mensagem a ser atualizada.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

Baixe mensagens adicionadas anteriormente chamando o método [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) .

Adicione este código ao final do método de `Main`:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Excluir mensagens de uma fila

Exclua as mensagens da fila depois que elas forem processadas. Nesse caso, o processamento está apenas exibindo a mensagem no console.

O aplicativo pausa a entrada do usuário chamando `Console.ReadLine` antes de processar e excluir as mensagens. Verifique no [portal do Azure](https://portal.azure.com) que os recursos foram criados corretamente, antes de serem excluídos. Todas as mensagens que não forem explicitamente excluídas se tornarão visíveis na fila novamente para outra oportunidade de processá-las.

Adicione este código ao final do método de `Main`:

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Eliminar uma fila

O código a seguir limpa os recursos que o aplicativo criou excluindo a fila usando o método [DeleteAsync](/dotnet/api/azure.storage.queues.queueclient.deleteasync) .

Adicione este código ao final do método de `Main`:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Executar o código

Esse aplicativo cria e adiciona três mensagens a uma fila do Azure. O código lista as mensagens na fila, depois as recupera e exclui, antes de excluir a fila.

Na janela do console, navegue até o diretório do aplicativo e, em seguida, compile e execute o aplicativo.

```console
dotnet build
```

```console
dotnet run
```

A saída do aplicativo é semelhante ao exemplo a seguir:

```output
Azure Queue storage v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

Quando o aplicativo for pausado antes de receber mensagens, verifique sua conta de armazenamento no [portal do Azure](https://portal.azure.com). Verifique se as mensagens estão na fila.

Pressione a tecla **Enter** para receber e excluir as mensagens. Quando solicitado, pressione a tecla **Enter** novamente para excluir a fila e concluir a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar uma fila e a adicionar mensagens a ela usando o código .NET assíncrono. Em seguida, você aprendeu a inspecionar, recuperar e excluir mensagens. Por fim, você aprendeu a excluir uma fila de mensagens.

Para obter tutoriais, exemplos, inícios rápidos e outras documentações, visite:

> [!div class="nextstepaction"]
> [Azure for .NET and .NET Core developers](https://docs.microsoft.com/dotnet/azure/) (Azure para programadores de .NET e .NET Core)

* Para saber mais, confira [bibliotecas de armazenamento do Azure para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage).
* Para ver mais aplicativos de exemplo de armazenamento de filas do Azure, vá para o [armazenamento de filas do Azure V12 exemplos de biblioteca de cliente .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples).
* Para saber mais sobre o .NET Core, veja [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
