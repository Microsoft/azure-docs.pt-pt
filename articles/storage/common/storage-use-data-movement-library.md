---
title: Transferir dados com a biblioteca de movimento de dados de armazenamento do Microsoft Azure | Documentos da Microsoft
description: Utilize a biblioteca de movimento de dados para mover ou copiar dados de ou para BLOBs e ficheiros de conteúdo. Copiar dados para o armazenamento do Azure de arquivos locais ou copiar dados em ou entre contas de armazenamento. Migre facilmente os dados ao armazenamento do Azure.
services: storage
author: seguler
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 966518f17392e6158089cb5b6df96f41674fed41
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996144"
---
# <a name="transfer-data-with-the-microsoft-azure-storage-data-movement-library"></a>Transferir dados com a biblioteca de movimento de dados de armazenamento do Microsoft Azure

## <a name="overview"></a>Descrição geral
A biblioteca de movimento de dados de armazenamento do Microsoft Azure é uma biblioteca de código-fonte aberto para várias plataformas que foi concebida para elevado desempenho do carregamento, descarregamento e cópia de ficheiros e Blobs de armazenamento do Azure. Esta biblioteca é a estrutura de movimento de dados principal que alimenta [AzCopy](../storage-use-azcopy.md). A biblioteca de movimento de dados fornece métodos convenientes que não estão disponíveis na nossa tradicional [biblioteca de clientes de armazenamento de Azure .NET](../blobs/storage-dotnet-how-to-use-blobs.md). Isto inclui a capacidade de definir o número de operações simultâneas, controlar o progresso da transferência, retome facilmente uma transferência foi cancelada e muito mais.

Esta biblioteca também usa o .NET Core, o que significa que pode usá-lo ao criar aplicações de .NET para Windows, Linux e macOS. Para saber mais sobre o .NET Core, consulte a [documentação .NET Core](https://dotnet.github.io/). Esta biblioteca também funciona para aplicações tradicionais do .NET Framework para Windows.

Este documento demonstra como criar uma aplicação de consola .NET Core que é executado no Windows, Linux e macOS e executa os seguintes cenários:

- Carregar ficheiros e diretórios para o armazenamento de Blobs.
- Defina o número de operações simultâneas quando a transferência de dados.
- Progresso da transferência de dados track.
- Retome a transferência de dados foi cancelada.
- Copie o ficheiro de URL para o armazenamento de Blobs.
- Copiar do armazenamento de BLOBs no armazenamento de Blobs.

**O que precisa:**

* [Visual Studio Code](https://code.visualstudio.com/)
* Uma [Conta do Storage do Azure](storage-quickstart-create-account.md)

> [!NOTE]
> Este guia assume que já está familiarizado com o [armazenamento do Azure](https://azure.microsoft.com/services/storage/). Se não, lendo o [introdução ao armazenamento do Azure](storage-introduction.md) documentação é útil. Mais importante, precisa [criar uma conta de armazenamento](storage-quickstart-create-account.md) para começar a utilizar a biblioteca de movimento de dados.
>
>

## <a name="setup"></a>Configurar

1. Visite o [guia de instalação do .NET Core](https://www.microsoft.com/net/core) para instalar o .NET Core. Ao selecionar o seu ambiente, escolha a opção da linha de comandos.
2. A partir da linha de comandos, crie um diretório para o seu projeto. Navegue para este diretório, em seguida, escreva `dotnet new console -o <sample-project-name>` para criar um projeto de consola c#.
3. Abra este diretório no Visual Studio Code. Este passo pode ser feito rapidamente através da linha de comando digitando `code .` no Windows.
4. Instalar o [extensão c#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) do mercado de código do Visual Studio. Reinicie o Visual Studio Code.
5. Neste ponto, verá dois pedidos. Um é para adicionar "recursos necessários para compilar e depurar." Clique em "Sim". É outro prompt para restaurar as dependências não resolvidas. Clique em "restaurar".
6. Modificar `launch.json` em `.vscode` para utilizar o terminal externo como uma consola. Esta definição deve ler como ` "console": "externalTerminal"`
7. Código do Visual Studio permite depurar aplicativos do .NET Core. Pressionar `F5` para executar a sua aplicação e certifique-se de que a configuração está a funcionar. Deverá ver "Hello World!" impresso no console.

## <a name="add-data-movement-library-to-your-project"></a>Adicionar a biblioteca de movimento de dados ao seu projeto

1. Adicionar a versão mais recente da biblioteca de movimento de dados para o `dependencies` secção do seu `<project-name>.csproj` ficheiro. No momento da escrita, seria nesta versão `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Uma linha de comandos deve apresentar para restaurar o seu projeto. Clique no botão "restaurar". Também pode restaurar seu projeto a partir da linha de comandos, escrevendo o comando `dotnet restore` na raiz do diretório do seu projeto.

Modificar `<project-name>.csproj`:

    <Project Sdk="Microsoft.NET.Sdk">

        <PropertyGroup>
            <OutputType>Exe</OutputType>
            <TargetFramework>netcoreapp2.0</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
            <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
            </ItemGroup>
        </Project>

## <a name="set-up-the-skeleton-of-your-application"></a>Configurar o esqueleto da sua aplicação
O primeira coisa que fazemos é configurar o código de "esqueleto" de nosso aplicativo. Este código pede-numa chave de conta e o nome da conta de armazenamento e utiliza essas credenciais para criar um `CloudStorageAccount` objeto. Este objeto é utilizado para interagir com nossa conta de armazenamento em todos os cenários de transferência. O código também avisa-nos para escolher o tipo de operação de transferência, gostaríamos de executar.

Modificar `Program.cs`:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.DataMovement;

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

## <a name="transfer-local-file-to-azure-blob"></a>Transferir o ficheiro local para o Blob do Azure
Adicione os métodos `GetSourcePath` e `GetBlob` para `Program.cs`:

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

Este código pede-no caminho para um ficheiro local, o nome de um contentor novo ou existente e o nome de um blob de novo. O `TransferManager.UploadAsync` método executa o carregamento usando essas informações.

Pressionar `F5` para executar a sua aplicação. Pode verificar que o carregamento ocorreu ao visualizar a sua conta de armazenamento com o [Explorador de armazenamento do Microsoft Azure](https://storageexplorer.com/).

## <a name="set-number-of-parallel-operations"></a>Número de conjunto de operações simultâneas
Um ótimo recurso oferecido pela biblioteca de movimento de dados é a capacidade de definir o número de operações simultâneas para aumentar o débito de transferência de dados. Por predefinição, a biblioteca de movimento de dados define o número de operações simultâneas para 8 * o número de núcleos na sua máquina.

Tenha em atenção que muitas operações simultâneas num ambiente de largura de banda baixa podem sobrecarregar a ligação de rede e, na verdade, impedir de totalmente serem concluídas. Precisará fazer experiências com esta definição para determinar o que funciona melhor com base na sua largura de banda de rede disponível.

Vamos adicionar algum código que nos permite definir o número de operações simultâneas. Vamos também adicionar código vezes o tempo que demora a transferência concluir.

Adicionar uma `SetNumberOfParallelOperations` método para `Program.cs`:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Modificar a `ExecuteChoice` método a utilizar `SetNumberOfParallelOperations`:

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

Modificar o `TransferLocalFileToAzureBlob` método a utilizar um temporizador:

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

## <a name="track-transfer-progress"></a>Controlar o progresso de transferência
É ótimo saber quanto tempo demorou para nossos dados a transferir. No entanto, a capacidade de ver o progresso da transferência de nosso *durante a* a operação de transferência seria ainda melhor. Para alcançar este cenário, temos de criar um `TransferContext` objeto. O `TransferContext` objeto vem em dois formatos: `SingleTransferContext` e `DirectoryTransferContext`. O primeiro é para a transferência de um único ficheiro (que é o que estamos fazendo agora) e a última opção é para a transferência de um diretório de ficheiros (o que estamos a adicionar mais tarde).

Adicione os métodos `GetSingleTransferContext` e `GetDirectoryTransferContext` para `Program.cs`:

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

Modificar a `TransferLocalFileToAzureBlob` método a utilizar `GetSingleTransferContext`:

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

## <a name="resume-a-canceled-transfer"></a>Retomar uma transferência foi cancelada
Outro recurso conveniente oferecido pela biblioteca de movimento de dados é a capacidade de retomar uma transferência foi cancelada. Vamos adicionar algum código que permite-nos temporariamente cancelar a transferência, escrevendo `c`e, em seguida, retomar a transferência de 3 segundos mais tarde.

Modificar `TransferLocalFileToAzureBlob`:

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

Até agora, nosso `checkpoint` valor sempre foi definido como `null`. Agora, se estamos a cancelar a transferência, podemos recuperar o último ponto de verificação de nossos transferência, em seguida, utilize este novo ponto de verificação no nosso contexto de transferência.

## <a name="transfer-local-directory-to-azure-blob-directory"></a>Diretório de local de transferência para o diretório de Blobs do Azure
Seria desapontador se a biblioteca de movimento de dados só pode transferir um ficheiro ao mesmo tempo. Felizmente, isso não é o caso. A biblioteca de movimento de dados fornece a capacidade de transferir um diretório de ficheiros e todos os seus subdiretórios. Vamos adicionar algum código que nos permite fazer exatamente isso.

Em primeiro lugar, adicione o método `GetBlobDirectory` para `Program.cs`:

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

Em seguida, modificar `TransferLocalDirectoryToAzureBlobDirectory`:

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

Existem algumas diferenças entre esse método e o método para carregar um único arquivo. Agora estamos usando `TransferManager.UploadDirectoryAsync` e o `getDirectoryTransferContext` método que criámos anteriormente. Além disso, atualmente, fornecemos uma `options` valor a nossa operação de carregamento, o que permite-nos indicar que queremos incluir subdiretórios no nosso carregamento.

## <a name="copy-file-from-url-to-azure-blob"></a>Copiar o ficheiro de URL para Blob do Azure
Agora, vamos adicionar código que permite copiar um ficheiro a partir de uma URL para um Blob do Azure.

Modificar `TransferUrlToAzureBlob`:

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

Um caso de uso importante para esta funcionalidade é quando precisa mover dados de outro serviço em nuvem (por exemplo, o AWS) para o Azure. Desde que tiver um URL que lhe dá acesso ao recurso, pode mover facilmente esse recurso nos Blobs do Azure utilizando o `TransferManager.CopyAsync` método. Esse método também introduz um novo parâmetro booleano. Definir esse parâmetro como `true` indica que queremos fazer uma cópia assíncrona do lado do servidor. Definir esse parâmetro como `false` indica uma cópia síncrona – que significa que o recurso é primeiro transferido para o nosso computador local, em seguida, carregado para o Blob do Azure. No entanto, a cópia síncrona está atualmente disponível apenas para copiar a partir de um recurso de armazenamento do Azure para outra.

## <a name="transfer-azure-blob-to-azure-blob"></a>Transferir BLOBs do Azure para BLOBs do Azure
Outro recurso que é fornecido exclusivamente pela biblioteca de movimento de dados é a capacidade de copiar de um recurso de armazenamento do Azure para outra.

Modificar `TransferAzureBlobToAzureBlob`:

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

Neste exemplo, definimos o parâmetro booleano `TransferManager.CopyAsync` para `false` para indicar que queremos fazer uma cópia síncrona. Isso significa que o recurso é primeiro transferido para o nosso computador local, em seguida, carregado para o Blob do Azure. A opção de cópia síncrona é uma excelente forma de garantir que a operação de cópia tem uma velocidade consistente. Por outro lado, a velocidade de uma cópia assíncrona do lado do servidor é dependente da largura de banda de rede disponível no servidor, o que pode variar. No entanto, a cópia síncrona pode gerar custos de saída adicionais quando comparados com a cópia assíncrona. A abordagem recomendada é usar a cópia síncrona numa VM do Azure que está na mesma região que a sua conta de armazenamento de origem para evitar custos de saída.

## <a name="conclusion"></a>Conclusão
Agora nosso aplicativo de movimento de dados está a ser concluído. [O exemplo de código completo está disponível no GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Passos Seguintes
Esta introdução ao, criamos um aplicativo que interage com o armazenamento do Azure e é executado no Windows, Linux e macOS. Esta introdução concentra-se no armazenamento de Blobs. No entanto, esse mesmo conhecimento pode ser aplicado ao armazenamento de ficheiros. Para obter mais informações, confira [documentação de referência da biblioteca de movimento de dados de armazenamento do Azure](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
