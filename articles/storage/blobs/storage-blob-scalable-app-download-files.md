---
title: Transferir grandes quantidades de dados aleatórios do Armazenamento do Azure | Microsoft Docs
description: Saiba como utilizar o Azure SDK para carregar grandes quantidades de dados aleatórios de uma conta de Armazenamento do Azure
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 8d270485cef9fb6859de056bc364a36c054c0121
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699021"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Transferir grandes quantidades de dados aleatórios do armazenamento do Azure

Este tutorial é a terceira parte de uma série. Este tutorial mostra como transferir grandes quantidades de dados do armazenamento do Azure.

Na terceira parte da série, ficará a saber como:

> [!div class="checklist"]
> * Atualizar a aplicação
> * Executar a aplicação
> * Validar o número de ligações

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você deve ter concluído o tutorial de armazenamento anterior: [Carregar grandes quantidades de dados aleatórios em paralelo no armazenamento do Azure][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Aceder remotamente à máquina virtual

 Utilize o seguinte comando na máquina virtual para criar uma sessão de ambiente de trabalho remoto com a máquina virtual. Substitua o endereço IP pelo publicIPAddress da máquina virtual. Quando lhe for pedido, introduza as credenciais utilizadas ao criar a máquina virtual.

```
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Atualizar a aplicação

No tutorial anterior, apenas carregou ficheiros para a conta de armazenamento. Abra `D:\git\storage-dotnet-perf-scale-app\Program.cs` num editor de texto. Substitua o método `Main` pelo exemplo seguinte. Este exemplo comenta a tarefa de carregamento e anula os comentários da tarefa de transferência e da tarefa para eliminar o conteúdo na conta de armazenamento, quando terminar.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        UploadFilesAsync().GetAwaiter().GetResult();

        // Uncomment the following line to enable downloading of files from the storage account.  This is commented out
        // initially to support the tutorial at https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files.
        // DownloadFilesAsync().GetAwaiter().GetResult();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.  This is commented out initially
        // As the tutorial at https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files has you upload only for one tutorial and download for the other. 
        if (!exception)
        {
            // DeleteExistingContainersAsync().GetAwaiter().GetResult();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Depois de a aplicação ser atualizada, terá de criar a aplicação novamente. Abra uma `Command Prompt` e navegue até `D:\git\storage-dotnet-perf-scale-app`. Reconstrua a aplicação ao executar `dotnet build`, como mostrado no exemplo seguinte:

```
dotnet build
```

## <a name="run-the-application"></a>Executar a aplicação

Agora que a aplicação foi reconstruída, está na altura de executar a aplicação com o código atualizado. Se ainda não estiver aberta, abra uma `Command Prompt` e navegue até `D:\git\storage-dotnet-perf-scale-app`.

Escreva `dotnet run` para executar a aplicação.

```
dotnet run
```

A aplicação lê os contentores localizados na conta de armazenamento especificada em **storageconnectionstring**. Itera através dos blobs, 10 de cada vez, com o método [ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) nos contentores e transfere-os para o computador local através do método [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync).
A tabela seguinte mostra as [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) que estão definidas para cada blob à medida que é transferido.

|Propriedade|Valor|Descrição|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Esta propriedade desativa a verificação do hash MD5 do conteúdo carregado. Desativar a validação MD5 permite uma transferência mais rápida. Mas não confirma a validade nem a integridade dos ficheiros que estão a ser transferidos. |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| Esta propriedade determina se um hash MD5 é calculado e armazenado.   |

A tarefa `DownloadFilesAsync` é mostrada no exemplo seguinte:

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.  Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container lazily 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

### <a name="validate-the-connections"></a>Validar as ligações

Enquanto os ficheiros estão a ser transferidos, pode verificar o número de ligações em simultâneo para a sua conta de armazenamento. Abra uma `Command Prompt` e escreva `netstat -a | find /c "blob:https"`. Este comando apresenta o número de ligações que estão atualmente abertas com `netstat`. O exemplo seguinte mostra um resultado semelhante ao que vê quando executa o tutorial. Como pode ver no exemplo, foram abertas mais de 280 ligações ao transferir os ficheiros aleatórios da conta de armazenamento.

```
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Passos Seguintes

Na terceira parte da série, aprendeu a transferir grandes quantidades de dados aleatórios para uma conta de armazenamento, como:

> [!div class="checklist"]
> * Executar a aplicação
> * Validar o número de ligações

Avance para a quarta parte da série para verificar as métricas de débito e latência no portal.

> [!div class="nextstepaction"]
> [Verificar as métricas de débito e latência no portal](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
