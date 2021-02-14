---
title: Ativar e gerir a versão blob
titleSuffix: Azure Storage
description: Saiba como ativar a versão blob no portal Azure ou utilizando um modelo de Gestor de Recursos Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 5b6bd16eacf4b1bbb7b93f5500813e7fa9dc7eef
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095851"
---
# <a name="enable-and-manage-blob-versioning"></a>Ativar e gerir a versão blob

Pode permitir que a versão de armazenamento Blob mantenha automaticamente as versões anteriores de uma bolha quando esta é modificada ou eliminada. Quando a versão blob estiver ativada, poderá restaurar uma versão anterior de uma bolha para recuperar os seus dados se este for erroneamente modificado ou eliminado.

Este artigo mostra como ativar ou desativar a versão blob para a conta de armazenamento utilizando o portal Azure ou um modelo de Gestor de Recursos Azure. Para saber mais sobre a versão blob, consulte [a versão Blob](versioning-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Ativar a criação de versões de blobs

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para permitir a versão blob para uma conta de armazenamento no portal Azure:

1. Navegue para a sua conta de armazenamento no portal.
1. No **serviço Blob,** escolha **a proteção de dados.**
1. Na secção **versão,** selecione **Ativado**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Screenshot mostrando como permitir a versão blob no portal Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para ativar a versão blob para uma conta de armazenamento com o PowerShell, instale primeiro a versão 2.3.0 ou posterior do módulo [Az.Storage.](https://www.powershellgallery.com/packages/Az.Storage) Em seguida, ligue para o comando [Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) para ativar a versão, como mostra o exemplo a seguir. Lembre-se de substituir os valores em suportes angulares pelos seus próprios valores:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para ativar a versão blob para uma conta de armazenamento com o Azure CLI, instale primeiro a versão Azure CLI 2.2.0 ou posterior. Em seguida, ligue para o comando [de atualização blob-service-properties de serviço de armazenamento az](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) para ativar a versão, como mostrado no exemplo seguinte. Lembre-se de substituir os valores em suportes angulares pelos seus próprios valores:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Modelo](#tab/template)

Para permitir a versão blob com um modelo, crie um modelo com a propriedade **IsVersioningEnabled** para **ser verdadeiro**. Os passos seguintes descrevem como criar um modelo no portal Azure.

1. No portal Azure, escolha **Criar um recurso.**
1. Em **Search the Marketplace**, **digitar a implementação do modelo** e, em seguida, premir **ENTER**.
1. Escolha a **implementação do modelo,** escolha **Criar** e, em seguida, escolha **Construir o seu próprio modelo no editor**.
1. No editor de modelo, cole no seguinte JSON. Substitua o `<accountName>` espaço reservado pelo nome da sua conta de armazenamento.
1. Guarde o modelo.
1. Especifique o grupo de recursos da conta e, em seguida, escolha o botão **Comprar** para implementar o modelo e permitir a versão blob.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Para obter mais informações sobre a implantação de recursos com modelos no portal Azure, consulte [implementar recursos com o portal Azure.](../../azure-resource-manager/templates/deploy-portal.md)

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Modificar uma bolha para desencadear uma nova versão

O exemplo de código que se segue mostra como desencadear a criação de uma nova versão com a biblioteca cliente Azure Storage para .NET, versão [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) ou posterior. Antes de executar este exemplo, certifique-se de que ativou a versão para a sua conta de armazenamento.

O exemplo cria uma bolha de bloco e, em seguida, atualiza os metadados da bolha. A atualização dos metadados da blob desencadeia a criação de uma nova versão. O exemplo recupera a versão inicial e a versão atual, e mostra que apenas a versão atual inclui os metadados.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Lista de versões blob

Para listar versões blob ou instantâneos com a biblioteca de clientes .NET v12, especifique o parâmetro [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) com o campo **Versão.**

O exemplo de código que se segue mostra como listar a versão blobs com a biblioteca cliente Azure Storage para .NET, versão [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) ou posterior. Antes de executar este exemplo, certifique-se de que ativou a versão para a sua conta de armazenamento.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Passos seguintes

- [Versão blob](versioning-overview.md)
- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](./soft-delete-blob-overview.md)