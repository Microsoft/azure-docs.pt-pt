---
title: Transferir dados com a biblioteca do Movimento de Dados para .NET
titleSuffix: Azure Storage
description: Utilize a biblioteca do Movimento de Dados para mover ou copiar dados de ou para o conteúdo de blob e ficheiros. Copie dados para o Armazenamento Azure a partir de ficheiros locais ou copie dados dentro ou entre contas de armazenamento. Emigre facilmente os seus dados para o Armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5b8654500fd697685b38e4f51ba1069e0cf6ccfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942911"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Transferir dados com a biblioteca do Movimento de Dados

A biblioteca Azure Storage Data Movement é uma biblioteca de código aberto de plataforma cruzada que é projetada para carregar, descarregar e copiar de alto desempenho, descarregamento, descarregamento e cópia de blobs e ficheiros. A biblioteca Data Movement fornece métodos convenientes que não estão disponíveis na biblioteca de clientes do Azure Storage para .NET. Estes métodos proporcionam a capacidade de definir o número de operações paralelas, acompanhar o progresso da transferência, retomar facilmente uma transferência cancelada, e muito mais.

Esta biblioteca também utiliza .NET Core, o que significa que pode usá-lo na construção de aplicações .NET para Windows, Linux e macOS. Para saber mais sobre o Núcleo .NET, consulte a [documentação .NET Core](https://dotnet.github.io/). Esta biblioteca também funciona para aplicações tradicionais .NET Framework para Windows.

Este documento demonstra como criar uma aplicação de consola .NET Core que funciona no Windows, Linux e macOS e executa os seguintes cenários:

- Faça upload de ficheiros e diretórios para blob Storage.
- Defina o número de operações paralelas ao transferir dados.
- Acompanhe o progresso da transferência de dados.
- Retomar a transferência de dados cancelada.
- Copiar ficheiro de URL para Blob Storage.
- Cópia do Blob Storage para blob Storage.

## <a name="prerequisites"></a>Pré-requisitos

- [Código de estúdio visual](https://code.visualstudio.com/)
- Uma [conta de armazenamento Azure](storage-account-create.md)

## <a name="setup"></a>Configuração

1. Visite o Guia de [Instalação .NET Core](https://www.microsoft.com/net/core) para instalar .NET Core. Ao selecionar o seu ambiente, escolha a opção linha de comando.
2. A partir da linha de comando, crie um diretório para o seu projeto. Navegue neste diretório `dotnet new console -o <sample-project-name>` e, em seguida, escreva para criar um projeto de consola C#.
3. Abra este diretório no Código do Estúdio Visual. Este passo pode ser feito rapidamente `code .` através da linha de comando digitando no Windows.
4. Instale a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) do Visual Studio Code Marketplace. Reiniciar o Código do Estúdio Visual.
5. Neste momento, deve ver duas indicações. Uma é para adicionar "bens necessários para construir e depurar". Clique em "sim". Outra razão é restaurar dependências não resolvidas. Clique em "restaurar".
6. Modifique-se `launch.json` em baixo `.vscode` para utilizar o terminal externo como consola. Esta definição deve ser lida como`"console": "externalTerminal"`
7. O Visual Studio Code permite-lhe depurar as aplicações .NET Core. Bata `F5` para executar o seu pedido e verifique se a sua configuração está funcionando. Devias ver "Olá Mundo!" impresso para a consola.

## <a name="add-the-data-movement-library-to-your-project"></a>Adicione a biblioteca do Movimento de Dados ao seu projeto

1. Adicione a versão mais recente da `dependencies` biblioteca `<project-name>.csproj` do Movimento de Dados à secção do seu ficheiro. No momento da escrita, esta versão seria`"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Deve ser apresentada uma solicitação para restaurar o seu projeto. Clique no botão "restaurar". Também pode restaurar o seu projeto a `dotnet restore` partir da linha de comando digitando o comando na raiz do seu diretório de projeto.

Modificar: `<project-name>.csproj`

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

## <a name="set-up-the-skeleton-of-your-application"></a>Configurar o esqueleto da sua aplicação

A primeira coisa que fazemos é estabelecer o código "esqueleto" da nossa aplicação. Este código solicita-nos um nome de conta de Armazenamento `CloudStorageAccount` e chave de conta e utiliza essas credenciais para criar um objeto. Este objeto é usado para interagir com a nossa conta de Armazenamento em todos os cenários de transferência. O código também nos leva a escolher o tipo de operação de transferência que gostaríamos de executar.

Modificar: `Program.cs`

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

## <a name="upload-a-local-file-to-a-blob"></a>Faça upload de um ficheiro local para uma bolha

Adicione os `GetSourcePath` `GetBlob` métodos e: `Program.cs`

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

Modificar `TransferLocalFileToAzureBlob` o método:

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

Este código leva-nos para o caminho para um arquivo local, o nome de um recipiente novo ou existente, e o nome de uma nova bolha. O `TransferManager.UploadAsync` método executa o upload usando esta informação.

Bata `F5` para executar o seu pedido. Pode verificar se o upload ocorreu visualizando a sua conta de Armazenamento com o [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Definir o número de operações paralelas

Uma característica oferecida pela biblioteca do Movimento de Dados é a capacidade de definir o número de operações paralelas para aumentar a produção de transferência de dados. Por padrão, a biblioteca data Movement define o número de operações paralelas para 8 * o número de núcleos na sua máquina.

Tenha em mente que muitas operações paralelas num ambiente de baixa largura de banda podem sobrecarregar a ligação da rede e impedir que as operações se completem completamente. Você precisará experimentar com este cenário para determinar o que funciona melhor com base na largura de banda de rede disponível.

Vamos adicionar um código que nos permite definir o número de operações paralelas. Vamos também adicionar código que vezes o tempo que a transferência leva para a transferência ser concluída.

Adicione `SetNumberOfParallelOperations` um `Program.cs`método para:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Modificar `ExecuteChoice` o método `SetNumberOfParallelOperations`de utilização:

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

Modificar `TransferLocalFileToAzureBlob` o método de utilização de um temporizador:

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

## <a name="track-transfer-progress"></a>Progresso da transferência de pista

Saber quanto tempo os dados demoraram a transferir é útil. No entanto, poder ver o progresso da transferência *durante* a operação de transferência seria ainda melhor. Para alcançar este cenário, precisamos criar um `TransferContext` objeto. O `TransferContext` objeto vem de `SingleTransferContext` `DirectoryTransferContext`duas formas: e . O primeiro é para transferir um único ficheiro e este último é para transferir um diretório de ficheiros.

Adicione os `GetSingleTransferContext` `GetDirectoryTransferContext` métodos e: `Program.cs`

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

Modificar `TransferLocalFileToAzureBlob` o método `GetSingleTransferContext`de utilização:

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

Outra funcionalidade conveniente oferecida pela biblioteca do Movimento de Dados é a capacidade de retomar uma transferência cancelada. Vamos adicionar um código que nos permite cancelar temporariamente `c`a transferência digitando e, em seguida, retomar a transferência 3 segundos depois.

Modificar: `TransferLocalFileToAzureBlob`

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

Até agora, `checkpoint` o nosso valor `null`sempre foi definido para. Agora, se cancelarmos a transferência, recuperamos o último ponto de verificação da nossa transferência, e ntão usamos este novo checkpoint no nosso contexto de transferência.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Transfira um diretório local para o armazenamento blob

Seria dececionante se a biblioteca do Movimento de Dados pudesse transferir apenas um ficheiro de cada vez. Felizmente, este não é o caso. A biblioteca data Movement fornece a capacidade de transferir um diretório de ficheiros e todos os seus subdiretórios. Vamos adicionar um código que nos permite fazer isso.

Em primeiro lugar, adicione o método `GetBlobDirectory` a: `Program.cs`

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

Em seguida, modificar: `TransferLocalDirectoryToAzureBlobDirectory`

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

Existem algumas diferenças entre este método e o método para carregar um único ficheiro. Estamos a usar `TransferManager.UploadDirectoryAsync` e `getDirectoryTransferContext` o método que criámos anteriormente. Além disso, fornecemos agora um `options` valor para a nossa operação de upload, o que nos permite indicar que queremos incluir subdiretórios no nosso upload.

## <a name="copy-a-file-from-url-to-a-blob"></a>Copiar um ficheiro de URL para uma bolha

Agora, vamos adicionar código que nos permite copiar um ficheiro de um URL para um Azure Blob.

Modificar: `TransferUrlToAzureBlob`

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

Um caso de utilização importante para esta funcionalidade é quando precisa de transferir dados de outro serviço na nuvem (por exemplo, AWS) para O Azure. Desde que tenha um URL que lhe dê acesso ao recurso, pode facilmente mover `TransferManager.CopyAsync` esse recurso para O Blobs Azure utilizando o método. Este método também introduz um novo parâmetro booleano. Configurar este `true` parâmetro para indicar que queremos fazer uma cópia assíncrona do lado do servidor. Configurar este `false` parâmetro para indicar uma cópia sincronizada - o que significa que o recurso é descarregado para a nossa máquina local primeiro, em seguida, enviado para Azure Blob. No entanto, a cópia sincronizada está atualmente disponível apenas para cópia de um recurso de Armazenamento Azure para outro.

## <a name="copy-a-blob"></a>Copiar uma bolha

Outra funcionalidade que é fornecida exclusivamente pela biblioteca data Movement é a capacidade de copiar de um recurso azure storage para outro.

Modificar: `TransferAzureBlobToAzureBlob`

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
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, true, null, context, cancellationSource.Token);
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

Neste exemplo, colocamos o parâmetro `TransferManager.CopyAsync` booleano para `false` indicar que queremos fazer uma cópia sincronizada. Isto significa que o recurso é descarregado para a nossa máquina local primeiro, e depois enviado para Azure Blob. A opção de cópia sincronizada é uma ótima maneira de garantir que a sua operação de cópia tem uma velocidade consistente. Em contraste, a velocidade de uma cópia assíncrona do lado do servidor depende da largura de banda de rede disponível no servidor, que pode flutuar. No entanto, a cópia sincronizada pode gerar custos de saída adicionais em comparação com a cópia assíncrona. A abordagem recomendada é usar cópia sincronizada num VM Azure que se encontra na mesma região que a sua conta de armazenamento de origem para evitar custos de saída.

A aplicação de movimento de dados está agora completa. A amostra completa de [código está disponível no GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Passos seguintes

[Documentação de referência da biblioteca Azure Storage Data Movement.](https://azure.github.io/azure-storage-net-data-movement)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
