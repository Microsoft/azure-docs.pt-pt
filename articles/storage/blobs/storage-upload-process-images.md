---
title: Carregar dados de imagem na cloud com o Armazenamento do Azure | Microsoft Docs
description: Usar o armazenamento de BLOBs do Azure com um aplicativo Web para armazenar dados de aplicativo
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: mhopkins
ms.reviewer: dineshm
ms.openlocfilehash: afa4672d2a8f65b61d634b95b695d1c9814bf991
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053453"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Tutorial: carregar dados de imagem na nuvem com o armazenamento do Azure

Este tutorial é a primeira parte de uma série. Neste tutorial, você aprenderá a implantar um aplicativo Web que usa a biblioteca de cliente de armazenamento do Azure para carregar imagens em uma conta de armazenamento. Quando tiver terminado, você terá um aplicativo Web que armazena e exibe imagens do armazenamento do Azure.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![Aplicativo de redimensionador de imagem no .NET](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[SDK do node. js v2](#tab/nodejs)
![Aplicativo de redimensionador de imagem no node. js v2](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[SDK do V10 do node. js](#tab/nodejsv10)
![Aplicativo de redimensionador de imagem no node. js v10](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Create a storage account
> * Criar um contentor e definir permissões
> * Obter uma chave de acesso
> * Implantar um aplicativo Web no Azure
> * Configurar as definições da aplicação
> * Interagir com o aplicativo Web

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure. Crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Para instalar e usar a CLI localmente, este tutorial requer que você execute o CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos 

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.  

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia 
```

## <a name="create-a-storage-account"></a>Create a storage account

O exemplo carrega imagens para um contentor de blobs numa conta de Armazenamento do Azure. Uma conta de armazenamento fornece um espaço de nomes exclusivo para armazenar e aceder aos objetos de dados de armazenamento do Azure. Utilize o comando [az storage account create](/cli/azure/storage/account) para criar uma conta de armazenamento no grupo de recursos que criou.

> [!IMPORTANT]
> Na parte 2 do tutorial, você usa a grade de eventos do Azure com o armazenamento de BLOBs. Certifique-se de criar sua conta de armazenamento em uma região do Azure que ofereça suporte à grade de eventos. Para obter uma lista de regiões com suporte, consulte [produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

No comando a seguir, substitua seu próprio nome globalmente exclusivo para a conta de armazenamento de BLOBs em que você vê o espaço reservado `<blob_storage_account>`.  

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

az storage account create --name $blobStorageAccount \
--location southeastasia --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>Criar contêineres de armazenamento de BLOBs

A aplicação utiliza dois contentores na conta de armazenamento de Blobs. Os contêineres são semelhantes às pastas e aos blobs de armazenamento. O contentor de *imagens* é para onde a aplicação carrega imagens com máxima resolução. Numa secção mais adiante da série, uma aplicação de função do Azure carrega as miniaturas de imagem dimensionadas para o contentor de *miniaturas*.

Obtenha a chave da conta de armazenamento com o comando [az storage account keys list](/cli/azure/storage/account/keys). Em seguida, use essa chave para criar dois contêineres com o comando [AZ Storage container Create](/cli/azure/storage/container) .  

O acesso público do contêiner de *imagens* é definido como `off`. O acesso público do contêiner de *miniaturas* é definido como `container`. A configuração de acesso público `container` permite que os usuários que visitam a página da Web exibam as miniaturas.

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey
```

Anote o nome e a chave da conta de armazenamento de BLOBs. O aplicativo de exemplo usa essas configurações para se conectar à conta de armazenamento para carregar as imagens. 

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

Um [plano do serviço de aplicações](../../app-service/overview-hosting-plans.md) especifica o local, tamanho e funcionalidades da farm de servidores Web que aloja a aplicação.

Crie um plano do Serviço de Aplicações com o comando [az appservice plan create](/cli/azure/appservice/plan).

O exemplo seguinte cria um plano do Serviço de Aplicações com o nome `myAppServicePlan`, que utiliza o escalão de preços **Gratuito**.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Criar uma aplicação Web

O aplicativo Web fornece um espaço de hospedagem para o código do aplicativo de exemplo que é implantado do repositório de exemplo do GitHub. Crie uma [aplicação Web](../../app-service/overview.md) no plano do `myAppServicePlan`Serviço de Aplicações com o comando[az webapp create](/cli/azure/webapp).  

No comando a seguir, substitua `<web_app>` por um nome exclusivo. Os carateres válidos são `a-z`, `0-9` e `-`. Se `<web_app>` não for exclusivo, obtém a mensagem de erro _O Website com o nome `<web_app>` já existe._ O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp=<web_app>

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Implementar a aplicação de exemplo a partir do repositório do GitHub

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web. Neste tutorial, vai implementar a aplicação Web a partir de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

O projeto de exemplo contém um aplicativo [MVC ASP.net](https://www.asp.net/mvc) . O aplicativo aceita uma imagem, salva-a em uma conta de armazenamento e exibe imagens de um contêiner em miniatura. O aplicativo Web usa os namespaces [Microsoft. Azure. Storage](/dotnet/api/overview/azure/storage), [Microsoft. Azure. Storage. blob](/dotnet/api/microsoft.azure.storage.blob)e Microsoft. Azure. Storage. auth da biblioteca de cliente do armazenamento do Azure para interagir com o armazenamento do Azure.

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v2-sdktabnodejs"></a>[SDK do node. js v2](#tab/nodejs)
O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web. Neste tutorial, vai implementar a aplicação Web a partir de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source). 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[SDK do V10 do node. js](#tab/nodejsv10)
O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web. Neste tutorial, vai implementar a aplicação Web a partir de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10). Configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source). 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10
```

---

## <a name="configure-web-app-settings"></a>Configurar as definições da aplicação Web

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

A aplicação Web de exemplo utiliza a [Biblioteca de Cliente de Armazenamento do Azure](/dotnet/api/overview/azure/storage) para pedir tokens de acesso, os quais são utilizados para carregar imagens. As credenciais da conta de armazenamento usadas pelo SDK de armazenamento são definidas nas configurações do aplicativo para o aplicativo Web. Adicione configurações de aplicativo ao aplicativo implantado com o comando [AZ webapp config appSettings Set](/cli/azure/webapp/config/appsettings) .

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=$blobStorageAccountKey  
```

# <a name="nodejs-v2-sdktabnodejs"></a>[SDK do node. js v2](#tab/nodejs)

A aplicação Web de exemplo utiliza a [Biblioteca de Cliente de Armazenamento do Azure](https://docs.microsoft.com/javascript/api/azure-storage) para pedir tokens de acesso, os quais são utilizados para carregar imagens. As credenciais da conta de armazenamento usadas pelo SDK de armazenamento são definidas nas configurações do aplicativo para o aplicativo Web. Adicione configurações de aplicativo ao aplicativo implantado com o comando [AZ webapp config appSettings Set](/cli/azure/webapp/config/appsettings) .

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__AccountKey=$blobStorageAccountKey \
AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[SDK do V10 do node. js](#tab/nodejsv10)

A aplicação Web de exemplo utiliza a [Biblioteca de Cliente de Armazenamento do Azure](https://github.com/Azure/azure-storage-js) para pedir tokens de acesso, os quais são utilizados para carregar imagens. As credenciais da conta de armazenamento usadas pelo SDK de armazenamento são definidas nas configurações do aplicativo para o aplicativo Web. Adicione configurações de aplicativo ao aplicativo implantado com o comando [AZ webapp config appSettings Set](/cli/azure/webapp/config/appsettings) .

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Depois de implantar e configurar o aplicativo Web, você pode testar a funcionalidade de carregamento de imagem no aplicativo.

## <a name="upload-an-image"></a>Carregar uma imagem

Para testar a aplicação Web, navegue para o URL da aplicação publicada. O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Selecione a região **carregar fotos** para selecionar e carregar um arquivo ou arraste um arquivo para a região. A imagem desaparece se for carregada com êxito. A seção de **miniaturas geradas** permanecerá vazia até que seja testada posteriormente neste tópico.

![Carregar fotos no .NET](media/storage-upload-process-images/figure1.png)

No código de exemplo, a tarefa de `UploadFiletoStorage` no arquivo *Storagehelper.cs* é usada para carregar as imagens no contêiner de *imagens* dentro da conta de armazenamento usando o método [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) . O exemplo de código seguinte contém a tarefa `UploadFiletoStorage`.

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
|[StorageCredentials](/dotnet/api/microsoft.azure.cosmos.table.storagecredentials)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)       |
|[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)     |[GetContainerReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getcontainerreference)         |
|[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer)    | [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference)        |
|[CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob)     | [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.file.cloudfile.uploadfromstreamasync)        |

# <a name="nodejs-v2-sdktabnodejs"></a>[SDK do node. js v2](#tab/nodejs)

Selecione **escolher arquivo** para selecionar um arquivo e clique em **carregar imagem**. A seção de **miniaturas geradas** permanecerá vazia até que seja testada posteriormente neste tópico. 

![Carregar fotos no node. js v2](media/storage-upload-process-images/upload-app-nodejs.png)

No código de exemplo, a rota `post` é responsável por carregar a imagem para um contentor de blobs. A rota utiliza os módulos para ajudar a processar o carregamento:

- [Multer](https://github.com/expressjs/multer) implementa a estratégia de upload para o manipulador de rotas.
- [no-Stream](https://github.com/sindresorhus/into-stream) converte o buffer em um fluxo, conforme exigido pelo [createBlockBlobFromStream]. (https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html)

À medida que o arquivo é enviado para a rota, o conteúdo do arquivo permanece na memória até que o arquivo seja carregado para o contêiner de BLOB.

> [!IMPORTANT]
> Carregar arquivos grandes na memória pode ter um efeito negativo no desempenho do aplicativo Web. Se você espera que os usuários postem arquivos grandes, convém considerar os arquivos de preparo no sistema de arquivos do servidor Web e, em seguida, agendar os carregamentos no armazenamento de BLOBs. Depois que os arquivos estiverem no armazenamento de BLOBs, você poderá removê-los do sistema de arquivos do servidor.

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

# <a name="nodejs-v10-sdktabnodejsv10"></a>[SDK do V10 do node. js](#tab/nodejsv10)

Selecione **escolher arquivo** para selecionar um arquivo e clique em **carregar imagem**. A seção de **miniaturas geradas** permanecerá vazia até que seja testada posteriormente neste tópico. 

![Carregar fotos em V10 node. js](media/storage-upload-process-images/upload-app-nodejs.png)

No código de exemplo, a rota `post` é responsável por carregar a imagem para um contentor de blobs. A rota utiliza os módulos para ajudar a processar o carregamento:

- [Multer](https://github.com/expressjs/multer) implementa a estratégia de upload para o manipulador de rotas.
- [no-Stream](https://github.com/sindresorhus/into-stream) converte o buffer em um fluxo, conforme exigido pelo [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html).

À medida que o arquivo é enviado para a rota, o conteúdo do arquivo permanece na memória até que o arquivo seja carregado para o contêiner de BLOB.

> [!IMPORTANT]
> Carregar arquivos grandes na memória pode ter um efeito negativo no desempenho do aplicativo Web. Se você espera que os usuários postem arquivos grandes, convém considerar os arquivos de preparo no sistema de arquivos do servidor Web e, em seguida, agendar os carregamentos no armazenamento de BLOBs. Depois que os arquivos estiverem no armazenamento de BLOBs, você poderá removê-los do sistema de arquivos do servidor.

```javascript
const {
  Aborter,
  BlobURL,
  BlockBlobURL,
  ContainerURL,
  ServiceURL,
  StorageURL,
  SharedKeyCredential,
  uploadStreamToBlockBlob
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };
const ONE_MINUTE = 60 * 1000;
const aborter = Aborter.timeout(30 * ONE_MINUTE);

const sharedKeyCredential = new SharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(sharedKeyCredential);
const serviceURL = new ServiceURL(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, ''); 
  return `${identifier}-${originalName}`;
};

router.post('/', uploadStrategy, async (req, res) => {

    const blobName = getBlobName(req.file.originalname);
    const stream = getStream(req.file.buffer);
    const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
    const blobURL = BlobURL.fromContainerURL(containerURL, blobName);
    const blockBlobURL = BlockBlobURL.fromBlobURL(blobURL);

    try {
      
      await uploadStreamToBlockBlob(aborter, stream,
        blockBlobURL, uploadOptions.bufferSize, uploadOptions.maxBuffers);

      res.render('success', { message: 'File uploaded to Azure Blob storage.' });   

    } catch (err) {

      res.render('error', { message: 'Something went wrong.' });

    }
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Verifique se a imagem é apresentada na conta de armazenamento

Inicie sessão no [portal do Azure](https://portal.azure.com). No menu à esquerda, selecione **Contas de armazenamento** e selecione o nome da conta de armazenamento. Em **serviço blob**, selecione **BLOBs**e, em seguida, selecione o contêiner **imagens** .

Verifique se a imagem é apresentada no contentor.

![Lista de contêineres de imagens do portal do Azure](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Testar a visualização de miniaturas

Para testar a exibição em miniatura, você carregará uma imagem no contêiner de **miniaturas** para verificar se o aplicativo pode ler o contêiner de **miniaturas** .

Inicie sessão no [portal do Azure](https://portal.azure.com). No menu à esquerda, selecione **Contas de armazenamento** e selecione o nome da conta de armazenamento. Em **serviço blob**, selecione **BLOBs**e, em seguida, selecione o contêiner **miniaturas** . Selecione **Carregar** para abrir o painel **Carregar blob**.

Escolha um arquivo com o seletor de arquivos e selecione **carregar**.

Navegue novamente para a aplicação para verificar se a imagem carregada no contentor de **miniaturas** está visível.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![Aplicativo de redimensionamento de imagem .NET com a nova imagem exibida](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[SDK do node. js v2](#tab/nodejs)
![Aplicativo do redimensionador de imagem do node. js V2 com nova imagem exibida](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[SDK do V10 do node. js](#tab/nodejsv10)
![Aplicativo de redimensionador de imagem V10 node. js com nova imagem exibida](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Na parte dois da série, você automatiza a criação de imagens em miniatura para não precisar dessa imagem. No contentor de **miniaturas** no portal do Azure, selecione a imagem que carregou e selecione **Eliminar** para eliminar a imagem. 

Você pode habilitar a CDN para armazenar em cache o conteúdo da sua conta de armazenamento do Azure. Para obter mais informações sobre como habilitar a CDN com sua conta de armazenamento do Azure, consulte [integrar uma conta de armazenamento do Azure com a CDN do Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Passos seguintes

Na parte um da série, você aprendeu a configurar um aplicativo Web para interagir com o armazenamento.

Vá para a parte dois da série para saber mais sobre como usar a grade de eventos para disparar uma função do Azure para redimensionar uma imagem.

> [!div class="nextstepaction"]
> [Utilizar o Event Grid para acionar uma Função do Azure para redimensionar uma imagem carregada](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
