---
title: Copie uma bolha com APIs de armazenamento Azure
description: Saiba como copiar uma bolha utilizando as bibliotecas de clientes do Azure Storage.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/10/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 2c474ed4d4158356075f861c3c0d5ace69173255
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90014655"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>Copie uma bolha com bibliotecas de clientes do Azure Storage

Este artigo demonstra como copiar uma bolha numa conta de Armazenamento Azure. Também mostra como abortar uma operação de cópia assíncronea. O código de exemplo utiliza as bibliotecas de clientes Azure Storage.

## <a name="about-copying-blobs"></a>Sobre copiar bolhas

Quando se copia uma bolha na mesma conta de armazenamento, é uma operação sincronizada. Quando se copia as contas é uma operação assíncronea.

A bolha de origem para uma operação de cópia pode ser uma bolha de bloco, uma bolha de apêndice, uma bolha de página ou uma foto instantânea. Se a bolha de destino já existir, deve ser do mesmo tipo de bolha que a bolha de origem. Uma bolha de destino existente será substituída.

A bolha de destino não pode ser modificada enquanto uma operação de cópia estiver em andamento. Uma bolha de destino só pode ter uma operação de cópia pendente. Por outras palavras, uma bolha não pode ser o destino para várias operações pendentes de cópia.

Toda a bolha de origem ou ficheiro é sempre copiado. A cópia de uma gama de bytes ou conjunto de blocos não é suportada.

Quando uma bolha é copiada, as suas propriedades do sistema são copiadas para a bolha de destino com os mesmos valores.

Uma operação de cópia pode assumir qualquer um dos seguintes formulários:

- Copie uma bolha de origem para uma bolha de destino com um nome diferente. A bolha de destino pode ser uma bolha existente do mesmo tipo blob (bloco, apêndice ou página), ou pode ser uma nova bolha criada pela operação de cópia.
- Copie uma bolha de origem para uma bolha de destino com o mesmo nome, substituindo efetivamente a bolha de destino. Tal operação de cópia remove quaisquer blocos não comprometidos e substitui os metadados da blob de destino.
- Copie um ficheiro de origem no serviço Azure File para uma bolha de destino. A bolha de destino pode ser uma bolha de bloco existente, ou pode ser uma nova bolha de bloco criada pela operação de cópia. A cópia de ficheiros para bolhas de página ou bolhas de apêndice não é suportada.
- Copie uma foto sobre a sua bolha de base. Ao promover um instantâneo para a posição da bolha de base, pode restaurar uma versão anterior de uma bolha.
- Copie uma foto para uma bolha de destino com um nome diferente. A bolha de destino resultante é uma bolha de escrever e não um instantâneo.

## <a name="copy-a-blob"></a>Copiar uma bolha

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para copiar uma bolha, ligue para um dos seguintes métodos:

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

Os `StartCopyFromUri` `StartCopyFromUriAsync` métodos devolvem um objeto [CopyFromUriOperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation) contendo informações sobre a operação da cópia.

O seguinte exemplo de código obtém um [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) representando uma bolha previamente criada e copia-o para uma nova bolha no mesmo recipiente:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para copiar uma bolha, ligue para um dos seguintes métodos:

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

Os `StartCopy` `StartCopyAsync` métodos devolvem um valor de ID de cópia que é usado para verificar o estado ou abortar a operação de cópia.

O exemplo de código a seguir obtém uma referência a uma bolha previamente criada e copia-a a uma nova bolha no mesmo recipiente:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

# <a name="python-v12"></a>[Python v12](#tab/python)

Para copiar uma bolha, ligue para o método [start_copy_from_url.](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-) O `start_copy_from_url` método devolve um dicionário contendo informações sobre a operação da cópia.

O seguinte exemplo de código obtém um [BlobClient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient) representando uma bolha previamente criada e copia-o para uma nova bolha no mesmo recipiente:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>Abortar uma operação de cópia

Abortar uma operação de cópia resulta numa bolha de destino de comprimento zero. No entanto, os metadados para a bolha de destino terão os novos valores copiados da bolha de origem ou definidos explicitamente durante a operação de cópia. Para manter os metadados originais antes da cópia, faça uma foto da bolha de destino antes de ligar para um dos métodos de cópia.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Verifique a propriedade [BlobProperties.CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus) na bolha de destino para obter o estado da operação de cópia. A bolha final será cometida quando a cópia estiver completa.

Quando aborta uma operação de cópia, o estado de cópia do destino blob está definido para [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus).

Os [métodos AbortCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri) e [AbortCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync) cancelam uma operação de cópia em curso.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Consulte a propriedade [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status) na bolha de destino para obter o estado da operação de cópia. A bolha final será cometida quando a cópia estiver completa.

Quando aborta uma operação de cópia, o estado de cópia do destino blob está definido para [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus).

Os [métodos AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy) e [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync) cancelam uma operação de cópia em curso.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

# <a name="python-v12"></a>[Python v12](#tab/python)

Verifique a entrada de "status" no dicionário [CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) devolvido [por get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-) método para obter o estado da operação de cópia. A bolha final será cometida quando a cópia estiver completa.

Quando aborta uma operação de cópia, o [estado](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) está definido para "abortado".

O método [abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-) cancela uma operação de cópia em curso.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>SDKs do Azure

Obtenha mais informações sobre Azure SDKs:

 - [SDK do Azure para .NET](https://github.com/azure/azure-sdk-for-net)
 - [SDK do Azure para Java](https://github.com/azure/azure-sdk-for-java)
 - [SDK do Azure para Python](https://github.com/azure/azure-sdk-for-python)
 - [SDK do Azure para JavaScript](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>Passos seguintes

Os seguintes tópicos contêm informações sobre a cópia de blobs e o aborto das operações de cópia em curso utilizando as APIs do Azure REST.

- [Bolha de cópia](/rest/api/storageservices/copy-blob)
- [Abortar Bolha de Cópia](/rest/api/storageservices/abort-copy-blob)
