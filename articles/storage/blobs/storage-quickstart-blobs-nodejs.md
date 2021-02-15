---
title: 'Quickstart: Azure Blob storage library v12 - JavaScript'
description: Neste arranque rápido, aprende-se a usar a versão 12 da biblioteca de clientes de armazenamento Azure Blob para o JavaScript criar um recipiente e uma bolha no armazenamento blob (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 2884df3a9b5cb3954c1c5e57d923eff97809a745
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382179"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Quickstart: Gerir bolhas com JavaScript v12 SDK em Node.js

Neste arranque rápido, aprende-se a gerir as bolhas usando Node.js. As bolhas são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, streaming de meios e dados de arquivo. Você vai carregar, baixar e listar bolhas, e você vai criar e apagar recipientes.

Recursos adicionais:

* [Documentação de referência da API](/javascript/api/@azure/storage-blob)
* [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Pacote (Gestor de Pacotes de Nó)](https://www.npmjs.com/package/@azure/storage-blob)
* [Amostras](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma conta do Armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- [Node.js. ](https://nodejs.org/en/download/)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes de armazenamento Azure Blob v12 para JavaScript.

### <a name="create-the-project"></a>Criar o projeto

Crie uma aplicação JavaScript chamada *blob-quickstart-v12*.

1. Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para o projeto.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Mude para o *recém-criado diretório blob-quickstart-v12.*

    ```console
    cd blob-quickstart-v12
    ```

1. Crie um novo ficheiro de texto chamado *package.jsligado*. Este ficheiro define o projeto Node.js. Guarde este ficheiro no *diretório blob-quickstart-v12.* Aqui está o conteúdo do ficheiro:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Pode colocar o seu próprio nome no `author` campo, se quiser.

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda se inserem no diretório *blob-quickstart-v12,* instale a biblioteca de clientes de armazenamento Azure Blob para o pacote JavaScript utilizando o `npm install` comando. Este comando lê o *package.jsno* ficheiro e instala a biblioteca de clientes de armazenamento Azure Blob v12 para o pacote JavaScript e todas as bibliotecas de que depende.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Abra mais um novo ficheiro de texto no seu editor de código
1. Adicionar `require` chamadas para carregar módulos Azure e Node.js
1. Criar a estrutura para o programa, incluindo o manuseamento básico de exceções

    Aqui está o código:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const { v1: uuid} = require('uuid');

    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }

    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Guarde o novo ficheiro à medida *queblob-quickstart-v12.js* no *diretório blob-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento Azure Blob está otimizado para armazenar quantidades massivas de dados não estruturados. Os dados não estruturados são dados que não seguem uma definição ou um modelo de dados em particular, como por exemplo, texto ou dados binários. O armazenamento blob oferece três tipos de recursos:

* A conta de armazenamento
* Um recipiente na conta de armazenamento
* Uma bolha no recipiente

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento blob](./media/storage-blobs-introduction/blob1.png)

Utilize as seguintes classes JavaScript para interagir com estes recursos:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): A `BlobServiceClient` classe permite-lhe manipular os recursos de armazenamento do Azure e os recipientes blob.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): A `ContainerClient` classe permite-lhe manipular recipientes de armazenamento Azure e suas bolhas.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): A `BlobClient` classe permite-lhe manipular as bolhas de armazenamento Azure.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código de exemplo mostram-lhe como executar o seguinte com a biblioteca de clientes de armazenamento Azure Blob para JavaScript:

* [Obter a cadeia de ligação](#get-the-connection-string)
* [Criar um contentor](#create-a-container)
* [Carregar bolhas para um recipiente](#upload-blobs-to-a-container)
* [Listar os blobs num contentor](#list-the-blobs-in-a-container)
* [Transferir blobs](#download-blobs)
* [Eliminar um contentor](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código abaixo recupera a cadeia de ligação para a conta de armazenamento a partir da variável ambiente criada na secção de cadeia de [ligação de armazenamento.](#configure-your-storage-connection-string)

Adicione este código dentro da `main` função:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Criar um contentor

Decida o nome do novo recipiente. O código abaixo anexa um valor UUID ao nome do recipiente para garantir que é único.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) chamando o método [deConnectionString.](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) Em seguida, ligue para o método [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) para obter uma referência a um recipiente. Finalmente, [ligue](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) criar para realmente criar o recipiente na sua conta de armazenamento.

Adicione este código ao fim da `main` função:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Carregar bolhas para um recipiente

O seguinte corte de código:

1. Cria uma cadeia de texto para carregar para uma bolha.
1. Obtém uma referência a um objeto [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) chamando o método [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) no [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) a partir da secção [Criar um recipiente.](#create-a-container)
1. Envia os dados de cadeia de texto para a bolha, chamando o método de [upload.](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-)

Adicione este código ao fim da `main` função:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Enuncie as bolhas no recipiente chamando o método [listBlobsFlat.](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) Neste caso, apenas uma bolha foi adicionada ao recipiente, pelo que a operação de listagem devolve apenas uma bolha.

Adicione este código ao fim da `main` função:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Transferir blobs

Descarregue a bolha anteriormente criada chamando o método [de descarregamento.](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) O código de exemplo inclui uma função de ajudante chamada `streamToString` , que é usada para ler um fluxo Node.js legível em uma cadeia.

Adicione este código ao fim da `main` função:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Adicione esta função de ajudante *após* a `main` função:

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Eliminar um contentor

O código seguinte limpa os recursos que a app criou removendo todo o recipiente utilizando o método [de eliminação.](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) Também pode apagar os ficheiros locais, se quiser.

Adicione este código ao fim da `main` função:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Executar o código

Esta aplicação cria uma cadeia de texto e envia-a para o armazenamento blob. O exemplo enumera então a (s) blob(s) no recipiente, descarrega a bolha e exibe os dados descarregados.

A partir de um pedido de consola, navegue até ao diretório que contém o ficheiro *blob-quickstart-v12.js* e, em seguida, execute o seguinte `node` comando para executar a aplicação.

```console
node blob-quickstart-v12.js
```

A saída da app é semelhante ao seguinte exemplo:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Passe pelo código no seu depurador e verifique o seu [portal Azure](https://portal.azure.com) durante todo o processo. Verifique se o contentor está a ser criado. Pode abrir a bolha no interior do recipiente e ver o conteúdo.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a carregar, descarregar e listar bolhas usando JavaScript.

Para tutoriais, amostras, quickstarts e outra documentação, visite:

> [!div class="nextstepaction"]
> [Azure para o centro de desenvolvimento JavaScript](/azure/developer/javascript/)

* Para aprender a implementar uma aplicação web que utiliza o armazenamento do Azure Blob, consulte [Tutorial: Carrevar dados de imagem na nuvem com O Azure Storage](./storage-upload-process-images.md?preserve-view=true&tabs=javascript)
* Para ver as aplicações de amostra de armazenamento Blob, continue até a [biblioteca de clientes de armazenamento Azure Blob v12 Amostras JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
* Para saber mais, consulte a biblioteca de [clientes de armazenamento Azure Blob para JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).