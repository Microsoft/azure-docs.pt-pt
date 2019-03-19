---
title: Introdução ao armazenamento de filas e o Visual Studio (ASP.NET Core) de serviços ligados | Documentos da Microsoft
description: Como começar a utilizar o armazenamento de filas do Azure num projeto ASP.NET Core no Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 8a91614e7dfb804e6a902967ce60f898ed0e54ad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999762"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Introdução ao armazenamento de filas e o Visual Studio (ASP.NET Core) de serviços ligados

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Este artigo descreve como começar a utilizar o armazenamento de filas do Azure no Visual Studio depois de ter criado ou referenciados uma conta de armazenamento do Azure num projeto do ASP.NET Core, utilizando o Visual Studio **serviços ligados** funcionalidade. O **serviços ligados** operação instala os pacotes de NuGet adequados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento para ficheiros de configuração do projeto. (Consulte [documentação do armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.)

Armazenamento de filas do Azure é um serviço para armazenar grandes quantidades de mensagens que podem ser acedidas em qualquer local no mundo através de chamadas autenticadas, utilizando HTTP ou HTTPS. Uma mensagem de fila única pode ser até 64 kilobytes (KB) e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. Consulte também [introdução ao armazenamento de filas do Azure com o .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter detalhes sobre a manipulação programática de filas.

Para começar a utilizar, crie primeiro uma fila do Azure na sua conta de armazenamento. Este artigo, em seguida, mostra como criar uma fila em c# e como realizar operações de fila básicas, tais como adicionar, modificar, ler e remover mensagens na fila.  O código utiliza a biblioteca de cliente de armazenamento do Azure para .NET. Para obter mais informações sobre o ASP.NET, consulte [ASP.NET](https://www.asp.net).

Algumas das APIs de armazenamento do Azure são assíncronas e o código neste artigo supõe que métodos async que estão a ser utilizados. Ver [programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para obter mais informações.

## <a name="access-queues-in-code"></a>Filas de acesso no código

Para aceder a filas em projetos do ASP.NET Core, incluem os seguintes itens em qualquer origem arquivo c# que acede ao armazenamento de filas do Azure. Utilize todo esse código à frente do código nas secções que se seguem.

1. Adicione as informações necessárias `using` instruções:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Obter um `CloudStorageAccount` objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um `CloudQueueClient` objeto para referenciar os objetos de fila na sua conta de armazenamento:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obter um `CloudQueue` objeto para fazer referência a uma fila específica:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Criar uma fila no código

Para criar a fila do Azure no código, chamar `CreateIfNotExistsAsync`:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Adicione uma mensagem numa fila

Para introduzir uma mensagem numa fila existente, crie uma nova `CloudQueueMessage` de objeto, em seguida, chamar o `AddMessageAsync` método. A `CloudQueueMessage` objeto pode ser criado a partir de uma cadeia de caracteres (em formato UTF-8) ou uma matriz de bytes.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Ler uma mensagem numa fila

Pode pré-visualizar a mensagem no início de uma fila sem a remover da fila chamando o `PeekMessageAsync` método:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Ler e remover uma mensagem numa fila

Pode remover o seu código (remover da fila) uma mensagem da fila em dois passos.

1. Chamar `GetMessageAsync` para obter a seguinte mensagem numa fila. Uma mensagem devolvida por `GetMessageAsync` torna-se invisível para qualquer outro código lendo as mensagens desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos.
1. Para concluir a remover a mensagem da fila, chamar `DeleteMessageAsync`.

Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seguinte código chamadas `DeleteMessageAsync` imediatamente após a mensagem tiver sido processada:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para remoção da fila de mensagens

Existem duas formas de personalizar a obtenção de mensagens de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem. O seguinte código de exemplo utiliza o `GetMessages` método para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem, usando um `foreach` loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Tenha em atenção que os cinco minuto temporizador é iniciada para todas as mensagens ao mesmo tempo, portanto, após cinco minutos tenham passado, todas as mensagens que não foram eliminadas se tornar visíveis novamente.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Obter o comprimento da fila

Pode obter uma estimativa do número de mensagens numa fila. O `FetchAttributes` método pede ao serviço de fila para obter os atributos de fila, incluindo a contagem de mensagens. O `ApproximateMethodCount` propriedade retorna o último valor obtido pelo `FetchAttributes` método, sem chamar o serviço de fila.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Utilizar o padrão Async-Await com APIs de fila comum

Este exemplo mostra como utilizar o async-await padrão com comum colocar em fila APIs terminando `Async`. Quando é utilizado um método async, o async-await padrão suspende a execução local até que a chamada for concluída. Este comportamento permite que o thread atual efetue outro trabalho que ajuda a evitar congestionamentos de desempenho e melhora a capacidade de resposta do seu aplicativo.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Eliminar uma fila

Para eliminar uma fila e todas as mensagens contidas no mesmo, chame o `Delete` método no objeto de fila:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
