---
title: Carregar dados de imagem na cloud com o Armazenamento do Azure | Microsoft Docs
description: Utilize o armazenamento do Azure Blob com aplicações web para armazenar dados de aplicações numa conta de armazenamento. Este tutorial cria uma aplicação web que armazena e exibe imagens do armazenamento do Azure.
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: c20c78cb3c946c666b1640ccac6f86c9b52387ea
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843881"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Tutorial: Carregar dados de imagem na nuvem com armazenamento Azure

Este tutorial é a primeira parte de uma série. Neste tutorial, você vai aprender a implementar uma aplicação web. A aplicação web utiliza a biblioteca de clientes de armazenamento Azure Blob para fazer o upload de imagens para uma conta de armazenamento. Quando terminar, terá uma aplicação web que armazena e exibe imagens do armazenamento do Azure.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

![App de resizer de imagem em .NET](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

![App de resizer de imagem em JavaScript](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Na primeira parte da série, saiba como:

> [!div class="checklist"]

> * Criar uma conta de armazenamento
> * Criar um contentor e definir permissões
> * Obter uma chave de acesso
> * Implementar uma aplicação Web no Azure
> * Configurar as definições da aplicação
> * Interaja com a aplicação web

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure. Crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar o CLI localmente, executar a versão 2.0.4 ou posterior do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.  

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup`.

```bash
az group create --name myResourceGroup --location southeastasia
```

```powershell
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

A amostra envia imagens para um recipiente de bolhas numa conta de armazenamento Azure. Uma conta de armazenamento fornece um espaço de nomes exclusivo para armazenar e aceder aos objetos de dados de armazenamento do Azure. Utilize o comando [az storage account create](/cli/azure/storage/account) para criar uma conta de armazenamento no grupo de recursos que criou.

> [!IMPORTANT]
> Na parte 2 do tutorial, você usa Azure Event Grid com armazenamento Blob. Certifique-se de criar a sua conta de armazenamento numa região Azure que suporta a Grade de Eventos. Para obter uma lista de regiões apoiadas, consulte [os produtos Azure por região.](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)

No seguinte comando, substitua o seu próprio nome globalmente único para a conta de armazenamento Blob onde vê o `<blob_storage_account>` espaço reservado.

```bash
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

```powershell
$blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia `
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>Criar recipientes de armazenamento Blob

A aplicação utiliza dois contentores na conta de armazenamento de Blobs. Os recipientes são semelhantes às pastas e armazenam bolhas. O contentor de *imagens* é para onde a aplicação carrega imagens com máxima resolução. Numa secção mais adiante da série, uma aplicação de função do Azure carrega as miniaturas de imagem dimensionadas para o contentor de *miniaturas*.

Obtenha a chave da conta de armazenamento com o comando [az storage account keys list](/cli/azure/storage/account/keys). Em seguida, utilize esta chave para criar dois recipientes com o [recipiente de armazenamento az criar](/cli/azure/storage/container) comando.

O acesso público do contentor de *imagens* está definido para `off` . O acesso público do contentor das *miniaturas* está definido para `container` . A `container` definição de acesso público permite que os utilizadores que visitam a página web vejam as miniaturas.

```bash
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey --public-access container
```

Tome nota do nome e chave da sua conta de armazenamento Blob. A aplicação da amostra utiliza estas definições para ligar à conta de armazenamento para fazer o upload das imagens. 

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

Um [plano do serviço de aplicações](../../app-service/overview-hosting-plans.md) especifica o local, tamanho e funcionalidades da farm de servidores Web que aloja a aplicação.

Crie um plano do Serviço de Aplicações com o comando [az appservice plan create](/cli/azure/appservice/plan).

O exemplo seguinte cria um plano do Serviço de Aplicações com o nome `myAppServicePlan`, que utiliza o escalão de preços **Gratuito**.

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

```powershell
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Criar uma aplicação Web

A aplicação web fornece um espaço de hospedagem para o código de aplicação da amostra que é implantado a partir do repositório de amostras GitHub. Crie uma [aplicação Web](../../app-service/overview.md) no plano do `myAppServicePlan`Serviço de Aplicações com o comando[az webapp create](/cli/azure/webapp).  

No comando seguinte, `<web_app>` substitua-o por um nome único. Os carateres válidos são `a-z`, `0-9` e `-`. Se `<web_app>` não for único, obtém-se a mensagem de erro: *O website com nome próprio já `<web_app>` existe.* O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.  

```bash
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

```powershell
$webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Implementar a aplicação de exemplo a partir do repositório do GitHub

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web. Neste tutorial, vai implementar a aplicação Web a partir de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

O projeto da amostra contém uma [aplicação de MVC ASP.NET.](https://www.asp.net/mvc) A aplicação aceita uma imagem, guarda-a para uma conta de armazenamento e exibe imagens de um recipiente de miniaturas. A aplicação web utiliza o [Azure.Storage](/dotnet/api/azure.storage), [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs)e [Azure.Storage.Blobs.Models](/dotnet/api/azure.storage.blobs.models) para interagir com o serviço de armazenamento Azure.

```bash
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web. Neste tutorial, vai implementar a aplicação Web a partir de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs). Configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

```bash
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

---

## <a name="configure-web-app-settings"></a>Configurar as definições da aplicação Web

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

A aplicação web de amostra utiliza as [APIs de Armazenamento Azure para .NET](/dotnet/api/overview/azure/storage) para fazer upload de imagens. As credenciais de conta de armazenamento são definidas nas definições da aplicação para a aplicação web. Adicione as definições de aplicações à aplicação implementada com o comando de configuração de [appsettings az webapp.](/cli/azure/webapp/config/appsettings)

```bash
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AzureStorageConfig__AccountName=$blobStorageAccount `
    AzureStorageConfig__ImageContainer=images `
    AzureStorageConfig__ThumbnailContainer=thumbnails `
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

A aplicação web de amostras utiliza a [biblioteca de clientes Azure Storage para o JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage) para fazer upload de imagens. As credenciais de conta de armazenamento estão definidas nas definições da aplicação para a aplicação web. Adicione as definições de aplicações à aplicação implementada com o comando de configuração de [appsettings az webapp.](/cli/azure/webapp/config/appsettings)

```bash
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Depois de implementar e configurar a aplicação web, pode testar a funcionalidade de upload de imagem na aplicação.

## <a name="upload-an-image"></a>Carregar uma imagem

Para testar a aplicação Web, navegue para o URL da aplicação publicada. O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Selecione a região de **fotos upload** para especificar e carregar um ficheiro, ou arraste um ficheiro para a região. A imagem desaparece se for carregada com êxito. A secção **De Miniaturas Geradas** permanecerá vazia até testá-la mais tarde neste tutorial.

![Faça upload de fotos em .NET](media/storage-upload-process-images/figure1.png)

No código de amostra, a `UploadFileToStorage` tarefa no ficheiro *Storagehelper.cs* é utilizada para enviar as imagens para o contentor de *imagens* dentro da conta de armazenamento utilizando o método [UploadAsync.](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) O exemplo de código seguinte contém a tarefa `UploadFileToStorage`.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

As classes e métodos seguintes são utilizados na tarefa anterior:

| Classe | Método |
|-------|--------|
| [Uri](/dotnet/api/system.uri) | [Construtor uri](/dotnet/api/system.uri.-ctor) |
| [ArmazenamentoSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [Construtor de StorageSharedKeyCredential (String, String)](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

**Selecione Escolher Ficheiro** para selecionar um ficheiro e, em seguida, clique em Upload **Image**. A secção **De Miniaturas Geradas** permanecerá vazia até testá-la mais tarde neste tutorial.

![Faça upload de fotos em Node.js](media/storage-upload-process-images/upload-app-nodejs.png)

No código de exemplo, a rota `post` é responsável por carregar a imagem para um contentor de blobs. A rota utiliza os módulos para ajudar a processar o carregamento:

- [multer](https://github.com/expressjs/multer) implementa a estratégia de upload para o manipulador de rotas.
- [em fluxo](https://github.com/sindresorhus/into-stream) converte o tampão num fluxo conforme exigido pelo [uploadStream](/javascript/api/%40azure/storage-blob/blockblobclient#uploadstream-readable--number--number--blockblobuploadstreamoptions-).

À medida que o ficheiro é enviado para a rota, o conteúdo do ficheiro permanece na memória até que o ficheiro seja enviado para o recipiente blob.

> [!IMPORTANT]
> O carregamento de ficheiros grandes na memória pode ter um efeito negativo no desempenho da sua aplicação web. Se espera que os utilizadores publiquem ficheiros grandes, pode considerar a paragem de ficheiros no sistema de ficheiros do servidor web e, em seguida, agendar uploads para o armazenamento blob. Uma vez que os ficheiros estejam no armazenamento blob, pode removê-los do sistema de ficheiros do servidor.

```javascript
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}

const {
  BlobServiceClient,
  StorageSharedKeyCredential,
  newPipeline
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const containerName1 = 'thumbnails';
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName2 = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };

const sharedKeyCredential = new StorageSharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = newPipeline(sharedKeyCredential);

const blobServiceClient = new BlobServiceClient(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, '');
  return `${identifier}-${originalName}`;
};

router.get('/', async (req, res, next) => {

  let viewData;

  try {
    const containerClient = blobServiceClient.getContainerClient(containerName1);
    const listBlobsResponse = await containerClient.listBlobFlatSegment();

    for await (const blob of listBlobsResponse.segment.blobItems) {
      console.log(`Blob: ${blob.name}`);
    }

    viewData = {
      title: 'Home',
      viewName: 'index',
      accountName: process.env.AZURE_STORAGE_ACCOUNT_NAME,
      containerName: containerName1
    };

    if (listBlobsResponse.segment.blobItems.length) {
      viewData.thumbnails = listBlobsResponse.segment.blobItems;
    }
  } catch (err) {
    viewData = {
      title: 'Error',
      viewName: 'error',
      message: 'There was an error contacting the blob storage container.',
      error: err
    };
    res.status(500);
  } finally {
    res.render(viewData.viewName, viewData);
  }
});

router.post('/', uploadStrategy, async (req, res) => {
  const blobName = getBlobName(req.file.originalname);
  const stream = getStream(req.file.buffer);
  const containerClient = blobServiceClient.getContainerClient(containerName2);;
  const blockBlobClient = containerClient.getBlockBlobClient(blobName);

  try {
    await blockBlobClient.uploadStream(stream,
      uploadOptions.bufferSize, uploadOptions.maxBuffers,
      { blobHTTPHeaders: { blobContentType: "image/jpeg" } });
    res.render('success', { message: 'File uploaded to Azure Blob storage.' });
  } catch (err) {
    res.render('error', { message: err.message });
  }
});

module.exports = router;
```

---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Verifique se a imagem é apresentada na conta de armazenamento

Inicie sessão no [portal do Azure](https://portal.azure.com). No menu à esquerda, selecione **Contas de armazenamento** e selecione o nome da conta de armazenamento. Selecione **Recipientes** e, em seguida, selecione o recipiente de **imagens.**

Verifique se a imagem é apresentada no contentor.

![Listagem do portal Azure do contentor de imagens](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Testar a visualização de miniaturas

Para testar a visualização das **miniaturas,** você irá enviar uma imagem para o recipiente das miniaturas para verificar se a aplicação pode ler o recipiente **das miniaturas.**

Inicie sessão no [portal do Azure](https://portal.azure.com). No menu à esquerda, selecione **Contas de armazenamento** e selecione o nome da conta de armazenamento. Selecione **Recipientes** e, em seguida, selecione o recipiente **das miniaturas.** Selecione **Carregar** para abrir o painel **Carregar blob**.

Escolha um ficheiro com o selecionador de ficheiros e selecione **Upload**.

Navegue novamente para a aplicação para verificar se a imagem carregada no contentor de **miniaturas** está visível.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

![.NET app de resizer de imagem com nova imagem exibida](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

![Node.js app de resizer de imagem com nova imagem exibida](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Na segunda parte da série, automatizas a criação de imagens de miniaturas para que não precises desta imagem. No recipiente **das miniaturas,** selecione a imagem que carregou e selecione **Eliminar** para remover a imagem.

Pode ativar a Rede de Entrega de Conteúdos (CDN) para cache de conteúdo a partir da sua conta de armazenamento Azure. Para obter mais informações, consulte [Integrar uma conta de armazenamento Azure com a Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Passos seguintes

Na primeira parte da série, aprendeu a configurar uma aplicação web para interagir com o armazenamento.

Vá à segunda parte da série para aprender a usar a Grade de Eventos para desencadear uma função Azure para redimensionar uma imagem.

> [!div class="nextstepaction"]
> [Utilizar o Event Grid para acionar uma Função do Azure para redimensionar uma imagem carregada](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
