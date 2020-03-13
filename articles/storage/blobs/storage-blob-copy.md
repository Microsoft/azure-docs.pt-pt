---
title: Copiar uma bolha com .NET - Armazenamento Azure
description: Aprenda a copiar uma bolha na sua conta de Armazenamento Azure utilizando a biblioteca de clientes .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135892"
---
# <a name="copy-a-blob-with-net"></a>Copiar uma bolha com .NET

Este artigo demonstra como copiar uma bolha com uma conta de Armazenamento Azure. Também mostra como abortar uma operação de cópia assíncrona. O código de exemplo utiliza a [biblioteca de clientes Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-copying-blobs"></a>Sobre copiar bolhas

Quando se copia uma bolha na mesma conta de armazenamento, trata-se de uma operação sincronizada. Quando se copia através de contas é uma operação assíncrona. Os métodos [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) e [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) devolvem um valor de ID de cópia que é usado para verificar o estado ou abortar o funcionamento da cópia.

A bolha de origem para uma operação de cópia pode ser uma bolha de bloco, uma bolha de apêndice, uma bolha de página ou um instantâneo. Se a bolha de destino já existir, deve ser do mesmo tipo de bolha que a bolha de origem. Qualquer bolha de destino existente será substituída. 

A bolha de destino não pode ser modificada enquanto uma operação de cópia está em andamento. Uma bolha de destino só pode ter uma excelente operação de blob cópia. Por outras palavras, uma bolha não pode ser o destino para várias operações de cópia pendentes.

Toda a bolha de origem ou ficheiro é sempre copiado. Não é suportado copiar uma gama de bytes ou blocos.

Quando uma bolha é copiada, as suas propriedades do sistema são copiadas para a bolha de destino com os mesmos valores.

Para todos os tipos de blob, pode consultar a propriedade [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) na bolha de destino para obter o estado da operação de cópia. A bolha final será cometida quando a cópia estiver concluída.

Uma operação de cópia pode assumir qualquer um dos seguintes formulários:

  - Você pode copiar uma bolha de origem para uma bolha de destino com um nome diferente. A bolha de destino pode ser uma bolha existente do mesmo tipo de bolha (bloco, apêndice ou página), ou pode ser uma nova bolha criada pela operação de cópia.
  - Pode copiar uma bolha de origem para uma bolha de destino com o mesmo nome, substituindo efetivamente a bolha de destino. Tal operação de cópia remove quaisquer blocos não comprometidos e substitui os metadados da bolha de destino.
  - Pode copiar um ficheiro fonte no serviço De Ficheiros Azure para uma bolha de destino. A bolha de destino pode ser uma bolha de bloco existente, ou pode ser uma nova bolha de bloco criada pela operação de cópia. Não é suportada a cópia de ficheiros para bolhas de página ou bolhas de apêndice.
  - Pode copiar uma foto sobre a sua bolha base. Ao promover um instantâneo para a posição da bolha base, pode restaurar uma versão anterior de uma bolha.
  - Pode copiar uma foto para uma bolha de destino com um nome diferente. A bolha de destino resultante é uma bolha reempresível e não um instantâneo.

## <a name="copy-a-blob"></a>Copiar uma bolha

Para copiar uma bolha, ligue para um dos seguintes métodos:

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

O seguinte exemplo de código obtém uma referência a uma bolha criada anteriormente e copia-a para uma nova bolha no mesmo recipiente:

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

## <a name="abort-a-blob-copy-operation"></a>Abortar uma operação de cópia blob

Abortar uma operação de cópia resulta numa bolha de destino de comprimento zero para bolhas de bloco, bolhas de apêndice e bolhas de página. No entanto, os metadados para a bolha de destino terão os novos valores copiados da bolha de origem ou definidos explicitamente na chamada [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) ou [StartCopyAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) Para manter os metadados originais antes da cópia, faça uma foto da bolha de destino antes de ligar para `StartCopy` ou `StartCopyAsync`.

Quando aborta uma operação de cópia blob em curso, o estado de cópia do destino blob [é](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) definido para [CopyStatus.Abortado](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

Os métodos [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) e [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) cancelam uma operação de cópia blob em curso e deixam uma bolha de destino com zero comprimento e metadados completos.

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

Os seguintes tópicos contêm informações sobre a cópia de bolhas e a abortação das operações de cópia em curso utilizando as APIs do Azure REST.

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Abortar copy blob](/rest/api/storageservices/abort-copy-blob)
