---
title: Transferir dados com a biblioteca do Movimento de Dados para .NET
titleSuffix: Azure Storage
description: Utilize a biblioteca Movimento de Dados para mover ou copiar dados para ou de bolhas e conteúdos de ficheiros. Copie os dados para o Azure Storage a partir de ficheiros locais ou copie dados dentro ou entre contas de armazenamento. Migrar facilmente os seus dados para o Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ec35a651d4d3ef9e0877463329a654bc7491f4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023419"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Transferir dados com a biblioteca do Movimento de Dados

A biblioteca Azure Storage Data Movement é uma biblioteca de código aberto de plataformas cruzadas que é projetada para carregar, descarregar e copiar de bolhas e ficheiros. A biblioteca Data Movement fornece métodos convenientes que não estão disponíveis na biblioteca do cliente do Azure Storage para .NET. Estes métodos proporcionam a capacidade de definir o número de operações paralelas, rastrear o progresso da transferência, facilmente retomar uma transferência cancelada, e muito mais.

Esta biblioteca também utiliza .NET Core, o que significa que pode usá-lo ao construir aplicações .NET para Windows, Linux e macOS. Para saber mais sobre .NET Core, consulte a [documentação .NET Core](https://dotnet.github.io/). Esta biblioteca também funciona para aplicações tradicionais .NET Framework para Windows.

Este documento demonstra como criar uma aplicação de consola .NET Core que funciona no Windows, Linux e macOS e executa os seguintes cenários:

- Faça upload de ficheiros e diretórios para o Blob Storage.
- Defina o número de operações paralelas ao transferir dados.
- Rastrear o progresso da transferência de dados.
- Retomar transferência de dados cancelada.
- Copiar ficheiro de URL para Blob Storage.
- Cópia do Blob Storage para o Blob Storage.

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio Code](https://code.visualstudio.com/)
- Uma [conta de armazenamento Azure](storage-account-create.md)

## <a name="setup"></a>Configuração

1. Visite o [Guia de Instalação do Núcleo .NET](https://www.microsoft.com/net/core) para instalar o Núcleo .NET. Ao selecionar o seu ambiente, escolha a opção de linha de comando.
2. A partir da linha de comando, crie um diretório para o seu projeto. Navegue neste diretório e, em seguida, `dotnet new console -o <sample-project-name>` escreva para criar um projeto de consola C#.
3. Abra este diretório no Código do Estúdio Visual. Este passo pode ser feito rapidamente através da linha de comando digitando `code .` no Windows.
4. Instale a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a partir do Mercado do Código do Estúdio Visual. Reiniciar código de estúdio visual.
5. Neste momento, deve ver duas instruções. Uma delas é a adição de "ativos necessários para construir e depurar". Clique em "sim". Outra solicitação é restaurar as dependências não resolvidas. Clique em "restaurar".
6. Modificar `launch.json` para usar o terminal externo como `.vscode` consola. Esta definição deve ser lida como `"console": "externalTerminal"`
7. O Código do Estúdio Visual permite-lhe depurar aplicações .NET Core. Bata `F5` para executar a sua aplicação e verifique se a sua configuração está a funcionar. Devias ver "Olá Mundo!" impresso na consola.

## <a name="add-the-data-movement-library-to-your-project"></a>Adicione a biblioteca do Movimento de Dados ao seu projeto

1. Adicione a versão mais recente da biblioteca Movimento de Dados à `dependencies` secção do seu `<project-name>.csproj` ficheiro. No momento da escrita, esta versão seria `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Um aviso deve ser exibido para restaurar o seu projeto. Clique no botão "restaurar". Também pode restaurar o seu projeto a partir da linha de comando digitando o comando `dotnet restore` na raiz do seu diretório de projeto.

`<project-name>.csproj`Modificar:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Crie o esqueleto da sua aplicação

A primeira coisa que fazemos é configurar o código "esqueleto" da nossa aplicação. Este código nos solicita um nome de conta de Armazenamento e chave de conta e utiliza essas credenciais para criar um `CloudStorageAccount` objeto. Este objeto é usado para interagir com a nossa conta de Armazenamento em todos os cenários de transferência. O código também nos leva a escolher o tipo de operação de transferência que gostaríamos de executar.

`Program.cs`Modificar:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Faça o upload de um ficheiro local para uma bolha

Adicione os métodos `GetSourcePath` `GetBlob` `Program.cs` e:

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Modificar o `TransferLocalFileToAzureBlob` método:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Este código nos leva para o caminho para um arquivo local, o nome de um recipiente novo ou existente, e o nome de uma nova bolha. O `TransferManager.UploadAsync` método executa o upload usando esta informação.

Bata `F5` para executar a sua candidatura. Pode verificar se o upload ocorreu visualizando a sua conta de Armazenamento com o [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Definir o número de operações paralelas

Uma das características oferecidas pela biblioteca do Movimento de Dados é a capacidade de definir o número de operações paralelas para aumentar o rendimento de transferência de dados. Por predefinição, a biblioteca Movimento de Dados define o número de operações paralelas a 8 * o número de núcleos na sua máquina.

Tenha em mente que muitas operações paralelas num ambiente de baixa largura de banda podem sobrecarregar a ligação da rede e impedir que as operações sejam completamente concluídas. Terá de experimentar esta definição para determinar o que funciona melhor com base na largura de banda disponível.

Vamos adicionar um código que nos permita definir o número de operações paralelas. Vamos também adicionar código que vezes o tempo que a transferência leva para completar.

Adicione um `SetNumberOfParallelOperations` método `Program.cs` para:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Modificar o `ExecuteChoice` método de `SetNumberOfParallelOperations` utilização:

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Modificar o `TransferLocalFileToAzureBlob` método para utilizar um temporizador:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Progresso da transferência de faixas

Saber quanto tempo demorou para os dados transferirem é útil. No entanto, poder ver o progresso da transferência *durante* a operação de transferência seria ainda melhor. Para alcançar este cenário, precisamos de criar um `TransferContext` objeto. O `TransferContext` objeto vem de duas formas: e `SingleTransferContext` `DirectoryTransferContext` . O primeiro é para a transferência de um único ficheiro e este último é para transferir um diretório de ficheiros.

Adicione os métodos `GetSingleTransferContext` `GetDirectoryTransferContext` `Program.cs` e:

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

Modificar o `TransferLocalFileToAzureBlob` método de `GetSingleTransferContext` utilização:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Retomar uma transferência cancelada

Outra funcionalidade conveniente oferecida pela biblioteca do Movimento de Dados é a capacidade de retomar uma transferência cancelada. Vamos adicionar um código que nos permite cancelar temporariamente a transferência digitando e, em `c` seguida, retomar a transferência 3 segundos depois.

`TransferLocalFileToAzureBlob`Modificar:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Até agora, o nosso `checkpoint` valor sempre foi definido `null` para. Agora, se cancelarmos a transferência, recuperamos o último ponto de verificação da nossa transferência, e usamos este novo posto de controlo no nosso contexto de transferência.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Transfira um diretório local para o armazenamento blob

Seria uma desilusão se a biblioteca do Movimento de Dados só pudesse transferir um ficheiro de cada vez. Felizmente, este não é o caso. A biblioteca Do Movimento de Dados fornece a capacidade de transferir um diretório de ficheiros e todas as suas subdiretivas. Vamos adicionar um código que nos permita fazer isso mesmo.

Primeiro, adicione o método `GetBlobDirectory` `Program.cs` para:

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Em seguida, `TransferLocalDirectoryToAzureBlobDirectory` modificar:

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Existem algumas diferenças entre este método e o método para o upload de um único ficheiro. Estamos a usar `TransferManager.UploadDirectoryAsync` e o método que `getDirectoryTransferContext` criámos anteriormente. Além disso, fornecemos agora um `options` valor para a nossa operação de upload, o que nos permite indicar que queremos incluir subdireções no nosso upload.

## <a name="copy-a-file-from-url-to-a-blob"></a>Copie um ficheiro de URL para uma bolha

Agora, vamos adicionar código que nos permite copiar um ficheiro de um URL para uma Blob Azure.

`TransferUrlToAzureBlob`Modificar:

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Um caso importante de utilização para esta funcionalidade é quando precisa de transferir dados de outro serviço de nuvem (por exemplo, AWS) para Azure. Desde que tenha um URL que lhe dê acesso ao recurso, pode facilmente mover esse recurso para Azure Blobs utilizando o `TransferManager.CopyAsync` método. Este método também introduz um novo parâmetro booleano. Definir este parâmetro `true` indica que queremos fazer uma cópia assíncronea do lado do servidor. Definir este parâmetro indica `false` uma cópia sincronizada - o que significa que o recurso é descarregado para a nossa máquina local primeiro e depois enviado para Azure Blob. No entanto, a cópia sincronizada só está atualmente disponível para copiar de um recurso de Armazenamento Azure para outro.

## <a name="copy-a-blob"></a>Copiar uma bolha

Outra funcionalidade que é exclusivamente fornecida pela biblioteca do Movimento de Dados é a capacidade de copiar de um recurso de Armazenamento Azure para outro.

`TransferAzureBlobToAzureBlob`Modificar:

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, CopyMethod.ServiceSideAsyncCopy, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Neste exemplo, definimos o parâmetro booleano `TransferManager.CopyAsync` `false` para indicar que queremos fazer uma cópia sincronizada. Isto significa que o recurso é descarregado para a nossa máquina local primeiro, depois enviado para Azure Blob. A opção de cópia sincronizada é uma ótima maneira de garantir que a sua operação de cópia tem uma velocidade consistente. Em contraste, a velocidade de uma cópia assíncrona do servidor está dependente da largura de banda de rede disponível no servidor, que pode flutuar. No entanto, a cópia sincronizada pode gerar custos adicionais de saída em comparação com a cópia assíncronia. A abordagem recomendada é utilizar uma cópia sincronizada num VM Azure que se encontra na mesma região que a sua conta de armazenamento de origem para evitar o custo da saída.

A aplicação de movimento de dados está agora completa. [A amostra de código completa está disponível no GitHub.](https://github.com/azure-samples/storage-dotnet-data-movement-library-app)

## <a name="next-steps"></a>Passos seguintes

[Documentação de referência do Movimento de Dados de Armazenamento Azure.](https://azure.github.io/azure-storage-net-data-movement)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
