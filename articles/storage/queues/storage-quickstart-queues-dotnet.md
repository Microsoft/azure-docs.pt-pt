---
title: 'Início rápido: Usar o .NET para criar uma fila no armazenamento do Azure'
description: Neste guia de início rápido, você aprende a usar a biblioteca de cliente de armazenamento do Azure para .NET para criar uma fila e adicionar mensagens a ela. Em seguida, você aprende a ler e processar mensagens da fila.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/06/2018
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: cbrooks
ms.openlocfilehash: d3706f8585c2644a31bf1f418f5425e0fa58d2a0
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721262"
---
# <a name="quickstart-use-net-to-create-a-queue-in-azure-storage"></a>Início rápido: Usar o .NET para criar uma fila no armazenamento do Azure

Neste guia de início rápido, você aprende a usar a biblioteca de cliente de armazenamento do Azure para .NET para criar uma fila e adicionar mensagens a ela. Em seguida, você aprende a ler e processar mensagens da fila. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Em seguida, transfira e instale o .NET Core 2.0 referente ao seu sistema operativo. Se estiver a executar o Windows, pode instalar o Visual Studio e utilizar o .NET Framework, se preferir. Também pode optar por instalar um editor para utilizar com o sistema operativo.

### <a name="windows"></a>Windows

- Instale o [.NET Core para Windows](https://www.microsoft.com/net/download/windows) ou o [.NET Framework](https://www.microsoft.com/net/download/windows) (incluído no Visual Studio para Windows)
- Instale o [Visual Studio para Windows](https://www.visualstudio.com/). Se estiver a utilizar o .NET Core, instalar o Visual Studio é opcional.  

Para obter informações sobre como escolher entre o .NET Core e o .NET Framework, veja [Escolher entre o .NET Core e o .NET Framework para aplicações de servidor](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

### <a name="linux"></a>Linux

- Instale o [.NET Core para Linux](https://www.microsoft.com/net/download/linux)
- Opcionalmente, instale o [Visual Studio Code](https://www.visualstudio.com/) e a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)

### <a name="macos"></a>macOS

- Instale o [.NET Core para macOS](https://www.microsoft.com/net/download/macos).
- Opcionalmente, instale o [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

A aplicação de exemplo utilizada neste início rápido é uma aplicação de consola básica. Pode explorar a aplicação de exemplo no [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

Utilize o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Este comando clona o repositório para a sua pasta local do git. Para abrir a solução do Visual Studio, procure a pasta *armazenamento-filas-dotnet-início rápido* , abra-a e clique duas vezes em *Storage-Queues-dotnet-QuickStart. sln*. 

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

O aplicativo de exemplo cria uma fila e adiciona uma mensagem a ela. O aplicativo inspeciona primeiro a mensagem sem removê-la da fila e, em seguida, recupera a mensagem e a exclui da fila.

### <a name="windows"></a>Windows

Se estiver a utilizar o Visual Studio como editor, pode premir **F5** para executar. 

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

O exemplo primeiro verifica se a variável de ambiente contém uma cadeia de conexão que pode ser analisada para criar um objeto [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) apontando para a conta de armazenamento. Para verificar se a cadeia de conexão é válida, o exemplo usa o método [TryParse](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) . Se o método **TryParse** for bem-sucedido, inicializa a variável *storageAccount* e devolve o valor **true**.

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

Primeiro, o exemplo cria uma fila e adiciona uma mensagem a ela. 

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name 
// is unique in your storage account. 
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Adicionar uma mensagem

Em seguida, o exemplo adiciona uma mensagem à parte posterior da fila. 

Uma mensagem deve estar em um formato que pode ser incluído em uma solicitação XML com codificação UTF-8 e pode ter até 64 KB de tamanho. Se uma mensagem contiver dados binários, recomendamos que você codifique a mensagem em base64.

Por padrão, o tempo de vida máximo para uma mensagem é definido como 7 dias. Você pode especificar qualquer número positivo para a vida útil da mensagem.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

Para adicionar uma mensagem que não expire, use `Timespan.FromSeconds(-1)` em sua chamada para [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>Inspecionar uma mensagem da fila

O exemplo mostra como inspecionar uma mensagem de uma fila. Ao inspecionar uma mensagem, você pode ler o conteúdo da mensagem. No entanto, a mensagem permanece visível para outros clientes, para que outro cliente possa recuperar e processar a mensagem posteriormente.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's 
// visibility, so that another client can still retrieve and process it. 
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Remover uma mensagem da fila

O exemplo também mostra como remover uma mensagem da fila. Ao remover uma mensagem da fila, você recupera a mensagem da frente da fila e a renderiza temporariamente invisível para outros clientes. Por padrão, uma mensagem permanece invisível por 30 segundos. Durante esse tempo, seu código pode processar a mensagem. Para concluir a remoção da fila da mensagem, você exclui a mensagem imediatamente após o processamento, para que outro cliente não faça a fila da mesma mensagem.

Se o código não processar uma mensagem devido a uma falha de hardware ou de software, a mensagem ficará visível novamente depois que o período de invisibilidade tiver se passado. Outro cliente pode recuperar a mesma mensagem e tentar novamente.

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

### <a name="clean-up-resources"></a>Limpar recursos

O exemplo limpa os recursos que ele criou excluindo a fila. Excluir a fila também exclui todas as mensagens que ela contém.

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

## <a name="resources-for-developing-net-applications-with-queues"></a>Recursos para desenvolvimento de aplicativos .NET com filas

Consulte estes recursos adicionais para o desenvolvimento do .NET com filas do Azure:

### <a name="binaries-and-source-code"></a>Binários e código fonte

- Baixe os pacotes NuGet para a versão mais recente da [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage/client)
    - [Common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
    - [filas](https://www.nuget.org/packages/Azure.Storage.Queues/)
- Veja o [código fonte da biblioteca de cliente .NET](https://github.com/Azure/azure-storage-net) no GitHub.

### <a name="client-library-reference-and-samples"></a>Referência e exemplos da biblioteca de cliente

- Consulte a [Referência da API de .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage) para obter mais informações sobre a biblioteca de cliente .NET.
- Explore [amostras de armazenamento de filas](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) gravadas usando a biblioteca de cliente .net.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você aprendeu a adicionar mensagens a uma fila, a inspecionar mensagens de uma fila e a remover da fila e processar mensagens usando o .NET. 

> [!div class="nextstepaction"]
> [Comunicar entre aplicativos com o armazenamento de filas do Azure](https://docs.microsoft.com/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- Para saber mais sobre o .NET Core, veja [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
