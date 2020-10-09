---
title: Acesso seguro aos dados da aplicação
titleSuffix: Azure Storage
description: Utilizar tokens SAS, encriptação e HTTPS para proteger os dados da aplicação na cloud.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: tamram
ms.reviewer: ozgun
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 8d11fada41d0152b44be6cb0cdef41e68808689b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967515"
---
# <a name="secure-access-to-application-data"></a>Acesso seguro aos dados da aplicação

Este tutorial é a terceira parte de uma série. Ficará a saber como proteger o acesso à conta de armazenamento. 

Na terceira parte da série, ficará a saber como:

> [!div class="checklist"]
> * Utilizar tokens SAS para aceder a imagens em miniatura
> * Ativar a encriptação do lado do servidor
> * Ativar o transporte apenas por HTTPS

O [Armazenamento de blobs do Azure](../common/storage-introduction.md#blob-storage) proporciona um serviço robusto para armazenar os ficheiros para as aplicações. Este tutorial expande [o tópico anterior][previous-tutorial] para mostrar como proteger o acesso à conta de armazenamento a partir de uma aplicação Web. Quando tiver terminado, as imagens são encriptadas e a aplicação Web utiliza os tokens SAS protegidos para aceder às imagens em miniatura.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter concluído o tutorial de armazenamento anterior: [Automatizar o redimensionamento de imagens carregadas com o Event Grid][previous-tutorial].

## <a name="set-container-public-access"></a>Definir o acesso público ao contentor

Nesta parte da série de tutoriais, os tokens SAS servem para aceder às miniaturas. Neste passo, definiu o acesso público do contentor de *miniaturas* como `off`.

```bash
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

```powershell
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission `
    --account-name $blobStorageAccount `
    --account-key $blobStorageAccountKey `
    --name thumbnails `
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>Configurar os tokens SAS para miniaturas

Na primeira parte desta série de tutoriais, a aplicação Web estava a mostrar imagens de um contentor público. Nesta parte da série, você usa fichas de acesso compartilhada (SAS) para recuperar as imagens de miniatura. Os tokens SAS permitem-lhe proporcionar acesso restrito a um contentor ou blob com base em IP, protocolo, intervalo de tempo ou direitos permitidos. Para obter mais informações sobre o SAS, consulte [Grant acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).

Neste exemplo, o repositório de código fonte utiliza o ramo `sasTokens`, que tem um exemplo de código atualizado. Elimine a implementação do GitHub existente com [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Em seguida, configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

No comando seguinte, `<web-app>` é o nome da aplicação Web.

```bash
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> `
    --resource-group myResourceGroup --branch sasTokens --manual-integration `
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

O ramo `sasTokens` do repositório atualiza o ficheiro `StorageHelper.cs`. Substitui a tarefa `GetThumbNailUrls` pelo exemplo de código abaixo. A tarefa atualizada recupera os URLs de miniatura utilizando um [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) para especificar a hora de início, o tempo de validade e permissões para o token SAS. Depois de implementada, a aplicação Web obtém as miniaturas com um URL através de um token SAS. A tarefa atualizada é mostrada no exemplo seguinte:

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

As seguintes classes, propriedades e métodos são utilizados na tarefa anterior:

| Classe | Propriedades | Métodos |
|-------|------------|---------|
|[ArmazenamentoSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [Uri](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Existe](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem](/dotnet/api/azure.storage.blobs.models.blobitem) | [Nome](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [Query](/dotnet/api/system.uribuilder.query) |  |
|[Lista](/dotnet/api/system.collections.generic.list-1) | | [Adicionar](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="azure-storage-encryption"></a>Encriptação do Armazenamento do Azure

[A encriptação do Azure Storage](../common/storage-service-encryption.md) ajuda-o a proteger e a salvaguardar os seus dados encriptando os dados em repouso e manuseando encriptação e desencriptação. Todos os dados são encriptados através de uma [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das cifras em bloco mais fortes disponíveis.

Pode optar por ter a Microsoft a gerir as chaves de encriptação, ou pode trazer as suas próprias chaves com teclas geridas pelo cliente armazenadas no Cofre de Chaves Azure ou no Key Vault Managed Hardware Security Model (HSM) (pré-visualização). Para obter mais informações, consulte [as chaves geridas pelo Cliente para encriptação de armazenamento Azure](../common/customer-managed-keys-overview.md).

A encriptação de armazenamento Azure encripta automaticamente os dados em todos os níveis de desempenho (Standard e Premium), todos os modelos de implementação (Azure Resource Manager e Classic) e todos os serviços de Armazenamento Azure (Blob, Fila, Tabela e Arquivo).

## <a name="enable-https-only"></a>Ativar apenas HTTPS

Para garantir que os pedidos de dados de/para uma conta de armazenamento são seguros, pode limitar os pedidos apenas a HTTPS. Atualize o protocolo necessário da conta de armazenamento através do comando [az storage account update](/cli/azure/storage/account).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Teste a ligação com `curl` através do protocolo `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Agora que a transferência segura é necessária, recebe a mensagem seguinte:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Passos seguintes

Na terceira parte da série, aprendeu a proteger o acesso à conta de armazenamento, como:

> [!div class="checklist"]
> * Utilizar tokens SAS para aceder a imagens em miniatura
> * Ativar a encriptação do lado do servidor
> * Ativar o transporte apenas por HTTPS

Avance para a quarta parte da série para aprender a monitorizar e a resolver problemas de uma aplicação de armazenamento na cloud.

> [!div class="nextstepaction"]
> [Monitorizar e resolver problemas de armazenamento da aplicação na cloud](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
