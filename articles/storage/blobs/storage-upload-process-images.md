---
title: Carregar dados de imagem na cloud com o Armazenamento do Azure | Microsoft Docs
description: Utilize o armazenamento Azure Blob com uma aplicação web para armazenar dados de aplicações
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.openlocfilehash: e2ee959fb6fc7e8454919c71cfa20e2bb9055dfb
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393853"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Tutorial: Enviar dados de imagem na nuvem com armazenamento azure

Este tutorial é a primeira parte de uma série. Neste tutorial, você aprenderá a implementar uma aplicação web que usa a biblioteca de clientes de armazenamento Azure Blob para fazer upload de imagens para uma conta de armazenamento. Quando terminar, terá uma aplicação web que armazena e exibe imagens do armazenamento do Azure.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)
![App de resizer de imagem em .NET](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)
![Aplicativo de resizer de imagem no Node.js V10](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Criar um contentor e definir permissões
> * Obter uma chave de acesso
> * Implementar uma aplicação web para o Azure
> * Configurar as definições da aplicação
> * Interaja com a aplicação web

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure. Crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar o CLI localmente, este tutorial requer que execute a versão Azure CLI 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.  

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

A amostra envia imagens para um recipiente de bolhas numa conta de armazenamento Azure. Uma conta de armazenamento fornece um espaço de nomes exclusivo para armazenar e aceder aos objetos de dados de armazenamento do Azure. Utilize o comando [az storage account create](/cli/azure/storage/account) para criar uma conta de armazenamento no grupo de recursos que criou.

> [!IMPORTANT]
> Na parte 2 do tutorial, você usa azure Event Grid com armazenamento Blob. Certifique-se de criar a sua conta de armazenamento numa região do Azure que suporta a Grelha de Eventos. Para obter uma lista de regiões apoiadas, consulte [os produtos Azure por região.](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)

No comando seguinte, substitua o seu nome globalmente único para `<blob_storage_account>` a conta de armazenamento Blob onde vê o espaço reservado.

```azurecli-interactive
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>Criar recipientes de armazenamento Blob

A aplicação utiliza dois contentores na conta de armazenamento de Blobs. Os recipientes são semelhantes a pastas e lojas de bolhas. O contentor de *imagens* é para onde a aplicação carrega imagens com máxima resolução. Numa secção mais adiante da série, uma aplicação de função do Azure carrega as miniaturas de imagem dimensionadas para o contentor de *miniaturas*.

Obtenha a chave da conta de armazenamento com o comando [az storage account keys list](/cli/azure/storage/account/keys). Em seguida, utilize esta chave para criar dois recipientes com o recipiente de [armazenamento az criar](/cli/azure/storage/container) comando.

O acesso público do contentor `off`de *imagens* está definido para . O acesso público do contentor das `container` *miniaturas* está definido para . A `container` definição de acesso público permite que os utilizadores que visitam a página web vejam as miniaturas.

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey
```

Tome nota do nome e da chave da sua conta de armazenamento Blob. A aplicação da amostra utiliza estas definições para se ligar à conta de armazenamento para fazer o upload das imagens. 

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

Um [plano do serviço de aplicações](../../app-service/overview-hosting-plans.md) especifica o local, tamanho e funcionalidades da farm de servidores Web que aloja a aplicação.

Crie um plano do Serviço de Aplicações com o comando [az appservice plan create](/cli/azure/appservice/plan).

O exemplo seguinte cria um plano do Serviço de Aplicações com o nome `myAppServicePlan`, que utiliza o escalão de preços **Gratuito**.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Criar uma aplicação Web

A aplicação web fornece um espaço de hospedagem para o código de aplicação de amostra que é implementado a partir do repositório de amostras GitHub. Crie uma [aplicação Web](../../app-service/overview.md) no plano do `myAppServicePlan`Serviço de Aplicações com o comando[az webapp create](/cli/azure/webapp).  

No comando seguinte, `<web_app>` substitua-o por um nome único. Os carateres válidos são `a-z`, `0-9` e `-`. Se `<web_app>` não for exclusivo, obtém a mensagem de erro *O Website com o nome `<web_app>` já existe.* O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Implementar a aplicação de exemplo a partir do repositório do GitHub

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web. Neste tutorial, vai implementar a aplicação Web a partir de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

O projeto de amostra contém uma aplicação [ASP.NET MVC.](https://www.asp.net/mvc) A aplicação aceita uma imagem, guarda-a para uma conta de armazenamento e exibe imagens de um recipiente de miniaturas. A aplicação web utiliza o [Azure.Storage,](/dotnet/api/azure.storage) [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs), e [Azure.Storage.Blobs.Models](/dotnet/api/azure.storage.blobs.models) nomeespaços para interagir com o serviço de Armazenamento Azure.

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)
O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web. Neste tutorial, vai implementar a aplicação Web a partir de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10). Configure a implementação do GitHub para a aplicação Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10
```

---

## <a name="configure-web-app-settings"></a>Configurar as definições da aplicação Web

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

A aplicação web da amostra utiliza as APIs de [Armazenamento Azure para .NET](/dotnet/api/overview/azure/storage) para fazer upload de imagens. As credenciais de conta de armazenamento estão definidas nas definições da aplicação para a aplicação web. Adicione as definições da aplicação à aplicação implementada com o comando de definição de definições de definição de definições de definição de definições de definição de definição de definições de configurações de config da [az webapp.](/cli/azure/webapp/config/appsettings)

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

A aplicação Web de exemplo utiliza a [Biblioteca de Cliente de Armazenamento do Azure](https://github.com/Azure/azure-storage-js) para pedir tokens de acesso, os quais são utilizados para carregar imagens. As credenciais de conta de armazenamento utilizadas pelo SDK de armazenamento estão definidas nas definições da aplicação para a aplicação web. Adicione as definições da aplicação à aplicação implementada com o comando de definição de definições de definição de definições de definição de definições de definição de definição de definições de configurações de config da [az webapp.](/cli/azure/webapp/config/appsettings)

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Depois de implementar e configurar a aplicação web, pode testar a funcionalidade de upload de imagem na aplicação.

## <a name="upload-an-image"></a>Carregar uma imagem

Para testar a aplicação Web, navegue para o URL da aplicação publicada. O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Selecione a região de **fotos do Upload** para especificar e fazer upload de um ficheiro, ou arraste um ficheiro para a região. A imagem desaparece se for carregada com êxito. A secção **Miniaturas Geradas** permanecerá vazia até a testarmos mais tarde neste tópico.

![Upload Fotos em .NET](media/storage-upload-process-images/figure1.png)

No código da `UploadFileToStorage` amostra, a tarefa no ficheiro *Storagehelper.cs* é utilizada para carregar as imagens para o recipiente de *imagens* dentro da conta de armazenamento utilizando o método [UploadAsync.](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) O exemplo de código seguinte contém a tarefa `UploadFileToStorage`.

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

| Classe    | Método   |
|----------|----------|
| [Uri](/dotnet/api/system.uri) | [Construtor uri](/dotnet/api/system.uri.-ctor) |
| [ArmazenamentoSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [ArmazenamentoSharedKeyCredential (String, String) construor](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

Selecione **Escolha O Ficheiro** para selecionar um ficheiro e, em seguida, clique em Enviar **imagem**. A secção **Miniaturas Geradas** permanecerá vazia até a testarmos mais tarde neste tópico. 

![Upload fotos em Node.js V10](media/storage-upload-process-images/upload-app-nodejs.png)

No código de exemplo, a rota `post` é responsável por carregar a imagem para um contentor de blobs. A rota utiliza os módulos para ajudar a processar o carregamento:

- [Multer](https://github.com/expressjs/multer) implementa a estratégia de upload para o manipulador de rotas.
- [o into-stream](https://github.com/sindresorhus/into-stream) converte o tampão num fluxo, conforme exigido pela [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html).

À medida que o ficheiro é enviado para a rota, o conteúdo do ficheiro permanece na memória até que o ficheiro seja enviado para o recipiente blob.

> [!IMPORTANT]
> Carregar ficheiros grandes na memória pode ter um efeito negativo no desempenho da sua aplicação web. Se espera que os utilizadores publiquem ficheiros grandes, é melhor considerar a realização de ficheiros no sistema de ficheiros do servidor web e, em seguida, agendar uploads para o armazenamento blob. Uma vez que os ficheiros estejam no armazenamento blob, pode removê-los do sistema de ficheiros do servidor.

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

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)
![.NET aplicativo de resizer de imagem com nova imagem exibida](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)
![App de resizer de imagem Node.js V10 com nova imagem exibida](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Na segunda parte da série, automatizas a criação de imagem de miniatura para que não precises desta imagem. No contentor de **miniaturas** no portal do Azure, selecione a imagem que carregou e selecione **Eliminar** para eliminar a imagem. 

Pode ativar a Rede de Entrega de Conteúdos (CDN) para cache conteúdo da sua conta de armazenamento Azure. Para obter mais informações sobre como ativar o CDN com a sua conta de armazenamento Azure, consulte Integrar uma conta de [armazenamento Azure com o Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Passos seguintes

Na primeira parte da série, aprendeu a configurar uma aplicação web para interagir com o armazenamento.

Vá à segunda parte da série para aprender sobre a utilização da Rede de Eventos para desencadear uma função Azure para redimensionar uma imagem.

> [!div class="nextstepaction"]
> [Utilizar o Event Grid para acionar uma Função do Azure para redimensionar uma imagem carregada](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
