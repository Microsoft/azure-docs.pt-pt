---
title: Ative e gere a eliminação suave para bolhas
titleSuffix: Azure Storage
description: Ativar a eliminação suave das bolhas para recuperar mais facilmente os seus dados quando estes são erroneamente modificados ou eliminados.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 07e306c6c428a0e3a3f64a29a2574cb0378bb81f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230233"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>Ative e gere a eliminação suave para bolhas

A blob soft delete protege os seus dados de serem acidental ou erroneamente modificados ou eliminados. Quando a eliminação suave de blob estiver ativada para uma conta de armazenamento, as versões blob, blob e instantâneos nessa conta de armazenamento podem ser recuperadas após a sua eliminação, num período de retenção que especifique.

Se houver a possibilidade de os seus dados poderem ser acidentalmente modificados ou eliminados por uma aplicação ou outro utilizador de uma conta de armazenamento, a Microsoft recomenda que se inscreva com uma eliminação macia. Este artigo mostra como permitir a eliminação suave para bolhas. Para obter mais detalhes sobre a eliminação suave da bolha, consulte [Soft Delete para bolhas](soft-delete-blob-overview.md).

Para aprender a permitir a eliminação suave para recipientes, consulte [Ativar e gerir a eliminação suave para recipientes](soft-delete-container-enable.md).

## <a name="enable-blob-soft-delete"></a>Ativar a eliminação macia da bolha

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ativar a eliminação suave para bolhas na sua conta de armazenamento utilizando o portal Azure:

1. No [portal do Azure](https://portal.azure.com/), navegue para a sua conta de armazenamento.
1. Localizar a opção **de Proteção de Dados** no **serviço Blob**.
1. Desace a propriedade **Blob soft Delete** para *Ativação*.
1. De acordo com **as políticas de retenção**, especifique quanto tempo as bolhas apagadas são mantidas pelo Azure Storage.
1. Guarde as alterações.

![Screenshot do Portal Azure com o serviço de proteção de dados eleito.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

Para visualizar bolhas apagadas suaves, selecione a caixa de verificação **de blobs apagadas do Show.**

![Screenshot da página de serviço de blob de proteção de dados com a opção de blobs apagadas do Show realçada.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Para visualizar instantâneos apagados suaves para uma determinada bolha, selecione a bolha e, em seguida, clique em **Ver instantâneos**.

![Screenshot da página de serviço de blob de proteção de dados com a opção Ver instantâneos realçada.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Certifique-se de que a caixa de verificação **de instantâneos apagadas do Show** está selecionada.

![Screenshot da página 'Ver instantâneos' com a opção de blobs apagadas do Show realçada.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Quando clicar numa bolha ou instantâneo apagado suave, note as novas propriedades blob. Indicam quando o objeto foi apagado e quantos dias faltam até que a bolha ou o instantâneo blob expirem permanentemente. Se o objeto apagado suave não for um instantâneo, também terá a opção de desafiá-lo.

![Screenshot dos detalhes de um objeto suave apagado.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-properties.png)

Lembre-se que desaderar uma bolha também irá desemarrá todos os instantâneos associados. Para desembolsar instantâneos suaves apagados para uma bolha ativa, clique na bolha e selecione **Undelete todos os instantâneos**.

![Screenshot dos detalhes de uma bolha suave apagada.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Assim que desempacoar as fotos de uma bolha, pode clicar em **Promover** para copiar uma imagem sobre a bolha de raiz, restaurando assim a bolha para o instantâneo.

![Screenshot da página 'Ver instantâneos' com a opção 'Promover' em destaque.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para ativar a eliminação suave, atualize as propriedades de serviço de um cliente blob. O exemplo a seguir permite a eliminação suave de um subconjunto de contas numa subscrição:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

Pode verificar se a eliminação suave foi ligada utilizando o seguinte comando:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Para recuperar as bolhas que foram acidentalmente apagadas, ligue para **Undelete Blob** naquelas bolhas. Lembre-se que chamar **Undelete Blob**, tanto em bolhas apagadas ativas como suaves, irá restaurar todos os instantâneos suaves apagados associados como ativos. O exemplo a seguir chama **Undelete Blob** em todas as bolhas macias apagadas e ativas num recipiente:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Para encontrar a atual política de retenção de eliminação suave, utilize o seguinte comando:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Para permitir a eliminação suave, atualize as propriedades de serviço de um cliente blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Para verificar se a eliminação suave está ligada, utilize o seguinte comando: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Para permitir a eliminação suave, atualize as propriedades de serviço de um cliente blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para permitir a eliminação suave, atualize as propriedades de serviço de um cliente blob:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

Para recuperar as bolhas que foram acidentalmente apagadas, ligue para Undelete naquelas bolhas. Lembre-se que chamar **Undelete**, tanto em bolhas apagadas ativas como suaves, irá restaurar todos os instantâneos suaves e suaves associados como ativos. O exemplo a seguir chama Undelete em todas as bolhas macias apagadas e ativas num recipiente:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Para recuperar para uma versão blob específica, primeiro ligue para Undelete numa bolha e, em seguida, copie o instantâneo desejado sobre a bolha. O exemplo a seguir recupera uma bolha de bloco para o seu instantâneo mais recentemente gerado:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para permitir a eliminação suave, atualize as propriedades de serviço de um cliente blob:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Para recuperar as bolhas que foram acidentalmente apagadas, ligue para **Undelete Blob** naquelas bolhas. Lembre-se que chamar **Undelete Blob**, tanto em bolhas apagadas ativas como suaves, irá restaurar todos os instantâneos suaves apagados associados como ativos. O exemplo a seguir chama **Undelete Blob** em todas as bolhas macias e ativas num recipiente:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Para recuperar para uma versão blob específica, primeiro ligue para a operação **Undelete Blob** e, em seguida, copie o instantâneo desejado sobre a bolha. O exemplo a seguir recupera uma bolha de bloco para o seu instantâneo mais recentemente gerado:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

## <a name="next-steps"></a>Passos seguintes

- [Excluir suave para armazenamento Blob](soft-delete-overview.md)
- [Versão blob](versioning-overview.md)
