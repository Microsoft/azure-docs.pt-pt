---
title: Ativar e gerir a eliminação suave para bolhas
titleSuffix: Azure Storage
description: Ative a eliminação suave de objetos blob para recuperar mais facilmente os seus dados quando estes forem erroneamente modificados ou eliminados.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 5d6cbf873ac1b76c24f5907a47038157b22e5680
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83634123"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>Ativar e gerir a eliminação suave para bolhas

A eliminação suave protege os dados blob de serem acidentalmente ou erroneamente modificados ou eliminados. Quando o soft delete está ativado para uma conta de armazenamento, bolhas, versões blob (pré-visualização) e instantâneos nessa conta de armazenamento podem ser recuperados após a sua eliminação, num período de retenção que especifica.

Se houver a possibilidade de os seus dados poderem ser acidentalmente modificados ou eliminados por uma aplicação ou outro utilizador de uma conta de armazenamento, a Microsoft recomenda que se vire a eliminar suavemente.

Este artigo mostra como começar com soft delete.

## <a name="enable-soft-delete"></a>Ativar a eliminação recuperável

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ativar a eliminação suave de bolhas na sua conta de armazenamento utilizando o portal Azure:

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento. 

2. Navegue para a opção **de Proteção** de Dados no **âmbito do Serviço Blob**.

3. Clique **ativado** sob **a eliminação suave** de Blob

4. Insira o número de dias que pretende *manter ao* abrigo das políticas de **retenção**

5. Escolha o botão **Guardar** para confirmar as definições de Proteção de Dados

![](media/soft-delete-enable/storage-blob-soft-delete-portal-configuration.png)

Para ver bolhas apagadas suaves, selecione a caixa de verificação de **blobs eliminada supérre.**

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Para ver imagens apagadas suaves para uma dada bolha, selecione a bolha e, em seguida, clique em **Ver snapshots**.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Certifique-se de que a caixa de verificação de **instantâneos eliminado seleções** do Show foi selecionada.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Quando clicar numa bolha ou instantâneo suavemente apagado, repare nas novas propriedades blob. Indicam quando o objeto foi apagado e quantos dias faltam até que a bolha ou a imagem blob estejam permanentemente expiradas. Se o objeto apagado macio não for instantâneo, também terá a opção de o desapagar.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-properties.png)

Lembre-se que não desabater uma bolha também irá desapagar todos os instantâneos associados. Para desapagar as imagens apagadas macias para uma bolha ativa, clique na bolha e **selecione Undelete todos os instantâneos**.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Uma vez que não apague as imagens de uma bolha, pode clicar em **Promover** para copiar uma imagem sobre a bolha da raiz, restaurando assim a bolha para o instantâneo.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para permitir a eliminação suave, atualize as propriedades de serviço de um cliente blob. O exemplo que se segue permite eliminar suavemente para um subconjunto de contas numa subscrição:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

Pode verificar se a eliminação suave foi ativada utilizando o seguinte comando:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Para recuperar bolhas que foram acidentalmente apagadas, ligue para undelete nessas bolhas. Lembre-se que chamar **Undelete Blob**, tanto em bolhas apagadas ativas como suaves, irá restaurar todos os instantâneos apagados associados como ativos. O exemplo seguinte chama Undelete em todas as bolhas macias apagadas e ativas num recipiente:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Para encontrar a atual política de retenção soft delete, utilize o seguinte comando:

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

# <a name="python"></a>[Pitão](#tab/python)

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

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Para permitir a eliminação suave, atualize as propriedades de serviço de um cliente blob:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

Para recuperar bolhas que foram acidentalmente apagadas, ligue para undelete nessas bolhas. Lembre-se que chamar **Undelete**, tanto em bolhas apagadas ativas como suaves, irá restaurar todas as imagens suaves associadas como ativas. O exemplo seguinte chama Undelete em todas as bolhas macias apagadas e ativas num recipiente:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Para recuperar para uma versão blob específica, primeiro ligue para Undelete em uma bolha e, em seguida, copie o instantâneo desejado sobre a bolha. O exemplo seguinte recupera uma bolha de bloco para o seu instantâneo mais recentemente gerado:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

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

Para recuperar bolhas que foram acidentalmente apagadas, ligue para undelete nessas bolhas. Lembre-se que chamar **Undelete**, tanto em bolhas apagadas ativas como suaves, irá restaurar todas as imagens suaves associadas como ativas. O exemplo seguinte chama Undelete em todas as bolhas macias apagadas e ativas num recipiente:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Para recuperar para uma versão blob específica, primeiro ligue para Undelete em uma bolha e, em seguida, copie o instantâneo desejado sobre a bolha. O exemplo seguinte recupera uma bolha de bloco para o seu instantâneo mais recentemente gerado:

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

- [Eliminação suave para armazenamento Blob](soft-delete-overview.md)
- [Versão blob (pré-visualização)](versioning-overview.md)