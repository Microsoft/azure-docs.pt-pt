---
title: 'Início rápido: biblioteca de armazenamento de BLOBs do Azure V12-JavaScript'
description: Neste guia de início rápido, você aprende a usar a biblioteca de cliente de armazenamento de BLOBs do Azure versão 12 para JavaScript para criar um contêiner e um blob no armazenamento de BLOB (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: eabfefbf28b54e4a0a025698f8da48518e7df9bf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906457"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Quickstart: Gerir bolhas com JavaScript v12 SDK em Node.js

Neste arranque rápido, aprende-se a gerir bolhas usando o Node.js. As bolhas são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, meios de streaming e dados de arquivo. Você vai carregar, descarregar e listar bolhas, e você vai criar e apagar recipientes.

[Documentação de referência de API](/javascript/api/@azure/storage-blob) | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [pacote (Gerenciador de pacotes do nó)](https://www.npmjs.com/package/@azure/storage-blob) | [exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma conta do Armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

> [!NOTE]
> Para começar com a versão SDK anterior, consulte [Quickstart: Gerencie as bolhas com JavaScript v10 SDK em Node.js](storage-quickstart-blobs-nodejs-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Configurando

Esta seção orienta você pela preparação de um projeto para trabalhar com a biblioteca de cliente do armazenamento de BLOBs do Azure V12 para JavaScript.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo JavaScript chamado *blob-QuickStart-V12*.

1. Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para o projeto.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Alterne para o diretório *blob-QuickStart-V12* recém-criado.

    ```console
    cd blob-quickstart-v12
    ```

1. Crie um novo arquivo de texto chamado *Package. JSON*. Esse arquivo define o projeto Node. js. Salve esse arquivo no diretório *blob-QuickStart-V12* . Aqui está o conteúdo do arquivo:

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
    
    Você pode colocar seu próprio nome no campo `author`, se desejar.
   
### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório *blob-QuickStart-V12* , instale a biblioteca de cliente do armazenamento de BLOBs do Azure para o pacote JavaScript usando o comando `npm install`. Esse comando lê o arquivo *Package. JSON* e instala a biblioteca de cliente do armazenamento de BLOBs do Azure V12 para o pacote JavaScript e todas as bibliotecas das quais ela depende.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Configurar a estrutura do aplicativo

No diretório do projeto:

1. Abrir outro arquivo de texto novo no editor de código
1. Adicionar chamadas de `require` para carregar os módulos do Azure e do node. js
1. Criar a estrutura para o programa, incluindo o tratamento básico de exceções

    Aqui está o código:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Salve o novo arquivo como *blob-QuickStart-V12. js* no diretório *blob-QuickStart-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de BLOBs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não aderem a um determinado modelo ou definição de dados, tais como texto ou dados binários. O armazenamento de BLOBs oferece três tipos de recursos:

* A conta de armazenamento
* Um contêiner na conta de armazenamento
* Um blob no contêiner

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de BLOBs](./media/storage-blob-introduction/blob1.png)

Use as seguintes classes JavaScript para interagir com estes recursos:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): a classe `BlobServiceClient` permite que você manipule os recursos de armazenamento do Azure e contêineres de BLOB.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): a classe `ContainerClient` permite que você manipule contêineres de armazenamento do Azure e seus BLOBs.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): a classe `BlobClient` permite que você manipule os blobs de armazenamento do Azure.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código de exemplo mostram como executar o seguinte com a biblioteca de cliente de armazenamento de BLOBs do Azure para JavaScript:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar um contêiner](#create-a-container)
* [Carregar BLOBs em um contêiner](#upload-blobs-to-a-container)
* [Listar os BLOBs em um contêiner](#list-the-blobs-in-a-container)
* [Baixar BLOBs](#download-blobs)
* [Eliminar um contentor](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código a seguir recupera a cadeia de conexão para a conta de armazenamento da variável de ambiente criada na seção [configurar sua cadeia de conexão de armazenamento](#configure-your-storage-connection-string) .

Adicione este código dentro da função `main`:

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

Escolha um nome para o novo contêiner. O código a seguir acrescenta um valor UUID ao nome do contêiner para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) chamando o método [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) . Em seguida, chame o método [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) para obter uma referência a um contêiner. Por fim, chame [Create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) para realmente criar o contêiner em sua conta de armazenamento.

Adicione este código ao final da função `main`:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Carregar BLOBs em um contêiner

O seguinte trecho de código:

1. Cria uma cadeia de texto para carregar em um blob.
1. Obtém uma referência a um objeto [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) chamando o método [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) no [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) da seção [criar um contêiner](#create-a-container) .
1. Carrega os dados de cadeia de caracteres de texto para o blob chamando o método [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) .

Adicione este código ao final da função `main`:

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

Liste os BLOBs no contêiner chamando o método [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) . Nesse caso, apenas um blob foi adicionado ao contêiner, portanto, a operação de listagem retorna apenas um blob.

Adicione este código ao final da função `main`:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Transferir blobs

Baixe o blob criado anteriormente chamando o método de [Download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) . O código de exemplo inclui uma função de ajudante chamada `streamToString`, que é usada para ler um fluxo legível no Nó numa corda.

Adicione este código ao final da função `main`:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Adicione essa função auxiliar *após* a função `main`:

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

O código a seguir limpa os recursos que o aplicativo criou removendo todo o contêiner usando o método [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) . Você também pode excluir os arquivos locais, se desejar.

Adicione este código ao final da função `main`:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Executar o código

Esse aplicativo cria uma cadeia de texto e a carrega no armazenamento de BLOBs. Em seguida, o exemplo lista os BLOBs no contêiner, baixa o blob e exibe os dados baixados.

Em um prompt de console, navegue até o diretório que contém o arquivo *blob-QuickStart-V12.py* e execute o comando `node` a seguir para executar o aplicativo.

```console
node blob-quickstart-v12.js
```

A saída do aplicativo é semelhante ao exemplo a seguir:

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

Percorra o código em seu depurador e verifique sua [portal do Azure](https://portal.azure.com) durante todo o processo. Verifique se o contêiner está sendo criado. Você pode abrir o blob dentro do contêiner e exibir o conteúdo.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como carregar, baixar e listar BLOBs usando JavaScript.

Para tutoriais, amostras, quickstarts e outra documentação, visite:

> [!div class="nextstepaction"]
> [Documentação do Azure para JavaScript](/azure/javascript/)

* Para saber mais, confira [biblioteca de cliente de armazenamento de BLOBs do Azure para JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
* Para ver os aplicativos de exemplo de armazenamento de BLOBs, vá para [biblioteca de cliente do armazenamento de BLOBs do Azure exemplos de JavaScript V12](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)
