---
title: Começar com o armazenamento de fila usando o Visual Studio (ASP.NET Core)
description: Como começar a usar o armazenamento de fila Azure num projeto ASP.NET Core no Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5cdf6f2644788674df91b533c9444fc88ab30b09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72300019"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Começar com o armazenamento de fila e serviços conectados do Estúdio Visual (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Este artigo descreve como começar a usar o armazenamento de fila Azure no Estúdio Visual depois de ter criado ou referenciado uma conta de armazenamento Azure num projeto ASP.NET Core utilizando a funcionalidade Visual Studio **Connected Services.** A operação **Connected Services** instala os pacotes NuGet apropriados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento aos seus ficheiros de configuração do projeto. (Consulte a [documentação](https://azure.microsoft.com/documentation/services/storage/) de armazenamento para obter informações gerais sobre o Armazenamento de Azure.)

O armazenamento de filas Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer parte do mundo através de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 quilobytes (KB) de tamanho, e uma fila pode conter milhões de mensagens, até ao limite total de capacidade de uma conta de armazenamento. Consulte também o Get started com o armazenamento da [Fila Azure utilizando .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para detalhes sobre a manipulação programática das filas.

Para começar, crie primeiro uma fila Azure na sua conta de armazenamento. Este artigo mostra então como criar uma fila em C# e como realizar operações básicas de fila, tais como adicionar, modificar, ler e remover mensagens de fila.  O código utiliza a Biblioteca de Clientes de Armazenamento Azure para .NET. Para mais informações sobre ASP.NET, consulte [ASP.NET](https://www.asp.net).

Algumas das APIs de Armazenamento Azure são assíncronas, e o código neste artigo pressupõe que os métodos de asincronização estão a ser usados. Consulte [a programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para obter mais informações.

## <a name="access-queues-in-code"></a>Filas de acesso em código

Para aceder a filas em ASP.NET projetos Core, inclua os seguintes itens em qualquer ficheiro de origem C# que aceda ao armazenamento de fila azure. Utilize todo este código em frente ao código nas secções que se seguem.

1. Adicione as `using` declarações necessárias:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Obtenha `CloudStorageAccount` um objeto que represente a informação da sua conta de armazenamento. Utilize o seguinte código para obter as informações de cadeia de ligação de armazenamento e conta de armazenamento da configuração do serviço Azure:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenha `CloudQueueClient` um objeto para fazer referência aos objetos de fila na sua conta de armazenamento:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obtenha `CloudQueue` um objeto para fazer referência a uma fila específica:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Criar uma fila em código

Para criar a fila Azure `CreateIfNotExistsAsync`em código, ligue:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Adicione uma mensagem a uma fila

Para inserir uma mensagem numa fila existente, crie um novo `CloudQueueMessage` objeto e, em seguida, ligue para o `AddMessageAsync` método. Um `CloudQueueMessage` objeto pode ser criado a partir de uma corda (em formato UTF-8) ou de uma matriz byte.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Leia uma mensagem em uma fila

Pode espreitar a mensagem na frente de uma fila sem `PeekMessageAsync` a retirar da fila, chamando o método:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Leia e remova uma mensagem em fila

O seu código pode remover (defilar) uma mensagem de uma fila em dois passos.

1. Ligue `GetMessageAsync` para receber a próxima mensagem numa fila. Uma mensagem `GetMessageAsync` devolvida torna-se invisível a qualquer outra mensagem de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos.
1. Para terminar de remover a `DeleteMessageAsync`mensagem da fila, ligue.

Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. As seguintes `DeleteMessageAsync` chamadas de código logo após a mensagem ter sido processada:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para desfilar mensagens

Há duas maneiras de personalizar a recuperação de mensagens de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem. O exemplo de `GetMessages` código que se segue utiliza o método para obter 20 mensagens numa chamada. Em seguida, processa `foreach` cada mensagem usando um loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Note que o temporizador de cinco minutos começa para todas as mensagens ao mesmo tempo, pelo que, após cinco minutos de corrido, quaisquer mensagens que não tenham sido apagadas tornam-se visíveis novamente.

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

Pode obter uma estimativa do número de mensagens numa fila. O `FetchAttributes` método pede ao serviço de fila para recuperar os atributos da fila, incluindo a contagem de mensagens. A `ApproximateMethodCount` propriedade devolve o último `FetchAttributes` valor recuperado pelo método, sem chamar o serviço de fila.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Use o padrão Async-Await com APIs de fila comum

Este exemplo mostra como usar o padrão de asincronização `Async`com APIs de fila comum terminando com . Quando um método de asincronização é usado, o padrão de asincronização suspende a execução local até que a chamada esteja concluída. Este comportamento permite que o fio atual faça outro trabalho que ajude a evitar estrangulamentos de desempenho e melhora a capacidade de resposta geral da sua aplicação.

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

Para eliminar uma fila e todas as mensagens contidas, ligue para o `Delete` método do objeto de fila:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
