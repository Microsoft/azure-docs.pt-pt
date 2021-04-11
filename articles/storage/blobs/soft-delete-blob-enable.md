---
title: Ativar a eliminação recuperável para blobs
titleSuffix: Azure Storage
description: Ativar a eliminação suave das bolhas para proteger os dados do blob de eliminações ou substituições acidentais.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 11323f2aec05935b9dc45187ed54597e61af924d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554127"
---
# <a name="enable-soft-delete-for-blobs"></a>Ativar a eliminação recuperável para blobs

A blob soft delete protege uma bolha individual e as suas versões, instantâneos e metadados de eliminações ou substituições acidentais mantendo os dados eliminados no sistema por um período de tempo especificado. Durante o período de retenção, pode restaurar a bolha no seu estado de supressão. Após o período de retenção ter expirado, a bolha é permanentemente eliminada. Para obter mais informações sobre a eliminação suave da bolha, consulte [Soft Delete para bolhas](soft-delete-blob-overview.md).

A eliminação suave blob faz parte de uma estratégia abrangente de proteção de dados para dados blob. Para saber mais sobre as recomendações da Microsoft para a proteção de dados, consulte a [visão geral da proteção de dados](data-protection-overview.md).

## <a name="enable-blob-soft-delete"></a>Ativar a eliminação macia da bolha

A eliminação macia blob é desativada por padrão para uma nova conta de armazenamento. Pode ativar ou desativar a exclusão suave de uma conta de armazenamento a qualquer momento utilizando o portal Azure, PowerShell ou Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para permitir a eliminação suave do blob para a sua conta de armazenamento utilizando o portal Azure, siga estes passos:

1. No [portal do Azure](https://portal.azure.com/), navegue para a sua conta de armazenamento.
1. Localizar a opção **de Proteção de Dados** no **serviço Blob**.
1. Na secção **Recuperação,** **selecione Desligue o apagar suavemente para as bolhas**.
1. Especifique um período de retenção entre 1 e 365 dias. A Microsoft recomenda um período mínimo de retenção de sete dias.
1. Guarde as alterações.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Screenshot mostrando como permitir a eliminação suave no portal Azure":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para ativar a eliminação suave do blob com o PowerShell, ligue para o comando [Enable-AzStorageBlobDeleteRetentionPolicy,](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) especificando o período de retenção em dias.

O exemplo a seguir permite a eliminação suave da bolha e define o período de retenção para sete dias. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Para verificar as definições atuais para eliminar suavemente blob, ligue para o comando [Get-AzStorageBlobServiceProperty:](/powershell/module/az.storage/get-azstorageblobserviceproperty)

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Para permitir a eliminação suave do blob com O Azure CLI, ligue para o comando [de atualização de propriedades de serviço de armazenamento az,](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) especificando o período de retenção em dias.

O exemplo a seguir permite a eliminação suave da bolha e define o período de retenção para sete dias. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Para verificar as definições atuais para eliminar suavemente blob, ligue para o comando de [série de propriedades de serviço blob-service-properties de armazenamento az:](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_show)

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Passos seguintes

- [Eliminação recuperável para blobs](soft-delete-blob-overview.md)
- [Gerir e restaurar bolhas apagadas suaves](soft-delete-blob-manage.md)
