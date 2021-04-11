---
title: Gerir e restaurar bolhas apagadas suaves
titleSuffix: Azure Storage
description: Gerir e restaurar bolhas e instantâneos com o portal Azure ou com as bibliotecas de clientes do Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d5ef85d947ae999fd94ba5a6e9cdb00baec9786
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556217"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>Gerir e restaurar bolhas apagadas suaves

A blob soft delete protege uma bolha individual e as suas versões, instantâneos e metadados de eliminações ou substituições acidentais mantendo os dados eliminados no sistema por um período de tempo especificado. Durante o período de retenção, pode restaurar a bolha no seu estado de supressão. Após o período de retenção ter expirado, a bolha é permanentemente eliminada. Para obter mais informações sobre a eliminação suave da bolha, consulte [Soft Delete para bolhas](soft-delete-blob-overview.md).

A eliminação suave blob faz parte de uma estratégia abrangente de proteção de dados para dados blob. Para saber mais sobre as recomendações da Microsoft para a proteção de dados, consulte a [visão geral da proteção de dados](data-protection-overview.md).

## <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Gerir bolhas com o portal Azure

Pode utilizar o portal Azure para visualizar e restaurar bolhas e instantâneos apagados.

### <a name="view-deleted-blobs"></a>Ver bolhas apagadas

Quando as bolhas são apagadas suavemente, são invisíveis no portal Azure por defeito. Para visualizar bolhas apagadas suaves, navegue na página **'Vista Geral'** para o recipiente e altere a definição de **bolhas apagadas** do Show. As bolhas apagadas com o tipo de **manchas apagadas** são apresentadas com um estatuto de Deleted .

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Screenshot mostrando como listar bolhas apagadas no portal Azure":::

Em seguida, selecione a bolha eliminada da lista de bolhas para exibir as suas propriedades. No **separador Visão Geral,** note que o estado da bolha está definido para **Deleted**. O portal também apresenta o número de dias até que a bolha seja permanentemente apagada.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Screenshot mostrando propriedades de blob apagado suave no portal Azure":::

### <a name="view-deleted-snapshots"></a>Ver instantâneos apagados

A eliminação de uma bolha também elimina quaisquer instantâneos associados à bolha. Se uma bolha depagou-se suavemente tiver instantâneos, as imagens apagadas também podem ser exibidas no portal. Exiba as propriedades da bolha apagada suave, em seguida, navegue para o **separador Snapshots** e toggle **Mostrar instantâneos apagados**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="Exibição de screenshot ":::

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Restaurar objetos apagados suaves quando a versão é desativada

Para restaurar uma bolha apagada no portal Azure quando a versão blob não estiver ativada, primeiro apresente as propriedades da bolha e, em seguida, selecione o botão **Undelete** no **separador Vista Geral.** Restaurar uma bolha também restaura quaisquer instantâneos que foram eliminados durante o período de retenção de eliminação suave.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Screenshot mostrando como restaurar uma bolha apagada no portal Azure":::

Para promover uma imagem depagou suavemente a bolha de base, certifique-se primeiro de que as imagens apagadas da bolha foram restauradas. Selecione o botão **Undelete** para restaurar as imagens apagadas suaves da bolha, mesmo que a bolha de base em si não tenha sido apagada suavemente. Em seguida, selecione o instantâneo para promover e use o botão **Desembaraçar** o instantâneo para substituir a bolha de base com o conteúdo do instantâneo.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="Screenshot mostrando como promover um instantâneo para a bolha base":::

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Restaurar as bolhas apagadas suaves quando a versão estiver ativada

Para restaurar uma bolha depagou-se suavemente no portal Azure quando a versão estiver ativada, selecione a bolha depagou-se suavemente para exibir as suas propriedades e, em seguida, selecione o separador **Versões.** Selecione a versão que pretende promover para ser a versão atual e, em seguida, selecione **Fazer a versão atual**.  

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Screenshot mostrando como promover uma versão para restaurar uma bolha no portal Azure":::

Para restaurar versões ou instantâneos apagados quando a versão estiver ativada, apresente as propriedades da bolha e, em seguida, selecione o botão **Undelete** no **separador Vista Geral.**

> [!NOTE]
> Quando a versão está ativada, selecionar o botão **Undelete** numa bolha apagada restaura quaisquer versões ou instantâneos apagados suaves, mas não restaura a bolha de base. Para restaurar a bolha de base, deve promover uma versão anterior.

## <a name="manage-soft-deleted-blobs-with-code"></a>Gerir bolhas com o código

Pode utilizar as bibliotecas do cliente do Azure Storage para restaurar uma bolha ou instantâneo apagado. Os exemplos a seguir mostram como utilizar a biblioteca de clientes .NET.

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Restaurar objetos apagados suaves quando a versão é desativada

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para restaurar as bolhas apagadas quando a versão não estiver ativada, ligue para a operação [Undelete Blob](/rest/api/storageservices/undelete-blob) nessas bolhas. A operação **Undelete Blob** restaura bolhas apagadas e quaisquer instantâneos apagados associados a essas bolhas.

Chamar **Undelete Blob** numa bolha que não foi apagada não tem efeito. O exemplo a seguir chama **Undelete Blob** em todas as bolhas num recipiente, e restaura as bolhas apagadas suaves e seus instantâneos:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Para restaurar uma versão específica, ligue primeiro para a operação **Undelete Blob** na bolha ou versão base e, em seguida, copie a versão desejada sobre a bolha base. O exemplo a seguir restaura uma bolha de bloco para a versão mais recentemente guardada:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para restaurar as bolhas apagadas quando a versão não estiver ativada, ligue para a operação [Undelete Blob](/rest/api/storageservices/undelete-blob) nessas bolhas. A operação **Undelete Blob** restaura bolhas apagadas e quaisquer instantâneos apagados associados a essas bolhas.

Chamar **Undelete Blob** numa bolha que não foi apagada não tem efeito. O exemplo a seguir chama **Undelete Blob** em todas as bolhas num recipiente, e restaura as bolhas apagadas suaves e seus instantâneos:

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Para restaurar uma imagem específica, ligue primeiro para a operação **Undelete Blob** na bolha de base e, em seguida, copie o instantâneo desejado sobre a bolha de base. O exemplo a seguir restaura uma bolha de bloco ao seu instantâneo mais recentemente gerado:

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Restaurar as bolhas apagadas suaves quando a versão estiver ativada

Para restaurar uma bolha depagou-se suavemente quando a versão estiver ativada, copie uma versão anterior sobre a bolha de base com uma [bolha de cópia](/rest/api/storageservices/copy-blob) ou bolha de cópia da operação [URL.](/rest/api/storageservices/copy-blob-from-url)  

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Não aplicável. A versão blob é suportada apenas na versão 12.x e superior das bibliotecas do Azure Storage.

---

## <a name="next-steps"></a>Passos seguintes

- [Excluir suave para armazenamento Blob](./soft-delete-blob-overview.md)
- [Ativar a eliminação recuperável para blobs](soft-delete-blob-enable.md)
- [Versão blob](versioning-overview.md)
