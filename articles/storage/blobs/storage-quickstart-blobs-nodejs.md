---
title: 'Início rápido: biblioteca de armazenamento de BLOBs do Azure V12-JavaScript'
description: Neste guia de início rápido, você aprende a usar a biblioteca de cliente de armazenamento de BLOBs do Azure versão 12 para JavaScript para criar um contêiner e um blob no armazenamento de BLOB (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 90ef1be4366205b1f655a0b93619a897d6d1f25d
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929485"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-javascript"></a>Início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure V12 para JavaScript

Introdução à biblioteca de cliente do armazenamento de BLOBs do Azure V12 para JavaScript. O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. Siga as etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O Armazenamento de blobs está otimizado para armazenar quantidades em grande escala de dados não estruturados.

> [!NOTE]
> Para começar a usar a versão anterior do SDK, consulte [início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure para JavaScript](storage-quickstart-blobs-nodejs-v10.md).

Use a biblioteca de cliente do armazenamento de BLOBs do Azure V12 para JavaScript para:

* Criar um contentor
* Carregar um blob no armazenamento do Azure
* Listar todos os BLOBs em um contêiner
* Baixe o blob em seu computador local
* Eliminar um contentor

[Documentação de referência de API](/javascript/api/@azure/storage-blob) | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [pacote (Gerenciador de pacotes do nó)](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | [exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Node. js](https://nodejs.org/en/download/) atual para seu sistema operacional.

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
1. Crie a estrutura para o programa, incluindo manipulação de exceção muito básica

    Este é o código:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - Javascript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Salve o novo arquivo como *blob-QuickStart-V12. js* no diretório *blob-QuickStart-V12* .

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar as credenciais do Portal do Azure

Quando o aplicativo de exemplo faz uma solicitação para o armazenamento do Azure, ele deve ser autorizado. Para autorizar uma solicitação, adicione as credenciais da conta de armazenamento ao aplicativo como uma cadeia de conexão. Veja as credenciais da conta de armazenamento através dos seguintes passos:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Localize a sua conta de armazenamento.
3. Na secção **Definições** da descrição geral da conta de armazenamento, selecione **Chaves de acesso**. Aqui, pode ver as chaves de acesso da conta e a cadeia de ligação completa para cada chave.
4. Encontre o valor da **Cadeia de ligação** em **key1** e selecione o botão **Copiar** para copiar a cadeia de ligação. Irá adicionar o valor da cadeia de ligação para uma variável de ambiente no próximo passo.

    ![Captura de ecrã que mostra como copiar uma cadeia de ligação do portal do Azure](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Após ter copiado a cadeia de ligação, escreva-a numa nova variável de ambiente no computador local que está a executar a aplicação. Para definir a variável de ambiente, abra uma janela da consola e siga as instruções relevantes para o seu sistema operativo. Substitua `<yourconnectionstring>` pela cadeia de conexão real.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente no Windows, você deve iniciar uma nova instância da janela de comando.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Reiniciar programas

Depois de adicionar a variável de ambiente, reinicie os programas em execução que precisarão ler a variável de ambiente. Por exemplo, reinicie o seu ambiente ou editor de desenvolvimento antes de continuar.

## <a name="object-model"></a>Modelo de objeto

O armazenamento de BLOBs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não aderem a um modelo ou definição de dados específico, como texto ou dados binários. O armazenamento de BLOBs oferece três tipos de recursos:

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
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### <a name="create-a-container"></a>Criar um contentor

Escolha um nome para o novo contêiner. O código a seguir acrescenta um valor UUID ao nome do contêiner para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) chamando o método [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--newpipelineoptions-) . Em seguida, chame o método [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) para obter uma referência a um contêiner. Por fim, chame [Create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) para realmente criar o contêiner em sua conta de armazenamento.

Adicione este código ao final da função `main`:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await new BlobServiceClient.fromConnectionString(CONNECT_STR);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
await containerClient.create();
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

console.log('\nUploading to Azure Storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
await blockBlobClient.upload(data, data.length);
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

Baixe o blob criado anteriormente chamando o método de [Download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) . O código de exemplo inclui uma função auxiliar chamada `streamToString` que é usada para ler um fluxo legível do node. js em uma cadeia de caracteres.

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
await containerClient.delete();
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

Percorra o código em seu depurador e verifique sua portal do Azure durante todo o processo. Verifique se o contêiner está sendo criado. Você pode abrir o blob dentro do contêiner e exibir o conteúdo.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como carregar, baixar e listar BLOBs usando JavaScript.

Para ver os aplicativos de exemplo de armazenamento de BLOBs, continue em:

> [!div class="nextstepaction"]
> [Exemplos de JavaScript do SDK do armazenamento de BLOBs do Azure V12](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

* Para saber mais, consulte o [SDK do Azure para JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md).
* Para obter tutoriais, exemplos, início rápido e outras documentações, visite [a documentação do SDK do Azure para JavaScript](/azure/javascript/).
