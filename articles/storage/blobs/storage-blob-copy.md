---
title: Copiar um blob com o .NET – armazenamento do Azure
description: Saiba como copiar um blob em sua conta de armazenamento do Azure usando a biblioteca de cliente .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9b3dba0041b38d9d59a10eaf80592bab91f65b98
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600283"
---
# <a name="copy-a-blob-with-net"></a>Copiar um blob com o .NET

Este artigo demonstra como copiar um blob com uma conta de armazenamento do Azure. Ele também mostra como anular uma operação de cópia assíncrona. O código de exemplo usa a [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage/client).

## <a name="about-copying-blobs"></a>Sobre a cópia de BLOBs

Quando você copia um blob dentro da mesma conta de armazenamento, é uma operação síncrona. Quando você copia entre contas, é uma operação assíncrona. Os métodos [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) e [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) retornam um valor de ID de cópia que é usado para verificar o status ou anular a operação de cópia.

O blob de origem para uma operação de cópia pode ser um blob de blocos, um blob de acréscimo, um blob de páginas ou um instantâneo. Se o blob de destino já existir, ele deverá ser do mesmo tipo de BLOB que o blob de origem. Qualquer blob de destino existente será substituído. 

O blob de destino não pode ser modificado enquanto uma operação de cópia está em andamento. Um blob de destino só pode ter uma operação pendente de blob de cópia. Em outras palavras, um BLOB não pode ser o destino para várias operações de cópia pendentes.

Todo o blob de origem ou arquivo é sempre copiado. Não há suporte para a cópia de um intervalo de bytes ou um conjunto de blocos.

Quando um blob é copiado, as propriedades do sistema são copiadas para o blob de destino com os mesmos valores.

Para todos os tipos de BLOB, você pode verificar a propriedade [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) no blob de destino para obter o status da operação de cópia. O blob final será confirmado quando a cópia for concluída.

Uma operação de cópia pode ter qualquer uma das seguintes formas:

  - Você pode copiar um blob de origem para um blob de destino com um nome diferente. O blob de destino pode ser um blob existente do mesmo tipo de BLOB (bloco, acrescentar ou página) ou pode ser um novo BLOB criado pela operação de cópia.
  - Você pode copiar um blob de origem para um blob de destino com o mesmo nome, substituindo efetivamente o blob de destino. Essa operação de cópia remove todos os blocos não confirmados e substitui os metadados do blob de destino.
  - Você pode copiar um arquivo de origem no serviço de arquivo do Azure para um blob de destino. O blob de destino pode ser um blob de blocos existente ou pode ser um novo BLOB de blocos criado pela operação de cópia. Não há suporte para a cópia de arquivos para BLOBs de páginas ou BLOBs de acréscimo.
  - Você pode copiar um instantâneo sobre seu blob de base. Ao promover um instantâneo para a posição do blob de base, você pode restaurar uma versão anterior de um blob.
  - Você pode copiar um instantâneo para um blob de destino com um nome diferente. O blob de destino resultante é um blob gravável e não um instantâneo.

## <a name="copy-a-blob"></a>Copiar um blob

Para copiar um blob, chame um dos seguintes métodos:

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

O exemplo de código a seguir obtém uma referência a um blob criado anteriormente e o copia para um novo BLOB no mesmo contêiner:

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

        // Lease the source blob for the copy operation to prevent another client from modifying it.
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
            Console.WriteLine();
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

## <a name="abort-a-blob-copy-operation"></a>Anular uma operação de cópia de BLOB

Anular uma operação de cópia resulta em um blob de destino de comprimento zero para BLOBs de blocos, blobs de acréscimo e blobs de páginas. No entanto, os metadados para o blob de destino terão os novos valores copiados do blob de origem ou definidos explicitamente na chamada [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) ou [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) . Para manter os metadados originais antes da cópia, crie um instantâneo do blob de destino antes de chamar `StartCopy` ou `StartCopyAsync`.

Quando você anula uma operação de cópia de BLOB em andamento, o [CopyState](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) do blob de destino é definido como [CopyStatus. Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

Os métodos [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) e [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) cancelam uma operação de cópia de BLOB em andamento e deixam um blob de destino com comprimento zero e metadados completos.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passos seguintes

Os tópicos a seguir contêm informações sobre como copiar BLOBs e anular operações de cópia em andamento usando as APIs REST do Azure.

- [Copiar blob](/rest/api/storageservices/copy-blob)
- [Anular cópia de BLOB](/rest/api/storageservices/abort-copy-blob)
