---
title: Carregar dados de imagem na cloud com o Armazenamento do Azure | Microsoft Docs
description: Utilize o armazenamento de Blobs do Azure com uma aplicação web para armazenar dados de aplicações
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 11/16/2019
ms.author: mhopkins
ms.reviewer: dineshm
ms.openlocfilehash: e4a2b1ee1b2b2726b7e0a807a965dbf4bc6b9500
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197016"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Tutorial: Carregar dados de imagem na cloud com o armazenamento do Azure

Este tutorial é a primeira parte de uma série. Neste tutorial, irá aprender como implementar uma aplicação web que utiliza a biblioteca de cliente de armazenamento do Azure para carregar imagens para uma conta de armazenamento. Quando tiver terminado, terá uma aplicação web que armazena e apresenta imagens a partir do armazenamento do Azure.

# <a name="net"></a>[\.NET](#tab/dotnet)
![App de resizer de imagem em .NET](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdk"></a>[Node.js V2 SDK](#tab/nodejs)
![Aplicativo de resizer de imagem no Node.js V2](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)
![Aplicativo de resizer de imagem no Node.js V10](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

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

Para concluir este tutorial, precisa de uma subscrição do Azure. Crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar a CLI localmente, este tutorial requer que execute a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos: 

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.  

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia

```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O exemplo carrega imagens para um contentor de blobs numa conta de Armazenamento do Azure. Uma conta de armazenamento fornece um espaço de nomes exclusivo para armazenar e aceder aos objetos de dados de armazenamento do Azure. Utilize o comando [az storage account create](/cli/azure/storage/account) para criar uma conta de armazenamento no grupo de recursos que criou.

> [!IMPORTANT]
> Na parte 2 do tutorial, utilize o Azure Event Grid com o armazenamento de Blobs. Certifique-se criar a conta de armazenamento numa região do Azure que suporta o Event Grid. Para obter uma lista de regiões apoiadas, consulte [os produtos Azure por região.](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)

No comando seguinte, substitua o seu nome globalmente único para a conta de armazenamento Blob onde vê o `<blob_storage_account>` espaço reservado.

```azurecli-interactive
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia --resource-group myResourceGroup --sku Standard_LRS --kind blobstorage --access-tier hot

```

## <a name="create-blob-storage-containers"></a>Criar contentores de armazenamento de BLOBs

A aplicação utiliza dois contentores na conta de armazenamento de Blobs. Os contentores são semelhantes às pastas e armazenam blobs. O contentor de *imagens* é para onde a aplicação carrega imagens com máxima resolução. Numa secção mais adiante da série, uma aplicação de função do Azure carrega as miniaturas de imagem dimensionadas para o contentor de *miniaturas*.

Obtenha a chave da conta de armazenamento com o comando [az storage account keys list](/cli/azure/storage/account/keys). Em seguida, utilize esta chave para criar dois recipientes com o recipiente de [armazenamento az criar](/cli/azure/storage/container) comando.  

O acesso público do contentor de *imagens* está definido para `off`. O acesso público do contentor das *miniaturas* está definido para `container`. A definição de acesso público `container` permite que os utilizadores que visitam a página web vejam as miniaturas.

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create -n images --account-name $blobStorageAccount --account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount --account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey

```

Anote o nome de conta de armazenamento de BLOBs e a chave. A aplicação de exemplo utiliza estas definições para ligar à conta de armazenamento para carregar as imagens. 

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

Um [plano do serviço de aplicações](../../app-service/overview-hosting-plans.md) especifica o local, tamanho e funcionalidades da farm de servidores Web que aloja a aplicação.

Crie um plano do Serviço de Aplicações com o comando [az appservice plan create](/cli/azure/appservice/plan).

O exemplo seguinte cria um plano do Serviço de Aplicações com o nome `myAppServicePlan`, que utiliza o escalão de preços **Gratuito**.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free

```

## <a name="create-a-web-app"></a>Criar uma aplicação Web

A aplicação web fornece um espaço de alojamento para o código de aplicação de exemplo que é implementado a partir do repositório de exemplo do GitHub. Crie uma [aplicação Web](../../app-service/overview.md) no plano do `myAppServicePlan`Serviço de Aplicações com o comando[az webapp create](/cli/azure/webapp).  

No comando seguinte, substitua `<web_app>` por um nome único. Os carateres válidos são `a-z`, `0-9` e `-`. Se `<web_app>` não for exclusivo, obtém a mensagem de erro _O Website com o nome `<web_app>` já existe._ O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan

```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Implementar a aplicação de exemplo a partir do repositório do GitHub

# <a name="net"></a>[\.NET](#tab/dotnet)

O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web. Neste tutorial, vai implementar a aplicação Web a partir de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

O projeto de amostra contém uma aplicação [ASP.NET MVC.](https://www.asp.net/mvc) A aplicação aceita uma imagem, guarda-a numa conta de armazenamento e apresenta imagens a partir de um contentor de miniaturas. A aplicação web utiliza a [Microsoft.Azure.Storage,](/dotnet/api/overview/azure/storage) [Microsoft.Azure.Storage.Blob](/dotnet/api/microsoft.azure.storage.blob), e os espaços de nome Microsoft.Azure.Storage.Auth da biblioteca de clientes Azure Storage para interagir com o armazenamento Azure.

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup --branch master --manual-integration --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp

```

# <a name="nodejs-v2-sdk"></a>[Node.js V2 SDK](#tab/nodejs)
O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web. Neste tutorial, vai implementar a aplicação Web a partir de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source). 

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup --branch master --manual-integration --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node

```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)
O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web. Neste tutorial, vai implementar a aplicação Web a partir de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10). Configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source). 

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup --branch master --manual-integration --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10

```

---

## <a name="configure-web-app-settings"></a>Configurar as definições da aplicação Web

# <a name="net"></a>[\.NET](#tab/dotnet)

A aplicação Web de exemplo utiliza a [Biblioteca de Cliente de Armazenamento do Azure](/dotnet/api/overview/azure/storage) para pedir tokens de acesso, os quais são utilizados para carregar imagens. As credenciais da conta de armazenamento utilizadas pelo SDK de armazenamento são definidas nas definições da aplicação para a aplicação web. Adicione as definições da aplicação à aplicação implementada com o comando de definição de definições de definição de definições de definição de definições de definição de definição de definições de configurações de config da [az webapp.](/cli/azure/webapp/config/appsettings)

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup --settings AzureStorageConfig__AccountName=$blobStorageAccount AzureStorageConfig__ImageContainer=images AzureStorageConfig__ThumbnailContainer=thumbnails AzureStorageConfig__AccountKey=$blobStorageAccountKey

```

# <a name="nodejs-v2-sdk"></a>[Node.js V2 SDK](#tab/nodejs)

A aplicação Web de exemplo utiliza a [Biblioteca de Cliente de Armazenamento do Azure](https://docs.microsoft.com/javascript/api/azure-storage) para pedir tokens de acesso, os quais são utilizados para carregar imagens. As credenciais da conta de armazenamento utilizadas pelo SDK de armazenamento são definidas nas definições da aplicação para a aplicação web. Adicione as definições da aplicação à aplicação implementada com o comando de definição de definições de definição de definições de definição de definições de definição de definição de definições de configurações de config da [az webapp.](/cli/azure/webapp/config/appsettings)

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group resourceGroupName --name $blobStorageAccount --query connectionString --output tsv)

az webapp config appsettings set --name $webapp --resource-group myResourceGroup --settings AzureStorageConfig__ImageContainer=images AzureStorageConfig__ThumbnailContainer=thumbnails AzureStorageConfig__AccountName=$blobStorageAccount AzureStorageConfig__AccountKey=$blobStorageAccountKey AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString

```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

A aplicação Web de exemplo utiliza a [Biblioteca de Cliente de Armazenamento do Azure](https://github.com/Azure/azure-storage-js) para pedir tokens de acesso, os quais são utilizados para carregar imagens. As credenciais da conta de armazenamento utilizadas pelo SDK de armazenamento são definidas nas definições da aplicação para a aplicação web. Adicione as definições da aplicação à aplicação implementada com o comando de definição de definições de definição de definições de definição de definições de definição de definição de definições de configurações de config da [az webapp.](/cli/azure/webapp/config/appsettings)

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey

```

---

Depois de implementar e configurar a aplicação web, pode testar a funcionalidade de carregamento de imagens na aplicação.

## <a name="upload-an-image"></a>Carregar uma imagem

Para testar a aplicação Web, navegue para o URL da aplicação publicada. O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.

# <a name="net"></a>[\.NET](#tab/dotnet)

Selecione a região de **fotos do Upload** para selecionar e carregar um ficheiro ou arrastar um ficheiro para a região. A imagem desaparece se for carregada com êxito. A secção **Miniaturas Geradas** permanecerá vazia até a testarmos mais tarde neste tópico.

![Upload Fotos em .NET](media/storage-upload-process-images/figure1.png)

No código da amostra, a tarefa `UploadFiletoStorage` no ficheiro *Storagehelper.cs* é utilizada para carregar as imagens para o recipiente de *imagens* dentro da conta de armazenamento utilizando o método [UploadFromStreamAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) O exemplo de código seguinte contém a tarefa `UploadFiletoStorage`.

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

# <a name="nodejs-v2-sdk"></a>[Node.js V2 SDK](#tab/nodejs)

Selecione **Escolha O Ficheiro** para selecionar um ficheiro e, em seguida, clique em Enviar **imagem**. A secção **Miniaturas Geradas** permanecerá vazia até a testarmos mais tarde neste tópico. 

![Upload fotos em Node.js V2](media/storage-upload-process-images/upload-app-nodejs.png)

No código de exemplo, a rota `post` é responsável por carregar a imagem para um contentor de blobs. A rota utiliza os módulos para ajudar a processar o carregamento:

- [Multer](https://github.com/expressjs/multer) implementa a estratégia de upload para o manipulador de rotas.
- [o into-stream](https://github.com/sindresorhus/into-stream) converte o tampão num fluxo, conforme exigido por [createBlockBlobFromStream]. (https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html)

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

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

Selecione **Escolha O Ficheiro** para selecionar um ficheiro e, em seguida, clique em Enviar **imagem**. A secção **Miniaturas Geradas** permanecerá vazia até a testarmos mais tarde neste tópico. 

![Upload fotos em Node.js V10](media/storage-upload-process-images/upload-app-nodejs.png)

No código de exemplo, a rota `post` é responsável por carregar a imagem para um contentor de blobs. A rota utiliza os módulos para ajudar a processar o carregamento:

- [Multer](https://github.com/expressjs/multer) implementa a estratégia de upload para o manipulador de rotas.
- [o into-stream](https://github.com/sindresorhus/into-stream) converte o tampão num fluxo, conforme exigido pela [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html).

À medida que o ficheiro é enviado para a rota, o conteúdo do arquivo mantenha-se na memória até que o ficheiro é carregado para o contentor de Blobs.

> [!IMPORTANT]
> A carregar ficheiros grandes na memória, pode ter um efeito negativo no desempenho da sua aplicação web. Se pretender que os utilizadores para publicar ficheiros grandes, convém considerar a ficheiros no sistema de arquivos de servidor web de teste e, em seguida, agendamento carregamentos para o armazenamento de Blobs. Assim que os ficheiros estiverem no armazenamento de BLOBs, pode removê-lo do sistema de arquivos de servidor.

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

Inicie sessão no [portal do Azure](https://portal.azure.com). No menu à esquerda, selecione **Contas de armazenamento** e selecione o nome da conta de armazenamento. Selecione **Recipientes**e, em seguida, selecione o recipiente de **imagens.**

Verifique se a imagem é apresentada no contentor.

![Lista de imagens do Portal Azure](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Testar a visualização de miniaturas

Para testar a visualização de miniaturas, você irá enviar uma imagem para o recipiente de **miniaturas** para verificar se a aplicação pode ler o recipiente de **miniaturas.**

Inicie sessão no [portal do Azure](https://portal.azure.com). No menu à esquerda, selecione **Contas de armazenamento** e selecione o nome da conta de armazenamento. Selecione **recipientes**e, em seguida, selecione o recipiente de **miniaturas.** Selecione **Carregar** para abrir o painel **Carregar blob**.

Escolha um ficheiro com o apanhador de ficheiros e selecione **Upload**.

Navegue novamente para a aplicação para verificar se a imagem carregada no contentor de **miniaturas** está visível.

# <a name="net"></a>[\.NET](#tab/dotnet)
![.NET aplicativo de resizer de imagem com nova imagem exibida](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdk"></a>[Node.js V2 SDK](#tab/nodejs)
![App de resizer de imagem Node.js V2 com nova imagem exibida](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)
![App de resizer de imagem Node.js V10 com nova imagem exibida](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Na parte dois da série, automatizar a criação de imagem em miniatura para que precise esta imagem. No contentor de **miniaturas** no portal do Azure, selecione a imagem que carregou e selecione **Eliminar** para eliminar a imagem. 

Pode ativar a CDN para conteúdo de cache da sua conta de armazenamento do Azure. Para obter mais informações sobre como ativar o CDN com a sua conta de armazenamento Azure, consulte Integrar uma conta de [armazenamento Azure com o Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Passos seguintes

Na primeira parte da série, aprendeu a configurar uma aplicação web para interagir com o armazenamento.

Vá para a parte dois da série para saber como utilizar o Event Grid para acionar uma função do Azure para redimensionar uma imagem.

> [!div class="nextstepaction"]
> [Utilizar o Event Grid para acionar uma Função do Azure para redimensionar uma imagem carregada](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
