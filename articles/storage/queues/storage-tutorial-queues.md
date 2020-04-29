---
title: Tutorial - Trabalho com filas de armazenamento Azure - Armazenamento Azure
description: Um tutorial sobre como usar o serviço De Fila Azure para criar filas, inserir, obter e apagar mensagens.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75968203"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Tutorial: Trabalhar com filas de armazenamento Azure

O armazenamento da Fila Azure implementa filas baseadas na nuvem para permitir a comunicação entre componentes de uma aplicação distribuída. Cada fila mantém uma lista de mensagens que podem ser adicionadas por um componente de remetente e processadas por um componente recetor. Com uma fila, a sua aplicação pode escalar imediatamente para satisfazer a procura. Este artigo mostra os passos básicos para trabalhar com uma fila de armazenamento Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Criar uma conta de armazenamento do Azure
> - Criar a aplicação
> - Adicione suporte para código assíncrono
> - Criar uma fila
> - Insira mensagens numa fila
> - Mensagens de defila
> - Apagar uma fila vazia
> - Verifique se existem argumentos de linha de comando
> - Compilar e executar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

- Obtenha a sua cópia gratuita do editor de [Código](https://code.visualstudio.com/download) visual da plataforma cruzada.
- Descarregue e instale o [.NET Core SDK](https://dotnet.microsoft.com/download).
- Se não tiver uma subscrição atual do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

Primeiro, crie uma conta de armazenamento Azure. Para obter um guia passo a passo para criar uma conta de armazenamento, consulte a [conta de armazenamento Criar uma conta](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) de armazenamento rápida.

## <a name="create-the-app"></a>Criar a aplicação

Crie uma aplicação .NET Core chamada **QueueApp**. Para a simplicidade, esta aplicação irá enviar e receber mensagens através da fila.

1. Numa janela de consola (como CMD, PowerShell ou Azure CLI), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome **QueueApp**. Este comando cria um simples projeto C# "Hello World" com um único ficheiro fonte: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Mude para a pasta **queueApp** recém-criada e construa a app para verificar se está tudo bem.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Deve ver resultados semelhantes aos seguintes:

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

## <a name="add-support-for-asynchronous-code"></a>Adicione suporte para código assíncrono

Uma vez que a aplicação utiliza recursos na nuvem, o código funciona assincronicamente. No entanto, c#'s **async** e **aguardando** não eram palavras-chave válidas em métodos **principais** até C# 7.1. Pode facilmente mudar para esse compilador através de uma bandeira no ficheiro **csproj.**

1. A partir da linha de comando `code .` no diretório do projeto, escreva para abrir o Código do Estúdio Visual no atual diretório. Mantenha a janela da linha de comando aberta. Haverá mais ordens para executar mais tarde. Se for solicitado a adicionar ativos C# necessários para construir e depurar, clique no botão **Sim.**

2. Abra o ficheiro **QueueApp.csproj** no editor.

3. Adicione `<LangVersion>7.1</LangVersion>` o primeiro **PropertyGroup** no ficheiro de construção. Certifique-se de que só adiciona a tag **LangVersion,** uma vez que o **targetFramework** pode ser diferente dependendo da versão de .NET que instalou.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Guarde o ficheiro **QueueApp.csproj.**

5. Abra o ficheiro de origem **Program.cs** e atualize o método **Principal** para executar assíncronamente. Substitua o **vazio** por um valor de devolução de **tarefas asincronizada.**

   ```csharp
   static async Task Main(string[] args)
   ```

6. Guarde o ficheiro **Program.cs.**

## <a name="create-a-queue"></a>Criar uma fila

1. Instale os pacotes **Microsoft.Azure.Storage.Common** e **Microsoft.Azure.Storage.Queue** para o projeto com o `dotnet add package` comando. Execute os seguintes comandos do dotnet a partir da pasta do projeto na janela da consola.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. No topo do ficheiro **Program.cs,** adicione os seguintes espaços de nome logo após a `using System;` declaração. Esta aplicação utiliza tipos destes espaços de nome para se conectar ao Armazenamento Azure e trabalhar com filas.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Guarde o ficheiro **Program.cs.**

### <a name="get-your-connection-string"></a>Obtenha a cadeia de ligação

A biblioteca de cliente utiliza uma cadeia de ligação para estabelecer a ligação. A sua cadeia de ligação está disponível na secção **Definições** da sua conta de armazenamento no portal Azure.

1. No seu navegador web, inscreva-se no [portal Azure.](https://portal.azure.com/)

2. Navegue para a sua conta de armazenamento no portal do Azure.

3. Selecione **teclas de acesso**.

4. Clique no botão **Copiar** à direita do campo de **cordas de Ligação.**

![Cadeia de ligação](media/storage-tutorial-queues/get-connection-string.png)

A cadeia de ligação está neste formato:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Adicione a cadeia de ligação à aplicação

Adicione a cadeia de ligação na aplicação para que possa aceder à conta de armazenamento.

1. Mude de volta para visual studio code.

2. Na aula do **Programa,** adicione um `private const string connectionString =` membro para segurar a corda de ligação.

3. Depois do sinal igual, cola o valor de corda que copiaste anteriormente no teu portal Azure. O valor **da ligaçãoString** será exclusivo da sua conta.

4. Retire o código "Hello World" da **Main**. O seu código deve ser semelhante ao seguinte, mas com o seu valor de cadeia de ligação único.

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

5. Atualizar **Principal** para criar um objeto **CloudQueue,** que é posteriormente passado para o envio e receber métodos.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Guarde o ficheiro.

## <a name="insert-messages-into-the-queue"></a>Insira mensagens na fila

Crie um novo método para enviar uma mensagem para a fila. Adicione o seguinte método à sua aula de **Programa.** Este método obtém uma referência de fila, e depois cria uma nova fila se já não existir chamando [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Em seguida, adiciona a mensagem à fila chamando [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Adicione o seguinte método **SendMessageAsync** à sua classe **Program.**

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

Uma mensagem deve estar num formato que possa ser incluído num pedido XML com codificação UTF-8, e pode ter até 64 KB de tamanho. Se uma mensagem contiver dados binários, recomendamos que o Código Base64 consiga a mensagem.

Por predefinição, o tempo máximo de vida para uma mensagem é definido para 7 dias. Pode especificar qualquer número positivo para o tempo de vida da mensagem. Para adicionar uma mensagem que `Timespan.FromSeconds(-1)` não expira, utilize a sua chamada para **AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Mensagens de defila

Crie um novo método chamado **ReceiveMessageAsync**. Este método recebe uma mensagem da fila chamando [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Uma vez que a mensagem é recebida com sucesso, é importante apagá-la da fila para que não seja processada mais do que uma vez. Depois de recebida a mensagem, apague-a da fila, ligando para [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Adicione o seguinte método **ReceiveMessageAsync** à sua classe **Program.**

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

## <a name="delete-an-empty-queue"></a>Apagar uma fila vazia

É uma boa prática no final de um projeto para identificar se ainda precisa dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Se a fila existe mas está vazia, pergunte ao utilizador se gostaria de apagá-la.

1. Expanda o método **ReceiveMessageAsync** para incluir uma solicitação para eliminar a fila vazia.

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

## <a name="check-for-command-line-arguments"></a>Verifique se existem argumentos de linha de comando

Se houver algum argumento de linha de comando passado para a aplicação, assuma que são uma mensagem a ser adicionada à fila. Juntem-se aos argumentos para fazer uma corda. Adicione esta cadeia à fila da mensagem, ligando para o método **SendMessageAsync** que adicionámos anteriormente.

Se não houver argumentos de linha de comando, execute uma operação de recuperação. Ligue para o método **ReceiveMessageAsync** para recuperar a primeira mensagem na fila.

Por fim, aguarde a entrada do utilizador antes de sair, ligando para **console.ReadLine**.

1. Expanda o método **Principal** para verificar se existem argumentos de linha de comando e aguarde a entrada do utilizador.

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

Aqui está a lista completa de código para este projeto.

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

1. A partir da linha de comando no diretório do projeto, executar o seguinte comando dotnet para construir o projeto.

   ```console
   dotnet build
   ```

2. Depois de o projeto construir com sucesso, execute o seguinte comando para adicionar a primeira mensagem à fila.

   ```console
   dotnet run First queue message
   ```

Deverá ver este resultado:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Executar a aplicação sem argumentos de linha de comando para receber e remover a primeira mensagem na fila.

   ```console
   dotnet run
   ```

4. Continue a executar a aplicação até que todas as mensagens sejam removidas. Se o executar mais uma vez, receberá uma mensagem de que a fila está vazia e um pedido para apagar a fila.

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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

1. Criar uma fila
2. Adicione e remova mensagens de uma fila
3. Eliminar uma fila de armazenamento Azure

Confira as filas Azure para obter mais informações.

> [!div class="nextstepaction"]
> [Filas aceleradas](storage-quickstart-queues-portal.md)
