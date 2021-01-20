---
title: 'Quickstart: Azure Blob storage client library v10 for JavaScript'
description: Criar, carregar e apagar bolhas e recipientes em Node.js com a biblioteca de clientes Azure Storage v10 para JavaScript
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 2371c789b9e4a9fc70f4207fd8a634e419c97912
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599454"
---
# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-nodejs"></a>Quickstart: Gerir blobs com JavaScript v10 SDK em Node.js

Neste arranque rápido, aprende-se a gerir as bolhas usando Node.js. As bolhas são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, streaming de meios e dados de arquivo. Você vai carregar, baixar, listar e apagar bolhas, e você vai gerir contentores.

> [!NOTE]
> Este quickstart usa uma versão antiga da biblioteca de clientes de armazenamento Azure Blob. Para começar com a versão mais recente, consulte [Quickstart: Gerencie blobs com JavaScript v12 SDK em Node.js](storage-quickstart-blobs-nodejs.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma conta do Armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- [Node.js. ](https://nodejs.org/en/download/)

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

O [exemplo de aplicação](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git) deste início rápido é uma aplicação de consola Node.js simples. Para começar, clone o repositório para o seu computador com o seguinte comando:

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

Em seguida, mude de pasta para a aplicação:

```bash
cd azure-storage-js-v10-quickstart
```

Agora, abra a pasta no seu ambiente de edição de código preferido.

## <a name="configure-your-storage-credentials"></a>Configurar as credenciais de armazenamento

Antes de executar a aplicação, tem de indicar as credenciais de segurança da sua conta de armazenamento. O repositório de exemplo inclui um ficheiro denominado *. env.example*. Mude o nome deste ficheiro, removendo a extensão *.example*, o que resulta num ficheiro com o nome *.env*. Dentro do ficheiro *.env*, adicione os valores de nome de conta e de chave de acesso a seguir às chaves *AZURE_STORAGE_ACCOUNT_NAME* e *AZURE_STORAGE_ACCOUNT_ACCESS_KEY*.

## <a name="install-required-packages"></a>Instalar pacotes necessários

No diretório da aplicação, execute *npm install* para instalar os pacotes necessários para a aplicação.

```bash
npm install
```

## <a name="run-the-sample"></a>Executar o exemplo

Agora que as dependências estão instaladas, pode executar o exemplo ao emitir o seguinte comando:

```bash
npm start
```

O resultado da aplicação será semelhante ao exemplo seguinte:

```bash
Container "demo" is created
Containers:
 - container-one
 - container-two
 - demo
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Se estiver a utilizar uma nova conta de armazenamento para este arranque rápido, só poderá ver o recipiente *de demonstração* indicado na etiqueta "*Containers:*".

## <a name="understanding-the-code"></a>Compreender o código

O exemplo começa por importar um número de classes e funções do espaço de nomes de armazenamento de Blobs do Azure. Cada um dos itens importados é abordado no contexto à medida que são utilizados no exemplo.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

As credenciais são lidas a partir de variáveis de ambiente com base no contexto adequado.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

O módulo *dotenv* carrega variáveis de ambiente ao executar a aplicação localmente para depuração. Os valores são definidos num ficheiro denominado *.env* e carregados para o contexto da execução atual. Em produção, a configuração do servidor fornece estes valores e é por isso que este código é executado apenas quando o script *não* está a ser executado num ambiente de "produção".

O próximo bloco de módulos é importado para ajudar a fazer a interface com o sistema de ficheiros.

```javascript
const fs = require('fs');
const path = require('path');
```

O objetivo destes módulos é o seguinte: 

- *fs* é o módulo Node.js nativo utilizado para trabalhar com o sistema de ficheiros

- *path* é necessário para determinar o caminho absoluto do ficheiro, que é utilizado ao carregar um ficheiro para o Armazenamento de blobs

Em seguida, os valores das variáveis de ambiente são lidos e reservados em constantes.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
O próximo conjunto de constantes ajuda a revelar a intenção dos cálculos de tamanho de ficheiro durante as operações de carregamento.

```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```

Os pedidos feitos pela API podem ser definidos para intervalo após um determinado intervalo. A classe [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-legacy&preserve-view=true) é responsável por gerir a forma como os pedidos excedem o limite de tempo e a constante seguinte serve para definir os tempos limite utilizados neste exemplo.

```javascript
const ONE_MINUTE = 60 * 1000;
```

### <a name="calling-code"></a>Código de chamada

Para suportar a sintaxe de JavaScript *async/await*, todo o código de chamada é encapsulado num wrapper numa função chamada *execute*. Então *executar* é chamado e tratado como uma promessa.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```

Todo o código seguinte é executado dentro da função execute onde o comentário `// commands...` é colocado.

Em primeiro lugar, as variáveis relevantes são declaradas para atribuir nomes e conteúdo de exemplo, e para apontar para o ficheiro local, para carregar para o armazenamento de blobs.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

As credenciais da conta são utilizadas para criar um pipeline, que é responsável por gerir a forma como os pedidos são enviados para a API REST. Os pipelines são seguros para os threads e especificam a lógica para as políticas de repetição, registo, regras de desserialização de respostas HTTP e muito mais.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```

As seguintes classes são utilizadas neste bloco de código:

- A classe [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-legacy&preserve-view=true) é responsável pelo encapsulamento num wrapper das credenciais da conta de armazenamento, para fornecê-las a um pipeline de pedido.

- A classe [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-legacy&preserve-view=true) é responsável por criar um novo pipeline.

- A classe [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-legacy&preserve-view=true) modela um URL utilizado na API REST. As instâncias desta classe permitem-lhe executar ações como listar contentores e fornecer informações de contexto para gerar URLs de contentor.

A instância de *ServiceURL* é utilizado com as instâncias de [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-legacy&preserve-view=true) e [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-legacy&preserve-view=true) para gerir contentores e blobs na sua conta de armazenamento.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```

As variáveis *containerURL* e *blockBlobURL* são reutilizadas em todo o exemplo para agir sobre a conta de armazenamento. 

Neste momento, o contentor não existe na conta de armazenamento. A instância de *ContainerURL* representa um URL sobre o qual pode agir. Ao utilizar esta instância, pode criar e eliminar o contentor. A localização deste contentor equivale a uma localização como esta:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

A variável *blockBlobURL* é utilizada para gerir blobs individuais, permitindo-lhe carregar, transferir e eliminar conteúdo dos blobs. O URL aqui representado é semelhante a esta localização:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```

Tal como acontece com o contentor, o blob de blocos ainda não existe. A variável *blockBlobURL* é utilizada mais tarde para criar o blob através do carregamento de conteúdo.

### <a name="using-the-aborter-class"></a>Utilizar a classe Aborter

Os pedidos feitos pela API podem ser definidos para intervalo após um determinado intervalo. A classe *Aborter* é responsável pela gestão da forma como os pedidos são cronometrado. O código que se segue cria um contexto em que um conjunto de pedidos é dado 30 minutos para executar.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```

Os Aborters dão-lhe controlo sobre os pedidos ao permitir que:

- designe a quantidade de tempo especificada para um lote de pedidos
- designe durante quanto tempo um pedido individual tem de ser executado no lote
- cancele pedidos
- utilize o membro estático *Aborter.None* para impedir que os pedidos excedam o tempo limite em conjunto

### <a name="create-a-container"></a>Criar um contentor

Para criar um contentor, é utilizado o método *create* do *ContainerURL*.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```

Como o nome do contentor é definido ao chamar *ContainerURL.fromServiceURL(serviceURL, containerName)*, só é preciso chamar o método *create* para criar o contentor.

### <a name="show-container-names"></a>Mostrar nomes de contentores

As contas podem armazenar um grande número de contentores. O código seguinte demonstra como listar os contentores de uma forma segmentada, o que lhe permite percorrer um grande número de contentores. À função *showContainerNames* são passadas instâncias de *ServiceURL* e *Aborter*.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```

A função *showContainerNames* utiliza o método *listContainersSegment* para solicitar lotes de nomes de contentor da conta de armazenamento.

```javascript
async function showContainerNames(aborter, serviceURL) {
    let marker = undefined;

    do {
        const listContainersResponse = await serviceURL.listContainersSegment(aborter, marker);
        marker = listContainersResponse.nextMarker;
        for(let container of listContainersResponse.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```

Quando a resposta é devolvida, os *containerItems* são iterados para registar o nome na consola. 

### <a name="upload-text"></a>Carregar texto

Para carregar texto para o blob, utilize o método *upload*.

```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```

Aqui, o texto e o respetivo comprimento são transmitidos para o método.

### <a name="upload-a-local-file"></a>Carregar um ficheiro local

Para carregar um ficheiro local para o contentor, precisa de um URL de contentor e do caminho para o ficheiro.

```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```

A função *uploadLocalFile* chama a função *uploadFileToBlockBlob*, que utiliza o caminho do ficheiro e uma instância do destino do blob de blocos como argumentos.

```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```

### <a name="upload-a-stream"></a>Carregar um fluxo

O carregamento de fluxos também é suportado. Este exemplo abre um ficheiro local como um fluxo para passar para o método de carregamento.

```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```

A função *uploadStream* chama *uploadStreamToBlockBlob* para carregar o fluxo para o contentor de armazenamento.

```javascript
async function uploadStream(aborter, containerURL, filePath) {
    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```

Durante um carregamento, *uploadStreamToBlockBlob* aloca memórias intermédias para dados da cache do fluxo, no caso de ser necessária uma repetição. O valor de *maxBuffers* designa o número máximo de memórias intermédias que são utilizadas à medida que cada memória intermédia cria um pedido de carregamento em separado. Idealmente, um maior número de memórias intermédias equivale a velocidades mais elevadas, mas implica a utilização de mais memória. A velocidade de carregamento estagna quando o número de memórias intermédias é tão elevado que o estrangulamento transita para a rede ou disco, em vez do cliente.

### <a name="show-blob-names"></a>Mostrar nomes de blobs

Da mesma forma que as contas podem conter muitos contentores, cada contentor pode potencialmente conter uma grande quantidade de blobs. O acesso a cada blob num contentor está disponível através de uma instância da classe *ContainerURL*.

```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```

A função *showBlobNames* chama *listBlobFlatSegment* para solicitar lotes de blobs ao contentor.

```javascript
async function showBlobNames(aborter, containerURL) {
    let marker = undefined;

    do {
        const listBlobsResponse = await containerURL.listBlobFlatSegment(Aborter.none, marker);
        marker = listBlobsResponse.nextMarker;
        for (const blob of listBlobsResponse.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```

### <a name="download-a-blob"></a>Transferir um blob

Depois de criar um blob, pode transferir o conteúdo através do método *download*.

```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = await streamToString(downloadResponse.readableStreamBody);
console.log(`Downloaded blob content: "${downloadedContent}"`);
```

A resposta é devolvida como um fluxo. Neste exemplo, o fluxo é convertido para uma corda utilizando a seguinte função de ajudante *streamToString.*

```javascript
// A helper method used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", data => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
}
```

### <a name="delete-a-blob"></a>Eliminar um blob

O método *delete* de uma instância *BlockBlobURL* elimina um blob do contentor.

```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```

### <a name="delete-a-container"></a>Eliminar um contentor

O método *delete* de uma instância *ContainerURL* elimina um contentor da conta de armazenamento.

```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Limpar os recursos

Todos os dados escritos na conta de armazenamento são eliminados automaticamente no fim do código de exemplo. 

## <a name="next-steps"></a>Passos seguintes

Este início rápido demonstra como gerir blobs e contentores no armazenamento de Blobs do Azure com Node.js. Para saber mais sobre a utilização deste SDK, consulte o repositório do GitHub.

> [!div class="nextstepaction"]
> [Azure Storage v10 SDK para repositório](https://github.com/Azure/azure-storage-js) 
>  JavaScript [Referência Azure Storage JavaScript API](/javascript/api/overview/azure/storage-overview)
