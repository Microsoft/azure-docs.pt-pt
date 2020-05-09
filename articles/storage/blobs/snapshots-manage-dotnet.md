---
title: Crie e gerencie um instantâneo blob em .NET
titleSuffix: Azure Storage
description: Aprenda a criar uma foto apenas de uma bolha para fazer o back-up dos dados blob num dado momento no tempo.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 438692d5c142c3f617ee9d0c3f55b9b3740f9b7a
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884699"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Crie e gerencie um instantâneo blob em .NET

Um instantâneo é uma versão só de leitura de uma bolha que é tirada em um momento de tempo. Este artigo mostra como criar e gerir imagens blob usando a [biblioteca de clientes Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

Para obter mais informações sobre imagens blob no Armazenamento Azure, consulte [Criar e gerir uma imagem de blob em .NET](snapshots-overview.md).

## <a name="create-a-snapshot"></a>Criar um instantâneo

# <a name="net-version-12x"></a>[.NET versão 12.x](#tab/v12)

Para criar uma imagem instantânea de uma bolha de bloco utilizando a versão 12.x da biblioteca de clientes do Azure Storage para .NET, utilize um dos seguintes métodos:

- [Criar Snapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

O exemplo de código que se segue mostra como criar um instantâneo com a versão 12.x. Inclua uma referência à biblioteca [Azure.Identity](https://www.nuget.org/packages/azure.identity) para usar as suas credenciais Azure AD para autorizar pedidos ao serviço.

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

# <a name="net-version-11x"></a>[.NET versão 11.x](#tab/v11)

Para criar uma imagem instantânea de uma bolha de bloco utilizando a versão 11.x da biblioteca de clientes do Azure Storage para .NET, utilize um dos seguintes métodos:

- [Criar Snapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

O exemplo de código que se segue mostra como criar um instantâneo com a versão 11.x. Este exemplo especifica metadados adicionais para o instantâneo quando é criado.

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

Para eliminar uma bolha, primeiro deve eliminar quaisquer imagens dessa bolha. Pode eliminar um instantâneo individualmente ou especificar que todas as imagens sejam eliminadas quando a bolha de origem for eliminada. Se tentar apagar uma bolha que ainda tem instantâneos, resulta um erro.

# <a name="net-version-12x"></a>[.NET versão 12.x](#tab/v12)

Para eliminar uma bolha e as suas imagens utilizando a versão 12.x da biblioteca de clientes Do Armazenamento Azure para .NET, utilize um dos seguintes métodos e inclua o [DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) enum:

- [Eliminar](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [Eliminarifexista](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [Eliminar IfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

O exemplo de código que se segue mostra como eliminar `blobClient` uma bolha e as suas imagens em .NET, onde está um objeto do tipo [BlobClient):](/dotnet/api/azure.storage.blobs.blobclient)

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-version-11x"></a>[.NET versão 11.x](#tab/v11)

Para eliminar uma bolha e as suas imagens utilizando a versão 11.x da biblioteca de clientes do Azure Storage para .NET, utilize um dos seguintes métodos de eliminação blob e inclua o [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) enum:

- [Eliminar](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [Eliminarifexista](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [Eliminar IfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

O exemplo de código que se segue mostra como eliminar `blockBlob` uma bolha e as suas imagens em .NET, onde está um objeto de tipo [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>Passos seguintes

- [Fotos blob](snapshots-overview.md)
- [Versões blob (pré-visualização)](versioning-overview.md)
- [Eliminação suave para bolhas](storage-blob-soft-delete.md)