---
title: Tutorial - trabalho com as filas de armazenamento do Azure - armazenamento do Azure
description: Um tutorial sobre como utilizar o serviço de fila do Azure para criar filas e insert, obter e eliminar as mensagens.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: cbrooks
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/24/2019
ms.openlocfilehash: 08ef140eb860637cc0c09619abe7051cc007e99f
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540300"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Tutorial: Trabalhar com filas de armazenamento do Azure

Armazenamento de filas do Azure implementa filas com base na cloud para ativar a comunicação entre os componentes de uma aplicação distribuída. Cada fila mantém uma lista de mensagens que podem ser adicionados por um componente de remetente e processados por um componente de destinatário. Com uma fila, o seu aplicativo pode dimensionar imediatamente para satisfazer a procura. Este artigo mostra as etapas básicas para trabalhar com uma fila do armazenamento do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Criar uma conta do Storage do Azure
> - Criar a aplicação
> - Adicionar suporte para o código assíncrono
> - Criar uma fila
> - Inserir mensagens numa fila
> - Remover da fila de mensagens
> - Eliminar fila vazia
> - Verificação de argumentos da linha de comandos
> - Compilar e executar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

- Obter a sua cópia gratuita da plataforma cruzada [Visual Studio Code](https://code.visualstudio.com/download) editor.
- Transferir e instalar o [.NET Core SDK](https://dotnet.microsoft.com/download).
- Se não tiver uma subscrição do Azure atual, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-storage-account"></a>Criar uma conta do Storage do Azure

Primeiro, crie uma conta de armazenamento do Azure. Para um guia passo a passo para criar uma conta de armazenamento, consulte a [criar uma conta de armazenamento](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) início rápido.

## <a name="create-the-app"></a>Criar a aplicação

Criar uma aplicação .NET Core com o nome **QueueApp**. Para simplificar, esta aplicação irá enviar e receber mensagens através da fila.

1. Na janela de consola (por exemplo, CMD, o PowerShell ou da CLI do Azure), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome **QueueApp**. Este comando cria um simples "Hello World" C# projeto com um único arquivo de origem: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Mude para o recém-criado **QueueApp** pasta e a aplicação para verificar que tudo estiver bem de compilação.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Deverá ver resultados semelhantes ao seguinte:

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

## <a name="add-support-for-asynchronous-code"></a>Adicionar suporte para o código assíncrono

Uma vez que a aplicação utiliza recursos na cloud, o código é executada assincronicamente. No entanto, C#da **async** e **await** não eram válidas palavras-chave no **Main** métodos até C# 7.1. Pode mudar com facilidade para esse compilador por meio de um sinalizador no **csproj** ficheiro.

1. A partir da linha de comandos no diretório do projeto, escreva `code .` para abrir o Visual Studio Code no diretório atual. Mantenha a janela da linha de comandos aberta. Haverá mais comandos para executar mais tarde. Se lhe for pedido para adicionar C# recursos necessários para criar e depurar, clique nas **Sim** botão.

2. Abra o **QueueApp.csproj** ficheiro no editor.

3. Adicione `<LangVersion>7.1</LangVersion>` na primeira **PropertyGroup** no arquivo de compilação. Certifique-se de que apenas adicionar a **LangVersion** assinalar como sua **TargetFramework** pode ser diferente dependendo de qual versão do .NET que instalou.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Guardar a **QueueApp.csproj** ficheiro.

5. Abra o **Program.cs** da origem de atualização de ficheiros e o **Main** método para executar de maneira assíncrona. Substitua **void** com um **async Task** valor de retorno.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Guardar a **Program.cs** ficheiro.

## <a name="create-a-queue"></a>Criar uma fila

1. Instalar o **Microsoft.Azure.Storage.Common** e **Microsoft.Azure.Storage.Queue** pacotes para o projeto com o `dotnet add package` comando. Execute os seguintes comandos do dotnet da pasta de projeto na janela da consola.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. Na parte superior a **Program.cs** do ficheiro, adicione os seguintes espaços de nomes logo após o `using System;` instrução. Esta aplicação utiliza tipos a partir desses espaços de nomes para estabelecer ligação ao armazenamento do Azure e trabalhar com as filas.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Guardar a **Program.cs** ficheiro.

### <a name="get-your-connection-string"></a>Obtenha a cadeia de ligação

A biblioteca de cliente usa uma cadeia de ligação para estabelecer a ligação. A cadeia de ligação está disponível na **definições** secção da sua conta de armazenamento no portal do Azure.

1. No seu browser, inicie sessão para o [portal do Azure](https://portal.azure.com/).

2. Navegue para a sua conta de armazenamento no portal do Azure.

3. Selecione **chaves de acesso**.

4. Clique nas **cópia** botão à direita do **cadeia de ligação** campo.

![Cadeia de ligação](media/storage-tutorial-queues/get-connection-string.png)

A cadeia de ligação está neste formato:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Adicione a cadeia de ligação para a aplicação

Adicione a cadeia de ligação para a aplicação para que ele possa acessar a conta de armazenamento.

1. Mude novamente para o Visual Studio Code.

2. Na **programa** , adicione um `private const string connectionString =` membro para armazenar a cadeia de ligação.

3. Depois do sinal, cole o valor de cadeia que copiou anteriormente no portal do Azure. O **connectionString** valor será exclusivo para a sua conta.

4. Remover o código de "Hello World" partir **Main**. Seu código deve ter um aspeto semelhante ao seguinte, mas com o valor da cadeia de ligação exclusivo.

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

5. Atualização **Main** para criar um **CloudQueue** objeto, o que mais tarde é passado para o envio e receção de métodos.

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

Crie um novo método para enviar uma mensagem na fila. Adicione o método seguinte à sua **programa** classe. Este método obtém uma referência de fila, em seguida, cria uma nova fila se ainda não exista chamando [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Em seguida, adiciona a mensagem à fila chamando [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Adicione as seguintes **SendMessageAsync** método para sua **programa** classe.

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

Uma mensagem deve estar num formato que pode ser incluído numa solicitação XML com codificação UTF-8 e pode ser até 64 KB de tamanho. Se uma mensagem contém dados binários, recomendamos que codificar para Base64 a mensagem.

Por predefinição, o máximo tempo de vida de uma mensagem é definido como 7 dias. Pode especificar qualquer número positivo para a mensagem time-to-live. Para adicionar uma mensagem que não expire, utilize `Timespan.FromSeconds(-1)` na sua chamada a **AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Remover da fila de mensagens

Criar um novo método chamado **ReceiveMessageAsync**. Esse método recebe uma mensagem da fila chamando [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Assim que a mensagem é recebida com êxito, é importante para eliminá-lo da fila, para que ele não é processado mais do que uma vez. Depois da mensagem é recebida, eliminá-lo da fila chamando [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Adicione as seguintes **ReceiveMessageAsync** método para sua **programa** classe.

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

## <a name="delete-an-empty-queue"></a>Eliminar fila vazia

É melhor prática no final de um projeto para identificar se ainda precisa que os recursos que criou. Pode executar esquerda de recursos custa dinheiro. Se a fila existe, mas está vazia, peça ao usuário se ele deseja eliminá-lo.

1. Expanda a **ReceiveMessageAsync** método para incluir uma linha de comandos para eliminar a fila vazia.

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

## <a name="check-for-command-line-arguments"></a>Verificação de argumentos da linha de comandos

Se existirem quaisquer argumentos de linha de comandos transmitidos para a aplicação, supor que eles são uma mensagem a ser adicionado à fila. Junte-se os argumentos em conjunto para garantir uma cadeia de caracteres. Adicionar essa cadeia de caracteres para a fila de mensagens ao chamar o **SendMessageAsync** método que adicionamos anteriormente.

Se não houver nenhum argumento da linha de comandos, execute uma operação de recuperação. Chamar o **ReceiveMessageAsync** método para recuperar a primeira mensagem na fila.

Por fim, aguarde pela intervenção do utilizador antes de sair chamando **Console. readline**.

1. Expanda a **Main** método para verificar a existência de argumentos da linha de comandos e aguardar a entrada do usuário.

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

Aqui está o código completo de listagem para este projeto.

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

1. A partir da linha de comandos no diretório do projeto, execute o seguinte comando do dotnet para compilar o projeto.

   ```console
   dotnet build
   ```

2. Depois do projeto é compilado com êxito, execute o seguinte comando para adicionar a primeira mensagem à fila.

   ```console
   dotnet run First queue message
   ```

Verá esta saída:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Execute a aplicação sem argumentos da linha de comandos para receber e remover a primeira mensagem na fila.

   ```console
   dotnet run
   ```

4. Continue a executar a aplicação até que todas as mensagens são removidas. Se executá-lo mais uma vez, obterá uma mensagem que a fila está vazia e uma linha de comandos para eliminar a fila.

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
3. Eliminar uma fila do armazenamento do Azure

Consulte o guia de introdução de filas do Azure para obter mais informações.

> [!div class="nextstepaction"]
> [Início rápido de filas](storage-quickstart-queues-portal.md)
