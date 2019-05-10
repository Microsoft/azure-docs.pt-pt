---
title: Proteger o acesso aos dados de uma aplicação na cloud com o Armazenamento do Azure | Microsoft Docs
description: Utilizar tokens SAS, encriptação e HTTPS para proteger os dados da aplicação na cloud.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 05/30/2018
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: 48bd21ad2f78879091419477dcc3c278d6e74711
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236625"
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Proteger o acesso aos dados de uma aplicação na cloud

Este tutorial é a terceira parte de uma série. Ficará a saber como proteger o acesso à conta de armazenamento. 

Na terceira parte da série, ficará a saber como:

> [!div class="checklist"]
> * Utilizar tokens SAS para aceder a imagens em miniatura
> * Ativar a encriptação do lado do servidor
> * Ativar o transporte apenas por HTTPS

O [Armazenamento de blobs do Azure](../common/storage-introduction.md#blob-storage) proporciona um serviço robusto para armazenar os ficheiros para as aplicações. Este tutorial expande [o tópico anterior][previous-tutorial] para mostrar como proteger o acesso à conta de armazenamento a partir de uma aplicação Web. Quando tiver terminado, as imagens são encriptadas e a aplicação Web utiliza os tokens SAS protegidos para aceder às imagens em miniatura.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial tem de ter concluído o tutorial de armazenamento anterior: [Automatizar o redimensionamento de imagens carregadas com o Event Grid][previous-tutorial]. 

## <a name="set-container-public-access"></a>Definir o acesso público ao contentor

Nesta parte da série de tutoriais, os tokens SAS servem para aceder às miniaturas. Neste passo, definiu o acesso público do contentor de _miniaturas_ como `off`.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbnails  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Configurar os tokens SAS para miniaturas

Na primeira parte desta série de tutoriais, a aplicação Web estava a mostrar imagens de um contentor público. Nesta parte da série, utilize os tokens [Assinatura de Acesso Partilhado (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) para obter as imagens em miniatura. Os tokens SAS permitem-lhe proporcionar acesso restrito a um contentor ou blob com base em IP, protocolo, intervalo de tempo ou direitos permitidos.

Neste exemplo, o repositório de código fonte utiliza o ramo `sasTokens`, que tem um exemplo de código atualizado. Elimine a implementação do GitHub existente com [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Em seguida, configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).  

No comando seguinte, `<web-app>` é o nome da aplicação Web.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

O ramo `sasTokens` do repositório atualiza o ficheiro `StorageHelper.cs`. Substitui a tarefa `GetThumbNailUrls` pelo exemplo de código abaixo. A tarefa atualizada obtém os URLs da miniatura ao definir uma [SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet) para especificar a hora de início, a hora de expiração e as permissões para o token SAS. Depois de implementada, a aplicação Web obtém as miniaturas com um URL através de um token SAS. A tarefa atualizada é mostrada no exemplo seguinte:
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

As seguintes classes, propriedades e métodos são utilizados na tarefa anterior:

|Classe  |Propriedades| Métodos  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.azure.cosmos.table.storagecredentials)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)     | |[GetContainerReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.azure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment?view=azure-dotnet)    | [Resultados](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[Permissões](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>Encriptação do lado do servidor

A [Encriptação do Serviço de Armazenamento (SSE) do Azure](../common/storage-service-encryption.md) ajuda a proteger e a salvaguardar os seus dados. A SSE encripta os dados inativos, ao processar a encriptação, a desencriptação e a gestão de chaves. Todos os dados são encriptados através de uma [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das cifras em bloco mais fortes disponíveis.

O SSE encripta automaticamente dados em todos os escalões de desempenho (Standard e Premium), todos os modelos de implementação (Azure Resource Manager e Clássico) e todos os serviços de Armazenamento do Azure (Blob, Fila, Tabela e Ficheiro). 

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

## <a name="next-steps"></a>Passos Seguintes

Na terceira parte da série, aprendeu a proteger o acesso à conta de armazenamento, como:

> [!div class="checklist"]
> * Utilizar tokens SAS para aceder a imagens em miniatura
> * Ativar a encriptação do lado do servidor
> * Ativar o transporte apenas por HTTPS

Avance para a quarta parte da série para aprender a monitorizar e a resolver problemas de uma aplicação de armazenamento na cloud.

> [!div class="nextstepaction"]
> [Monitorizar e resolver problemas de armazenamento da aplicação na cloud](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
