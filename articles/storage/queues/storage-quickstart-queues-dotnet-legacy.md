---
title: 'Quickstart: Use Azure Storage v11 para .NET para gerir uma fila'
description: Neste arranque rápido, aprende-se a usar a biblioteca de clientes Azure Storage para .NET para criar uma fila e adicionar-lhe mensagens. Em seguida, aprende-se a ler e processar mensagens da fila.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 23703dc507aa909aea4711289a4d7d5c5e6a170e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588204"
---
# <a name="quickstart-use-the-azure-storage-sdk-v11-for-net-to-manage-a-queue"></a>Quickstart: Use o Azure Storage SDK v11 para .NET para gerir uma fila

Neste arranque rápido, aprende-se a usar a biblioteca de clientes Azure Storage v11 para .NET para criar uma fila e adicionar-lhe mensagens. Em seguida, aprende-se a ler e processar mensagens da fila.

> [!NOTE]
> Este quickstart utiliza uma versão antiga da biblioteca de clientes Azure Queue Storage. Para começar com a versão mais recente, consulte [Quickstart: Azure Queue Storage Client library v12 for.NET](storage-quickstart-queues-dotnet.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Em seguida, transfira e instale o .NET Core 2.0 referente ao seu sistema operativo. Se estiver a executar o Windows, pode instalar o Visual Studio e utilizar o .NET Framework, se preferir. Também pode optar por instalar um editor para utilizar com o sistema operativo.

### <a name="windows"></a>Windows

- Instale o [.NET Core para Windows](https://www.microsoft.com/net/download/windows) ou o [.NET Framework](https://www.microsoft.com/net/download/windows) (incluído no Visual Studio para Windows)
- Instale o [Visual Studio para Windows](https://www.visualstudio.com/). Se estiver a utilizar o .NET Core, instalar o Visual Studio é opcional.

Para obter informações sobre como escolher entre o .NET Core e o .NET Framework, veja [Escolher entre o .NET Core e o .NET Framework para aplicações de servidor](/dotnet/standard/choosing-core-framework-server).

### <a name="linux"></a>Linux

- Instale o [.NET Core para Linux](https://www.microsoft.com/net/download/linux)
- Opcionalmente, instale o [Visual Studio Code](https://www.visualstudio.com/) e a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

### <a name="macos"></a>macOS

- Instale o [.NET Core para macOS](https://www.microsoft.com/net/download/macos).
- Opcionalmente, instale o [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

A aplicação de exemplo utilizada neste início rápido é uma aplicação de consola básica. Pode explorar a aplicação de exemplo no [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

Use [o Git](https://git-scm.com/) para descarregar uma cópia da aplicação para o seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Este comando clona o repositório da sua pasta Git local. Para abrir a solução Visual Studio, procure a `storage-queues-dotnet-quickstart` pasta, abra-a e clique duas vezes `storage-queues-dotnet-quickstart.sln` em .

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Para executar a aplicação, tem de indicar a cadeia de ligação da sua conta de armazenamento. A aplicação de exemplo lê a cadeia de ligação da variável de um ambiente e utiliza-a para autorizar os pedidos ao Armazenamento do Azure.

Após ter copiado a cadeia de ligação, escreva-a numa nova variável de ambiente no computador local que está a executar a aplicação. Para definir a variável de ambiente, abra uma janela da consola e siga as instruções relevantes para o seu sistema operativo. Substitua `<yourconnectionstring>` pela cadeia de ligação real:

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente, poderá ter de reiniciar todos os programas em execução que irão precisar de ler a variável de ambiente, incluindo a janela da consola. Por exemplo, se estiver a utilizar o Visual Studio como editor, reinicie o Visual Studio antes de executar o exemplo.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

### <a name="macos"></a>macOS

Edite o seu .bash_profile e adicione a variável de ambiente:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.

## <a name="run-the-sample"></a>Executar o exemplo

A aplicação da amostra cria uma fila e adiciona-lhe uma mensagem. A aplicação primeiro espreita a mensagem sem a retirar da fila, depois recupera a mensagem e apaga-a da fila.

### <a name="windows"></a>Windows

Se estiver a utilizar o Visual Studio como seu editor, pode pressionar `F5` para correr.

Caso contrário, navegue para o diretório de aplicações e execute a aplicação com o comando `dotnet run`.

```
dotnet run
```

### <a name="linux"></a>Linux

Navegue para o diretório de aplicações e execute a aplicação com o comando `dotnet run`.

```
dotnet run
```

### <a name="macos"></a>macOS

Navegue para o diretório de aplicações e execute a aplicação com o comando `dotnet run`.

```
dotnet run
```

O resultado do exemplo de aplicação é semelhante ao seguinte:

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

Em seguida, explore o código de exemplo para compreender como funciona.

### <a name="try-parsing-the-connection-string"></a>Experimentar a análise da cadeia de ligação

A amostra verifica primeiro que a variável ambiente contém uma cadeia de ligação que pode ser analisada para criar um [`CloudStorageAccount`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) objeto que aponta para a conta de armazenamento. Para verificar se a cadeia de ligação é válida, a amostra utiliza o [`TryParse`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) método. Se `TryParse` for bem sucedido, rubrica a `storageAccount` variável e devoluções `true` .

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>Criar a fila

Primeiro, a amostra cria uma fila e adiciona-lhe uma mensagem.

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name
// is unique in your storage account.
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Adicionar uma mensagem

Em seguida, a amostra adiciona uma mensagem na parte de trás da fila.

Uma mensagem deve estar num formato que possa ser incluído num pedido de XML com codificação UTF-8, podendo ter até 64 KB de tamanho. Se uma mensagem contiver dados binários, recomendamos que você Base64-codificar a mensagem.

Por predefinição, o tempo máximo de vida para uma mensagem é definido para 7 dias. Pode especificar qualquer número positivo para o tempo de vida da mensagem.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

Para adicionar uma mensagem que não expire, utilize `Timespan.FromSeconds(-1)` na sua chamada para [`AddMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) .

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>Espreite uma mensagem da fila

A amostra mostra como espreitar uma mensagem de uma fila. Quando espreita uma mensagem, pode ler o conteúdo da mensagem. No entanto, a mensagem permanece visível para outros clientes, para que outro cliente possa posteriormente recuperar e processar a mensagem.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's
// visibility, so that another client can still retrieve and process it.
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Remover uma mensagem da fila

A amostra também mostra como desescoar uma mensagem. Quando descoda uma mensagem, recupera a mensagem da frente da fila e torna-a temporariamente invisível para outros clientes. Por defeito, uma mensagem permanece invisível durante 30 segundos. Durante este tempo, o seu código pode processar a mensagem. Para terminar de descoduar a mensagem, apaga a mensagem imediatamente após o processamento, para que outro cliente não desaque a mesma mensagem.

Se o seu código não processar uma mensagem devido a uma falha de hardware ou software, então a mensagem torna-se visível novamente após o período de invisibilidade ter caducado. Outro cliente pode recuperar a mesma mensagem e tentar novamente.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>Limpar os recursos

A amostra limpa os recursos que criou eliminando a fila. A eliminação da fila também elimina todas as mensagens que contém.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>Recursos para o desenvolvimento de aplicações .NET com filas

Consulte estes recursos adicionais para o desenvolvimento de .NET com armazenamento de fila Azure:

### <a name="binaries-and-source-code"></a>Binários e código fonte

- Descarregue os pacotes NuGet para a versão mais recente da biblioteca de [clientes Azure Storage para .NET](/dotnet/api/overview/azure/storage)
  - [Common](https://www.nuget.org/packages/microsoft.azure.storage.common/)
  - [Filas](https://www.nuget.org/packages/Azure.Storage.Queues/)
- Veja o [código fonte da biblioteca de cliente .NET](https://github.com/Azure/azure-storage-net) no GitHub.

### <a name="azure-storage-client-library-reference-and-samples"></a>Referência e amostras da biblioteca do cliente de Azure Storage

- Consulte as [bibliotecas de clientes do Azure Storage para obter](/dotnet/api/overview/azure/storage) mais informações sobre as bibliotecas de clientes .NET.
- Explore [as amostras de armazenamento de fila escritas](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) utilizando a biblioteca do cliente .NET.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a adicionar mensagens a uma fila, a espreitar mensagens de uma fila e a descaitar e a processar mensagens usando .NET.

> [!div class="nextstepaction"]
> [Comunicar entre aplicações com armazenamento de fila Azure](/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- Para saber mais sobre o .NET Core, veja [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
