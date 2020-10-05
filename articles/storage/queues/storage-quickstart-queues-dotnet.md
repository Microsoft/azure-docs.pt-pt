---
title: 'Quickstart: Biblioteca de armazenamento Azure Queue v12 - .NET'
description: Saiba como utilizar a biblioteca Azure Queue .NET v12 para criar uma fila e adicionar mensagens à fila. Em seguida, aprende-se a ler e a apagar mensagens da fila. Também aprenderá a apagar uma fila.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: fd60adf94e57be3892a264c2a8457b839e1c8778
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89011298"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Quickstart: Azure Queue storage client library v12 for .NET

Começa com a versão 12 da biblioteca do cliente de armazenamento da Fila Azure para .NET. O armazenamento da fila Azure é um serviço para armazenar um grande número de mensagens para posterior recuperação e processamento. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

Utilize a biblioteca de clientes de armazenamento Azure Queue v12 para .NET para:

* Criar uma fila
* Adicione mensagens a uma fila
* Espreite as mensagens em uma fila
* Atualize uma mensagem em uma fila
* Receber mensagens de uma fila
* Apagar mensagens de uma fila
* Eliminar uma fila

Recursos adicionais:

* [Documentação de referência da API](/dotnet/api/azure.storage.queues)
* [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues)
* [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0)
* [Amostras](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento Azure - [crie uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Corrente [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operativo. Certifique-se de pegar o SDK e não o tempo de execução.

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes de armazenamento Azure Queue v12 para .NET.

### <a name="create-the-project"></a>Criar o projeto

Criar uma aplicação .NET Core chamada *QueuesQuickstartV12*.

1. Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome *QueuesQuickstartV12*. Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: *Program.cs*.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Mude para o recém-criado *diretório QueuesQuickstartV12.*

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda está no diretório de aplicações, instale a biblioteca do cliente de armazenamento Azure Queue para pacote .NET utilizando o `dotnet add package` comando.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Abra o ficheiro *Program.cs* no seu editor
1. Remova a `Console.WriteLine("Hello World!");` declaração
1. Adicionar `using` diretivas
1. Atualizar a `Main` declaração do método para apoiar o código [async](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7-1#async-main)



Aqui está o código:

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

O armazenamento de Filas do Azure é um serviço para alojar grandes quantidades de mensagens. Uma mensagem de fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até ao limite total de capacidade de uma conta de armazenamento. As filas são comumente usadas para criar um atraso de trabalho para processar assíncronos. O armazenamento de fila oferece três tipos de recursos:

* A conta de armazenamento
* Uma fila na conta de armazenamento
* Mensagens dentro da fila

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama de arquitetura de armazenamento de fila](./media/storage-queues-introduction/queue1.png)

Utilize as seguintes classes .NET para interagir com estes recursos:

* [QueueServiceClient](/dotnet/api/azure.storage.queues.queueserviceclient): `QueueServiceClient` Permite-lhe gerir todas as filas na sua conta de armazenamento.
* [QueueClient](/dotnet/api/azure.storage.queues.queueclient): A `QueueClient` classe permite-lhe gerir e manipular uma fila individual e as suas mensagens.
* [FilaMessage](/dotnet/api/azure.storage.queues.models.queuemessage): A `QueueMessage` classe representa os objetos individuais devolvidos ao chamar ["Receber Caixas"](/dotnet/api/azure.storage.queues.queueclient.receivemessages) numa fila.

## <a name="code-examples"></a>Exemplos de código

Estes excertos de código de exemplo mostram-lhe como fazer as seguintes ações com a biblioteca do cliente de armazenamento Azure Queue para .NET:

* [Obter a cadeia de ligação](#get-the-connection-string)
* [Criar uma fila](#create-a-queue)
* [Adicione mensagens a uma fila](#add-messages-to-a-queue)
* [Espreite as mensagens em uma fila](#peek-at-messages-in-a-queue)
* [Atualize uma mensagem em uma fila](#update-a-message-in-a-queue)
* [Receber mensagens de uma fila](#receive-messages-from-a-queue)
* [Apagar mensagens de uma fila](#delete-messages-from-a-queue)
* [Eliminar uma fila](#delete-a-queue)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código abaixo recupera a cadeia de ligação para a conta de armazenamento. A cadeia de ligação é armazenada na variável ambiente criada na secção [de cadeia de ligação de armazenamento.](#configure-your-storage-connection-string)

Adicione este código dentro do `Main` método:

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

Decida um nome para a nova fila. O código abaixo anexa um valor GUID ao nome da fila para garantir que é único.

> [!IMPORTANT]
> Os nomes da fila só podem conter letras minúsculas, números e hífens, e devem começar com uma letra ou um número. Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen. O nome também deve ter entre 3 e 63 caracteres de comprimento. Para obter mais informações sobre o nome de filas, consulte [As Filas de Nomeação e Os Metadados](/rest/api/storageservices/naming-queues-and-metadata).


Crie um exemplo da classe [QueueClient.](/dotnet/api/azure.storage.queues.queueclient) Em seguida, ligue para o método [CreateAsync](/dotnet/api/azure.storage.queues.queueclient.createasync) para criar a fila na sua conta de armazenamento.

Adicione este código ao fim do `Main` método:

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

### <a name="add-messages-to-a-queue"></a>Adicione mensagens a uma fila

O seguinte código snippet assíncronimo adiciona mensagens à fila, chamando o método [SendMessageAsync.](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) Também salva um [SendReceipt](/dotnet/api/azure.storage.queues.models.sendreceipt) devolvido de uma `SendMessageAsync` chamada. O recibo é utilizado para atualizar a mensagem mais tarde no programa.

Adicione este código ao fim do `Main` método:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Espreite as mensagens em uma fila

Espreite as mensagens na fila chamando o método [PeekMessagesAsync.](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) O `PeekMessagesAsync` método recupera uma ou mais mensagens da parte da frente da fila, mas não altera a visibilidade da mensagem.

Adicione este código ao fim do `Main` método:

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

### <a name="update-a-message-in-a-queue"></a>Atualize uma mensagem em uma fila

Atualize o conteúdo de uma mensagem chamando o método [UpdateMessageAsync.](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) O `UpdateMessageAsync` método pode alterar o tempo limite de visibilidade e o conteúdo de uma mensagem. O conteúdo da mensagem deve ser uma cadeia codificada UTF-8 que tem até 64 KB de tamanho. Juntamente com o novo conteúdo da mensagem, passe os valores do `SendReceipt` que foi guardado anteriormente no código. Os `SendReceipt` valores identificam a mensagem a atualizar.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

Descarregue mensagens previamente adicionadas chamando o método [ReceiveMessagesAsync.](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync)

Adicione este código ao fim do `Main` método:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Apagar mensagens de uma fila

Apague as mensagens da fila depois de processadas. Neste caso, o processamento é apenas exibindo a mensagem na consola.

A aplicação faz uma pausa para a entrada do utilizador, ligando `Console.ReadLine` antes de processar e eliminando as mensagens. Verifique no seu [portal Azure](https://portal.azure.com) que os recursos foram criados corretamente, antes de serem eliminados. Quaisquer mensagens não explicitamente apagadas acabarão por se tornar visíveis na fila novamente para outra oportunidade de as processar.

Adicione este código ao fim do `Main` método:

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

O código seguinte limpa os recursos que a app criou eliminando a fila utilizando o método [DeleteAsync.](/dotnet/api/azure.storage.queues.queueclient.deleteasync)

Adicione este código ao fim do `Main` método:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Executar o código

Esta aplicação cria e adiciona três mensagens a uma fila Azure. O código lista as mensagens na fila e, em seguida, recupera-as e apaga-as, antes de finalmente apagar a fila.

Na janela da consola, navegue para o seu diretório de aplicações e, em seguida, construa e execute a aplicação.

```console
dotnet build
```

```console
dotnet run
```

A saída da app é semelhante ao seguinte exemplo:

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

Quando a aplicação parar antes de receber mensagens, consulte a sua conta de armazenamento no [portal Azure](https://portal.azure.com). Verifique se as mensagens estão na fila.

Prima a tecla **'Inserir'** para receber e eliminar as mensagens. Quando solicitado, prima novamente a tecla **Enter** para apagar a fila e terminar a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma fila e a adicionar-lhe mensagens utilizando código ASSíncronos .NET. Depois aprendeu a espreitar, a recuperar e a apagar mensagens. Finalmente, aprendeu a apagar uma fila de mensagens.

Para tutoriais, amostras, arranques rápidos e outra documentação, visite:

> [!div class="nextstepaction"]
> [Azure for .NET and .NET Core developers](https://docs.microsoft.com/dotnet/azure/) (Azure para programadores de .NET e .NET Core)

* Para saber mais, consulte as [bibliotecas de Armazenamento Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage).
* Para ver mais aplicativos de amostra de armazenamento da Azure Queue, continue até a [Azure Queue storage v12 .NET client library samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples).
* Para saber mais sobre o .NET Core, veja [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
