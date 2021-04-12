---
title: Começa com o armazenamento da fila utilizando o Visual Studio (serviços em nuvem)
description: Como começar a usar o armazenamento da Azure Queue num projeto de serviço em nuvem em Visual Studio depois de se ligar a uma conta de armazenamento usando serviços conectados ao Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 94f248edfebd6c6fedb78a54eee220c0ef38b4ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95545881"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução aos serviços ligados (projetos de serviços cloud) Armazenamento de Filas do Azure e o Visual Studio
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição Geral
Este artigo descreve como começar a usar o armazenamento da Azure Queue no Visual Studio depois de ter criado ou referenciado uma conta de armazenamento Azure num projeto de serviços na nuvem utilizando o diálogo visual Studio **Add Connected Services.**

Vamos mostrar-lhe como criar uma fila em código. Também lhe mostraremos como realizar operações básicas de fila, tais como adicionar, modificar, ler e remover mensagens de fila. As amostras estão escritas no código C# e utilizam a Biblioteca do [Cliente de Armazenamento microsoft Azure para .NET](/previous-versions/azure/dn261237(v=azure.100)).

A operação **Add Connected Services** instala os pacotes NuGet apropriados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento aos ficheiros de configuração do projeto.

* Consulte [Começar com o armazenamento da Fila Azure utilizando .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter mais informações sobre a manipulação de filas em código.
* Consulte [a documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o Armazenamento Azure.
* Consulte [a documentação dos Serviços cloud](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços na nuvem Azure.
* Consulte [ASP.NET](https://www.asp.net) para obter mais informações sobre a programação ASP.NET aplicações.

O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB e uma fila pode conter milhões de mensagens, até ao limite da capacidade total de uma conta de armazenamento.

## <a name="access-queues-in-code"></a>Filas de acesso em código
Para aceder a filas em projetos do Visual Studio Cloud Services, é necessário incluir os seguintes itens em qualquer ficheiro de origem C# que aceda ao armazenamento da Fila Azure.

1. Certifique-se de que as declarações de espaço de nome no topo do ficheiro C# incluem estas **declarações.**
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
2. Obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações da sua cadeia de ligação de armazenamento e conta de armazenamento a partir da configuração do serviço Azure.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
3. Obtenha um objeto **CloudQueueClient** para fazer referência aos objetos de fila na sua conta de armazenamento.  
   
    ```csharp
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
4. Obtenha um objeto **CloudQueue** para fazer referência a uma fila específica.
   
    ```csharp
    // Get a reference to a queue named "messageQueue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");
    ```

**NOTA:** Utilize todo o código acima em frente ao código nas seguintes amostras.

## <a name="create-a-queue-in-code"></a>Criar uma fila em código
Para criar a fila em código, basta adicionar uma chamada ao **CreateIfNotExists**.

```csharp
// Create the CloudQueue if it does not exist
messageQueue.CreateIfNotExists();
```

## <a name="add-a-message-to-a-queue"></a>Adicione uma mensagem a uma fila
Para inserir uma mensagem numa fila existente, crie um novo objeto **CloudQueueMessage** e, em seguida, chame o método **AddMessage.**

Um objeto **CloudQueueMessage** pode ser criado a partir de uma cadeia (em formato UTF-8) ou de um conjunto de byte.

Aqui está um exemplo que insere a mensagem "Olá, Mundo".

```csharp
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
messageQueue.AddMessage(message);
```

## <a name="read-a-message-in-a-queue"></a>Leia uma mensagem em uma fila
Pode pré-visualizar a mensagem no início de um fila sem a remover da fila ao chamar o método **PeekMessage**.

```csharp
// Peek at the next message
CloudQueueMessage peekedMessage = messageQueue.PeekMessage();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Leia e remova uma mensagem em uma fila
O seu código pode remover (de-fila) uma mensagem de uma fila em dois passos.

1. Ligue para **a GetMessage** para receber a próxima mensagem numa fila. Uma mensagem devolvida por **GetMessage** torna-se invisível para quaisquer outras mensagens de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos.
2. Para terminar a remoção da mensagem da fila, ligue para **DeleteMessage**.

Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seguinte código chama **DeleteMessage** logo após o processo da mensagem.

```csharp
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

// Process the message in less than 30 seconds

// Then delete the message.
await messageQueue.DeleteMessage(retrievedMessage);
```


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Use opções adicionais para processar e remover mensagens de fila
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.

* Pode obter um lote de mensagens (até 32).
* Pode definir um tempo de invisibilidade mais longo ou curto, permitindo ao seu código mais ou menos tempo para processar totalmente cada mensagem. O seguinte código de exemplo utiliza o método **GetMessages** para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem através de um ciclo **foreach**. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Tenha em atenção que os 5 minutos começam para todas as mensagens ao mesmo tempo, como tal, 5 minutos após a chamada para **GetMessages**, as mensagens que não tenham sido eliminadas ficarão visíveis novamente.

Eis um exemplo:

```csharp
foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.

    // Then delete the message after processing
    messageQueue.DeleteMessage(message);

}
```

## <a name="get-the-queue-length"></a>Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens numa fila. O método **FetchAttributes** pede ao serviço Fila que devolva os atributos de fila, incluindo a contagem de mensagens. A propriedade **ApproximateMethodCount** devolve o último valor recuperado pelo método **FetchAttributes,** sem chamar o serviço de Fila.

```csharp
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Use o padrão de Async-Await com APIs comuns da fila Azure
Este exemplo mostra como usar o padrão Async-Await com APIs comuns da Fila Azure. A amostra chama a versão async de cada um dos métodos dados, isto pode ser visto pela pós-correcção **assínc** de cada método. Quando um método de assínc é usado, o padrão de espera de assínc suspende a execução local até que a chamada termine. Este comportamento permite que o fio atual faça outros trabalhos que ajudem a evitar estrangulamentos de desempenho e melhorem a capacidade de resposta geral da sua aplicação. Para obter mais detalhes sobre como utilizar o padrão Async-Await no .NET, consulte [Async-Await (C# e Visual Basic)](/previous-versions/hh191443(v=vs.140))

```csharp
// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Add the message asynchronously
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Delete the message asynchronously
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Eliminar uma fila
Para eliminar uma fila e todas as mensagens nela contidas, chame o método **Eliminar** no objeto de fila.

```csharp
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]