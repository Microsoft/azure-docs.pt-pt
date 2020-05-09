---
title: Ativar e gerir a versão blob (pré-visualização)
titleSuffix: Azure Storage
description: Saiba como permitir a versão blob no portal Azure ou utilizando um modelo de Gestor de Recursos Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 0e24bcb54fd26d4a3d983681b3348ef736b277cf
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884348"
---
# <a name="enable-and-manage-blob-versioning"></a>Ativar e gerir a versão blob

Pode ativar ou desativar a versão blob (pré-visualização) para a conta de armazenamento a qualquer momento utilizando o portal Azure ou um modelo de Gestor de Recursos Azure.

## <a name="enable-blob-versioning"></a>Ativar a versão blob

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para permitir a versão blob no portal Azure:

1. Navegue para a sua conta de armazenamento no portal.
1. No **serviço Blob,** escolha **a proteção de dados.**
1. Na secção **versão,** selecione **Ativado**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Screenshot mostrando como permitir a versão blob no portal Azure":::

# <a name="template"></a>[Modelo](#tab/template)

Para permitir a versão blob com um modelo, crie um modelo com a propriedade **IsVersioningEnabled** **para verdade**. Os seguintes passos descrevem como criar um modelo no portal Azure.

1. No portal Azure, escolha **Criar um recurso.**
1. Em **Pesquisar no Marketplace**, escreva **implementação de modelo** e prima **ENTER**.
1. Escolha **a implementação do modelo,** escolha **Criar,** e depois escolha **construir o seu próprio modelo no editor**.
1. No editor de modelos, cola no seguinte JSON. Substitua `<accountName>` o espaço reservado com o nome da sua conta de armazenamento.
1. Guarde o modelo.
1. Especifique o grupo de recursos da conta e, em seguida, escolha o botão **de compra** para implementar o modelo e ativar a versão blob.

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

Para obter mais informações sobre a implantação de recursos com modelos no portal Azure, consulte a Implantação de recursos com o [portal Azure.](../../azure-resource-manager/templates/deploy-portal.md)

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Modificar uma bolha para desencadear uma nova versão

O exemplo de código que se segue mostra como desencadear a criação de uma nova versão com a biblioteca de clientes Azure Storage para a versão 12 .NET. Antes de executar este exemplo, certifique-se de ter ativado a versão para a sua conta de armazenamento.

O exemplo cria uma bolha de bloco, e depois atualiza os metadados da bolha. Atualizar os metadados da bolha desencadeia a criação de uma nova versão. O exemplo recupera a versão inicial e a versão atual, e mostra que apenas a versão atual inclui os metadados.

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>Passos seguintes

- [Versão blob (pré-visualização)](versioning-overview.md)
- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](soft-delete-overview.md)
