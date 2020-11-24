---
title: Criar e gerir um instantâneo blob em .NET
titleSuffix: Azure Storage
description: Aprenda a usar a biblioteca de clientes .NET para criar uma imagem apenas de leitura de uma bolha para fazer o back-up dados blob num dado no tempo.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d72c30fb7c45c92f24ec5132859a45c6ae953
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95543379"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Criar e gerir um instantâneo blob em .NET

Uma foto é uma versão só de leitura de uma bolha que é tirada num ponto do tempo. Este artigo mostra como criar e gerir instantâneos blob utilizando a biblioteca de [clientes Azure Storage para .NET](/dotnet/api/overview/azure/storage).

Para obter mais informações sobre as fotos blob no Azure Storage, consulte [as fotos blob](snapshots-overview.md).

## <a name="create-a-snapshot"></a>Criar um instantâneo

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para criar uma imagem instantânea de uma bolha de bloco utilizando a versão 12.x da biblioteca do cliente Azure Storage para .NET, utilize um dos seguintes métodos:

- [Criar Snapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [Criar SnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

O exemplo de código que se segue mostra como criar uma imagem instantânea com a versão 12.x. Inclua uma referência à biblioteca [Azure.Identity](https://www.nuget.org/packages/azure.identity) para utilizar as suas credenciais AZure AD para autorizar pedidos ao serviço.

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para criar uma imagem instantânea de uma bolha de bloco utilizando a versão 11.x da biblioteca do cliente Azure Storage para .NET, utilize um dos seguintes métodos:

- [Criar Snapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [Criar SnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

O exemplo de código que se segue mostra como criar uma imagem instantânea com a versão 11.x. Este exemplo especifica metadados adicionais para o instantâneo quando é criado.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

## <a name="delete-snapshots"></a>Eliminar instantâneos

Para eliminar uma bolha, deve primeiro eliminar quaisquer instantâneos dessa bolha. Pode eliminar uma imagem individualmente ou especificar que todas as imagens são eliminadas quando a bolha de origem é eliminada. Se tentar apagar uma bolha que ainda tenha instantâneos, resulta um erro.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para eliminar uma bolha e as suas imagens utilizando a versão 12.x da biblioteca do cliente do Azure Storage para .NET, utilize um dos seguintes métodos e inclua o [DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) enum:

- [Eliminar](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

O exemplo de código que se segue mostra como apagar uma bolha e as suas imagens em .NET, onde `blobClient` está um objeto do tipo [BlobClient):](/dotnet/api/azure.storage.blobs.blobclient)

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para eliminar uma bolha e as suas imagens utilizando a versão 11.x da biblioteca do cliente do Azure Storage para .NET, utilize um dos seguintes métodos de eliminação de bolhas e inclua o [enum DeleteSnapshotsOption:](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption)

- [Eliminar](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

O exemplo de código que se segue mostra como eliminar uma bolha e as suas imagens em .NET, onde `blockBlob` está um objeto do tipo [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>Passos seguintes

- [Snapshots blob](snapshots-overview.md)
- [Versões blob](versioning-overview.md)
- [Eliminação recuperável para blobs](./soft-delete-blob-overview.md)