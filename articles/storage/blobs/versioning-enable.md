---
title: Ativar e gerir a versão blob
titleSuffix: Azure Storage
description: Saiba como ativar a versão blob no portal Azure ou utilizando um modelo de Gestor de Recursos Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 64ab86836989030c36ad2e8ad054c364b382af0b
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95248021"
---
# <a name="enable-and-manage-blob-versioning"></a>Ativar e gerir a versão blob

Pode permitir que a versão de armazenamento Blob mantenha automaticamente as versões anteriores de um objeto.  Quando a versão blob estiver ativada, pode restaurar uma versão anterior de uma bolha para recuperar os seus dados se for erroneamente modificada ou eliminada.

Este artigo mostra como ativar ou desativar a versão blob para a conta de armazenamento utilizando o portal Azure ou um modelo de Gestor de Recursos Azure. Para saber mais sobre a versão blob, consulte [a versão Blob](versioning-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Ativar a criação de versões de blobs

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para permitir a versão blob no portal Azure:

1. Navegue para a sua conta de armazenamento no portal.
1. No **serviço Blob,** escolha **a proteção de dados.**
1. Na secção **versão,** selecione **Ativado**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Screenshot mostrando como permitir a versão blob no portal Azure":::

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

## <a name="next-steps"></a>Próximos passos

- [Versão blob](versioning-overview.md)
- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](soft-delete-overview.md)
