---
title: Tutorial – trabalhar com filas do armazenamento do Azure-armazenamento do Azure
description: Um tutorial sobre como usar o serviço Fila do Azure para criar filas e inserir, obter e excluir mensagens.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: c8e1d5c1c11c4fdf902c7be7bc03be298e93a8b9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721132"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Tutorial: Trabalhar com filas de armazenamento do Azure

O armazenamento de filas do Azure implementa filas baseadas em nuvem para habilitar a comunicação entre os componentes de um aplicativo distribuído. Cada fila mantém uma lista de mensagens que podem ser adicionadas por um componente de remetente e processadas por um componente de receptor. Com uma fila, seu aplicativo pode ser dimensionado imediatamente para atender à demanda. Este artigo mostra as etapas básicas para trabalhar com uma fila de armazenamento do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Criar uma conta do Storage do Azure
> - Criar a aplicação
> - Adicionar suporte para código assíncrono
> - Criar uma fila
> - Inserir mensagens em uma fila
> - Remover mensagens da fila
> - Excluir uma fila vazia
> - Verificar argumentos de linha de comando
> - Compilar e executar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

- Obtenha sua cópia gratuita do editor de [Visual Studio Code](https://code.visualstudio.com/download) de plataforma cruzada.
- Baixe e instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).
- Se você não tiver uma assinatura atual do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-storage-account"></a>Criar uma conta do Storage do Azure

Primeiro, crie uma conta de armazenamento do Azure. Para obter um guia passo a passo para criar uma conta de armazenamento, consulte o início rápido [criar uma conta de armazenamento](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) .

## <a name="create-the-app"></a>Criar a aplicação

Crie um aplicativo .NET Core chamado **QueueApp**. Para simplificar, esse aplicativo enviará e receberá mensagens por meio da fila.

1. Em uma janela de console (como cmd, PowerShell ou CLI do Azure), use o `dotnet new` comando para criar um novo aplicativo de console com o nome **QueueApp**. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de origem: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Alterne para a pasta **QueueApp** recém-criada e compile o aplicativo para verificar se tudo está bem.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Você deverá ver resultados semelhantes ao seguinte:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Adicionar suporte para código assíncrono

Como o aplicativo usa recursos de nuvem, o código é executado de forma assíncrona. No C#entanto **, Async** e **Await** não eram palavras-chave válidas em C# métodos **principais** até 7,1. Você pode alternar facilmente para esse compilador por meio de um sinalizador no arquivo **csproj** .

1. Na linha de comando no diretório do projeto, digite `code .` para abrir Visual Studio Code no diretório atual. Mantenha a janela de linha de comando aberta. Haverá mais comandos para executar mais tarde. Se você for solicitado a adicionar C# ativos necessários para compilar e depurar, clique no botão **Sim** .

2. Abra o arquivo **QueueApp. csproj** no editor.

3. Adicione `<LangVersion>7.1</LangVersion>` ao primeiro **PropertyGroup** no arquivo de compilação. Certifique-se de adicionar apenas a marca **langversion** , pois seu **TargetFramework** pode ser diferente dependendo da versão do .net instalada.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Salve o arquivo **QueueApp. csproj** .

5. Abra o arquivo de origem **Program.cs** e atualize o método **Main** para ser executado de forma assíncrona. Substituir **void** por um valor de retorno de **tarefa assíncrona** .

   ```csharp
   static async Task Main(string[] args)
   ```

6. Salve o arquivo **Program.cs** .

## <a name="create-a-queue"></a>Criar uma fila

1. Instale os pacotes **Microsoft. Azure. Storage. Common** e **Microsoft. Azure. Storage. Queue** no projeto com o `dotnet add package` comando. Execute os seguintes comandos dotnet na pasta do projeto na janela do console.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. Na parte superior do arquivo **Program.cs** , adicione os namespaces a seguir logo após a `using System;` instrução. Esse aplicativo usa tipos desses namespaces para se conectar ao armazenamento do Azure e trabalhar com filas.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Salve o arquivo **Program.cs** .

### <a name="get-your-connection-string"></a>Obtenha a cadeia de ligação

A biblioteca de cliente usa uma cadeia de conexão para estabelecer a conexão. Sua cadeia de conexão está disponível na seção **configurações** da sua conta de armazenamento no portal do Azure.

1. No navegador da Web, entre no [portal do Azure](https://portal.azure.com/).

2. Navegue para a sua conta de armazenamento no portal do Azure.

3. Selecione **chaves de acesso**.

4. Clique no botão **copiar** à direita do campo **cadeia de conexão** .

![Cadeia de ligação](media/storage-tutorial-queues/get-connection-string.png)

A cadeia de ligação está neste formato:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Adicionar a cadeia de conexão ao aplicativo

Adicione a cadeia de conexão ao aplicativo para que ele possa acessar a conta de armazenamento.

1. Volte para Visual Studio Code.

2. Na classe **programa** , adicione um `private const string connectionString =` membro para conter a cadeia de conexão.

3. Após o sinal de igual, Cole o valor da cadeia de caracteres que você copiou anteriormente em seu portal do Azure. O valor **ConnectionString** será exclusivo para sua conta.

4. Remova o código "Olá, Mundo" do **principal**. Seu código deve ser semelhante ao seguinte, mas com seu valor de cadeia de conexão exclusivo.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. Atualize **Main** para criar um objeto **CloudQueue** , que é passado posteriormente para os métodos Send e Receive.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Guarde o ficheiro.

## <a name="insert-messages-into-the-queue"></a>Inserir mensagens na fila

Crie um novo método para enviar uma mensagem para a fila. Adicione o método a seguir à sua classe de **programa** . Esse método obtém uma referência de fila e, em seguida, cria uma nova fila, caso ela ainda não exista chamando [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Em seguida, ele adiciona a mensagem à fila chamando [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Adicione o seguinte método **SendMessageAsync** à sua classe **programa** .

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Guarde o ficheiro.

Uma mensagem deve estar em um formato que pode ser incluído em uma solicitação XML com codificação UTF-8 e pode ter até 64 KB de tamanho. Se uma mensagem contiver dados binários, recomendamos que você codifique a mensagem em base64.

Por padrão, o tempo de vida máximo para uma mensagem é definido como 7 dias. Você pode especificar qualquer número positivo para a vida útil da mensagem. Para adicionar uma mensagem que não expire, use `Timespan.FromSeconds(-1)` em sua chamada para **AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Remover mensagens da fila

Crie um novo método chamado **ReceiveMessageAsync**. Esse método recebe uma mensagem da fila chamando [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Depois que a mensagem for recebida com êxito, é importante excluí-la da fila para que ela não seja processada mais de uma vez. Depois que a mensagem for recebida, exclua-a da fila chamando [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Adicione o seguinte método **ReceiveMessageAsync** à sua classe **programa** .

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Guarde o ficheiro.

## <a name="delete-an-empty-queue"></a>Excluir uma fila vazia

É uma prática recomendada no final de um projeto identificar se você ainda precisa dos recursos que criou. Os recursos deixados em execução podem custar dinheiro. Se a fila existir, mas estiver vazia, pergunte ao usuário se ele gostaria de excluí-la.

1. Expanda o método **ReceiveMessageAsync** para incluir um prompt para excluir a fila vazia.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Guarde o ficheiro.

## <a name="check-for-command-line-arguments"></a>Verificar argumentos de linha de comando

Se houver argumentos de linha de comando passados para o aplicativo, presuma que eles são uma mensagem a ser adicionada à fila. Junte os argumentos para criar uma cadeia de caracteres. Adicione essa cadeia de caracteres à fila de mensagens chamando o método **SendMessageAsync** que adicionamos anteriormente.

Se não houver argumentos de linha de comando, execute uma operação de recuperação. Chame o método **ReceiveMessageAsync** para recuperar a primeira mensagem na fila.

Por fim, aguarde a entrada do usuário antes de sair chamando **console. ReadLine**.

1. Expanda o método **Main** para verificar argumentos de linha de comando e aguardar a entrada do usuário.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Guarde o ficheiro.

## <a name="complete-code"></a>Código completo

Aqui está a listagem de código completa para este projeto.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Na linha de comando no diretório do projeto, execute o seguinte comando dotnet para compilar o projeto.

   ```console
   dotnet build
   ```

2. Depois que o projeto for compilado com êxito, execute o comando a seguir para adicionar a primeira mensagem à fila.

   ```console
   dotnet run First queue message
   ```

Você deve ver esta saída:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Execute o aplicativo sem argumentos de linha de comando para receber e remover a primeira mensagem na fila.

   ```console
   dotnet run
   ```

4. Continue a executar o aplicativo até que todas as mensagens sejam removidas. Se você executá-lo mais uma vez, receberá uma mensagem informando que a fila está vazia e uma solicitação para excluir a fila.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

1. Criar uma fila
2. Adicionar e remover mensagens de uma fila
3. Excluir uma fila de armazenamento do Azure

Confira o guia de início rápido das filas do Azure para obter mais informações.

> [!div class="nextstepaction"]
> [Início rápido das filas](storage-quickstart-queues-portal.md)
