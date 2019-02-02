---
title: Carregar dados de imagem na cloud com o Armazenamento do Azure | Microsoft Docs
description: Utilize o armazenamento de Blobs do Azure com uma aplicação web para armazenar dados de aplicações
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: c1a279f85ef4e959869e0a3faa86b9ca7d3e05f7
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657676"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Tutorial: Carregar dados de imagem na cloud com o Armazenamento do Azure

Este tutorial é a primeira parte de uma série. Neste tutorial, irá aprender como implementar uma aplicação web que utiliza a biblioteca de cliente de armazenamento do Azure para carregar imagens para uma conta de armazenamento. Quando tiver terminado, terá uma aplicação web que armazena e apresenta imagens a partir do armazenamento do Azure.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![Vista do contentor de imagens](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Vista do contentor de imagens](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Criar um contentor e definir permissões
> * Obter uma chave de acesso
> * Implementar uma aplicação web no Azure
> * Configurar as definições da aplicação
> * Interagir com a aplicação web

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure. Criar uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar a CLI localmente, este tutorial requer que execute a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos 

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.  

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia 
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O exemplo carrega imagens para um contentor de blobs numa conta de Armazenamento do Azure. Uma conta de armazenamento fornece um espaço de nomes exclusivo para armazenar e aceder aos objetos de dados de armazenamento do Azure. Utilize o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) para criar uma conta de armazenamento no grupo de recursos que criou.

> [!IMPORTANT]
> Na parte 2 do tutorial, utilize o Azure Event Grid com o armazenamento de Blobs. Certifique-se criar a conta de armazenamento numa região do Azure que suporta o Event Grid. Para obter uma lista de regiões suportadas, consulte [produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

No comando seguinte, substitua o nome globalmente exclusivo para a conta de armazenamento de BLOBs onde vir o `<blob_storage_account>` marcador de posição.  

```azurecli-interactive
az storage account create --name <blob_storage_account> \
--location southeastasia --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>Criar contentores de armazenamento de BLOBs

A aplicação utiliza dois contentores na conta de armazenamento de Blobs. Os contentores são semelhantes às pastas e armazenam blobs. O contentor de *imagens* é para onde a aplicação carrega imagens com máxima resolução. Numa secção mais adiante da série, uma aplicação de função do Azure carrega as miniaturas de imagem dimensionadas para o contentor de *miniaturas*.

Obtenha a chave da conta de armazenamento com o comando [az storage account keys list](/cli/azure/storage/account/keys). Em seguida, utilize esta chave para criar dois contentores com o [criar contentor de armazenamento az](/cli/azure/storage/container#az_storage_container_create) comando.  

Neste caso, `<blob_storage_account>` é o nome da conta de armazenamento de Blobs que criou. O *imagens* acesso de público do contentor está definido como `off`. O *miniaturas* acesso de público do contentor está definido como `container`. O `container` definição de acesso público permite aos utilizadores que visitam a página da web para ver as miniaturas.

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey
```

Anote o nome de conta de armazenamento de BLOBs e a chave. A aplicação de exemplo utiliza estas definições para ligar à conta de armazenamento para carregar as imagens. 

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

Um [plano do serviço de aplicações](../../app-service/overview-hosting-plans.md) especifica o local, tamanho e funcionalidades da farm de servidores Web que aloja a aplicação.

Crie um plano do Serviço de Aplicações com o comando [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create).

O exemplo seguinte cria um plano do Serviço de Aplicações com o nome `myAppServicePlan`, que utiliza o escalão de preços **Gratuito**.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Criar uma aplicação Web

A aplicação web fornece um espaço de alojamento para o código de aplicação de exemplo que é implementado a partir do repositório de exemplo do GitHub. Crie uma [aplicação Web](../../app-service/overview.md) no plano do `myAppServicePlan`Serviço de Aplicações com o comando[az webapp create](/cli/azure/webapp#az_webapp_create).  

No comando seguinte, substitua `<web_app>` com um nome exclusivo. Os carateres válidos são `a-z`, `0-9` e `-`. Se `<web_app>` é não exclusivo, obtém a mensagem de erro: _Web site com o nome fornecido `<web_app>` já existe._ O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Implementar a aplicação de exemplo a partir do repositório do GitHub

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web. Neste tutorial, vai implementar a aplicação Web a partir de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Substitua `<web_app>` pelo nome da aplicação Web que criou no passo anterior.

O projeto de exemplo contém um [ASP.NET MVC](https://www.asp.net/mvc) aplicação. A aplicação aceita uma imagem, guarda-a numa conta de armazenamento e apresenta imagens a partir de um contentor de miniaturas. A aplicação web utiliza a [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet)e o [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) espaços de nomes de o biblioteca de cliente para interagir com o armazenamento do Azure de armazenamento do Azure.

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web. Neste tutorial, vai implementar a aplicação Web a partir de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Substitua `<web_app>` pelo nome da aplicação Web que criou no passo anterior.

---

```azurecli-interactive
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

## <a name="configure-web-app-settings"></a>Configurar as definições da aplicação Web

A aplicação Web de exemplo utiliza a [Biblioteca de Cliente de Armazenamento do Azure](/dotnet/api/overview/azure/storage?view=azure-dotnet) para pedir tokens de acesso, os quais são utilizados para carregar imagens. As credenciais da conta de armazenamento utilizadas pelo SDK de armazenamento são definidas nas definições da aplicação para a aplicação web. Adicionar as definições da aplicação para a aplicação implementada com o [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) comando.

No comando seguinte, substitua `<blob_storage_account>` com o nome da conta de armazenamento de BLOBs e `<blob_storage_key>` com a chave associada. Substitua `<web_app>` pelo nome da aplicação Web que criou no passo anterior.

```azurecli-interactive
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
```

Depois de implementar e configurar a aplicação web, pode testar a funcionalidade de carregamento de imagens na aplicação.

## <a name="upload-an-image"></a>Carregar uma imagem

Para testar a aplicação Web, navegue para o URL da aplicação publicada. O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.
Selecione o **carregar fotografias** região para selecionar e carregar um ficheiro ou arrastar um ficheiro para a região. A imagem desaparece se for carregada com êxito.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

![Aplicação ImageResizer](media/storage-upload-process-images/figure1.png)

No código de exemplo, o `UploadFiletoStorage` de tarefas no *Storagehelper.cs* arquivo é usado para carregar as imagens para o *imagens* contentor na conta de armazenamento através do [ UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) método. O exemplo de código seguinte contém a tarefa `UploadFiletoStorage`.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

As classes e métodos seguintes são utilizados na tarefa anterior:

|Classe  |Método  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

![Aplicação de Carregamento de Imagens](media/storage-upload-process-images/upload-app-nodejs.png)

No código de exemplo, a rota `post` é responsável por carregar a imagem para um contentor de blobs. A rota utiliza os módulos para ajudar a processar o carregamento:

- [multer](https://github.com/expressjs/multer) implementa a estratégia de carregamento para o manipulador de rotas.
- [para o stream](https://github.com/sindresorhus/into-stream) converte o buffer num fluxo conforme exigido pelo [createBlockBlobFromStream]. (http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html#createBlockBlobFromStream)

À medida que o ficheiro é enviado para a rota, o conteúdo do arquivo mantenha-se na memória até que o ficheiro é carregado para o contentor de Blobs.

> [!IMPORTANT]
> A carregar ficheiros grandes na memória, pode ter um efeito negativo no desempenho da sua aplicação web. Se pretender que os utilizadores para publicar ficheiros grandes, convém considerar a ficheiros no sistema de arquivos de servidor web de teste e, em seguida, agendamento carregamentos para o armazenamento de Blobs. Assim que os ficheiros estiverem no armazenamento de BLOBs, pode removê-lo do sistema de arquivos de servidor.

```javascript
const
      express = require('express')
    , router = express.Router()

    , multer = require('multer')
    , inMemoryStorage = multer.memoryStorage()
    , uploadStrategy = multer({ storage: inMemoryStorage }).single('image')

    , azureStorage = require('azure-storage')
    , blobService = azureStorage.createBlobService()

    , getStream = require('into-stream')
    , containerName = 'images'
;

const handleError = (err, res) => {
    res.status(500);
    res.render('error', { error: err });
};

const getBlobName = originalName => {
    const identifier = Math.random().toString().replace(/0\./, ''); // remove "0." from start of string
    return `${originalName}-${identifier}`;
};

router.post('/', uploadStrategy, (req, res) => {

    const
          blobName = getBlobName(req.file.originalname)
        , stream = getStream(req.file.buffer)
        , streamLength = req.file.buffer.length
    ;

    blobService.createBlockBlobFromStream(containerName, blobName, stream, streamLength, err => {

        if(err) {
            handleError(err);
            return;
        }

        res.render('success', { 
            message: 'File uploaded to Azure Blob storage.' 
        });
    });
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Verifique se a imagem é apresentada na conta de armazenamento

Inicie sessão no [portal do Azure](https://portal.azure.com). No menu à esquerda, selecione **Contas de armazenamento** e selecione o nome da conta de armazenamento. Sob **serviço Blob**, selecione **Blobs**, em seguida, selecione o **imagens** contentor.

Verifique se a imagem é apresentada no contentor.

![Vista do contentor de imagens](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Testar a visualização de miniaturas

Para testar a visualização de miniaturas, carregue uma imagem para o **miniaturas** contentor para verificar se a aplicação pode ler a **miniaturas** contentor.

Inicie sessão no [portal do Azure](https://portal.azure.com). No menu à esquerda, selecione **Contas de armazenamento** e selecione o nome da conta de armazenamento. Sob **serviço Blob**, selecione **Blobs**, em seguida, selecione o **miniaturas** contentor. Selecione **Carregar** para abrir o painel **Carregar blob**.

Escolha um ficheiro com o Seletor de ficheiros e selecione **carregar**.

Navegue novamente para a aplicação para verificar se a imagem carregada no contentor de **miniaturas** está visível.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![Vista do contentor de imagens](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Vista do contentor de imagens](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Na parte dois da série, automatizar a criação de imagem em miniatura para que precise esta imagem. No contentor de **miniaturas** no portal do Azure, selecione a imagem que carregou e selecione **Eliminar** para eliminar a imagem. 

Pode ativar a CDN para conteúdo de cache da sua conta de armazenamento do Azure. Para obter mais informações sobre como ativar a CDN com a sua conta de armazenamento do Azure, consulte [integrar uma conta de armazenamento do Azure CDN do Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Passos Seguintes

Na primeira parte da série, aprendeu a configurar uma aplicação web para interagir com o armazenamento.

Vá para a parte dois da série para saber como utilizar o Event Grid para acionar uma função do Azure para redimensionar uma imagem.

> [!div class="nextstepaction"]
> [Utilizar o Event Grid para acionar uma Função do Azure para redimensionar uma imagem carregada](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
