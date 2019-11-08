---
title: 'Início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure v2 para JavaScript'
description: Crie uma conta de armazenamento e um contentor no armazenamento de objetos (blobs). Em seguida, use a biblioteca de cliente de armazenamento do Azure para node. js v2 para carregar um blob no armazenamento do Azure, baixar um blob e listar os BLOBs em um contêiner.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/04/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 12078300b36412a1507991507a39654acdeb7c69
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825337"
---
# <a name="quickstart-azure-blob-storage-client-library-v2-for-javascript"></a>Início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure v2 para JavaScript

Neste guia de instruções, você aprende a usar a biblioteca de cliente para node. js v2 para carregar, baixar e listar BLOBs com o armazenamento de BLOBs do Azure.

> [!TIP]
> A versão mais recente da biblioteca de cliente de armazenamento do Azure para node. js é v10. A Microsoft recomenda que você use a versão mais recente da biblioteca de cliente, quando possível. Para começar a usar o v10, consulte [início rápido: carregar, baixar, listar e excluir BLOBs usando a biblioteca de cliente de armazenamento do Azure para JavaScript v10 (versão prévia)](storage-quickstart-blobs-nodejs-v10.md).

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Crie uma conta de armazenamento do Azure no [portal do Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Para obter ajuda na criação da conta, veja [Criar uma conta de armazenamento](../common/storage-quickstart-create-account.md).

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) é um aplicativo de console simples do node. js. Para começar, clone o repositório para o seu computador com o seguinte comando:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Para abrir a aplicação, procure a pasta *storage-blobs-node-quickstart* e abra-a no seu ambiente de edição de código preferido.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Antes de executar a aplicação, tem de indicar a cadeia de ligação da sua conta de armazenamento. O repositório de exemplo inclui um ficheiro denominado *. env.example*. Pode mudar o nome deste ficheiro ao remover a extensão *.example*, o que resulta num ficheiro denominado *.env*. Dentro do ficheiro *.env*, adicione o valor da cadeia de ligação a seguir à chave *AZURE_STORAGE_CONNECTION_STRING*.

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

O resultado do script será semelhante ao seguinte:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Se você estiver usando uma nova conta de armazenamento para este exemplo, talvez não veja nenhum nome de contêiner listado no rótulo "*contêineres*".

## <a name="understanding-the-code"></a>Compreender o código
A primeira expressão é utilizada para carregar os valores nas variáveis de ambiente.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

O módulo *dotenv* carrega variáveis de ambiente ao executar a aplicação localmente para depuração. Os valores são definidos num ficheiro denominado *.env* e carregados para o contexto da execução atual. Em contextos de produção, a configuração do servidor fornece estes valores e é por isso que este código é executado apenas quando o script não está a ser executado num contexto de "produção".

```javascript
const path = require('path');
const storage = require('azure-storage');
```

O objetivo dos módulos é o seguinte: 

Ficheiro com o nome *.env* no contexto de execução atual
- *path* é necessário para determinar o caminho absoluto do ficheiro a carregar no armazenamento de blobs
- *Azure-Storage* é o módulo [biblioteca de cliente de armazenamento do Azure](https://docs.microsoft.com/javascript/api/azure-storage) para node. js

Em seguida, a variável **blobService** é iniciada como uma nova instância do serviço Blob do Azure.

```javascript
const blobService = storage.createBlobService();
```

Na seguinte implementação, cada uma das funções *blobService* é encapsulada num wrapper *Promise*, o que permite o acesso à função *async* e ao operador *await* de JavaScript para simplificar o cariz de chamada de retorno da [API de Armazenamento do Azure](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Quando é devolvida uma resposta bem-sucedida para cada função, a promessa (Promise) é resolvida com dados relevantes, juntamente com uma mensagem específica para a ação.

### <a name="list-containers"></a>Listar contentores

A função *listContainers* chama o método [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) que devolve coleções de contentores em grupos.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

O tamanho dos grupos pode ser configurado através do método [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest). Chamar o método *listContainersSegmented* devolve os metadados do blob como uma matriz de instâncias [ContainerResult](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.containerresult?view=azure-node-latest). Os resultados são devolvidos em lotes (segmentos) de 5000 incrementos. Se existirem mais de 5000 blobs num contentor, os resultados incluem um valor para *continuationToken*. Para listar os segmentos subsequentes do contentor de blobs, pode transmitir o token de continuação novamente para o método *listContainersSegment* como um segundo argumento.

### <a name="create-a-container"></a>Criar um contentor

A função *createContainer* chama [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) e define o nível de acesso apropriado para o blob.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

O segundo parâmetro (*options*) para **createContainerIfNotExists** aceita um valor para [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). O valor *blob* para *publicAccessLevel* especifica que dados de blob específicos estão expostos ao público. Esta definição contrasta com o acesso de nível *container*, que concede a capacidade de listar o conteúdo do contentor.

A utilização de **createContainerIfNotExists** permite que a aplicação execute o comando *createContainer* várias vezes sem devolver erros quando o contentor já existe. Num ambiente de produção, o mais frequente é **createContainerIfNotExists** só ser chamado uma vez, já que o mesmo contentor é utilizado em toda a aplicação. Nestes casos, pode criar o contentor antecipadamente através do portal ou através da CLI do Azure.

### <a name="upload-text"></a>Carregar texto

A função *uploadString* chama o método [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) para escrever (ou substituir) uma sequência arbitrária no contentor de blobs.

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>Carregar um ficheiro local

A função *uploadLocalFile* utiliza o método [createBlockBlobFromLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile-string--string--string--errororresult-blobresult--) para carregar e escrever (ou substituir) um ficheiro do sistema de ficheiros no armazenamento de blobs. 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
Outras abordagens disponíveis para carregar conteúdo nos blobs incluem trabalhar com [texto](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest-string--string--string---buffer--errororresult-blobresult--) e [fluxos](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream-string--string--stream-readable--number--errororresult-blobresult--). Para confirmar se o ficheiro está carregado no armazenamento de blobs, pode utilizar o [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para ver os dados na sua conta.

### <a name="list-the-blobs"></a>Listar os blobs

A função *listBlobs* chama o método [listBlobsSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented-string--continuationtoken--errororresult-listblobsresult--) para devolver uma lista de metadados do blob num contentor. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

Chamar *listBlobsSegmented* devolve os metadados do blob como uma matriz de instâncias [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest). Os resultados são devolvidos em lotes (segmentos) de 5000 incrementos. Se existirem mais de 5000 blobs num contentor, os resultados incluem um valor para **continuationToken**. Para listar os segmentos subsequentes do contentor do blobs, pode transmitir o token de continuação novamente para o **listBlobSegmented** como um segundo argumento.

### <a name="download-a-blob"></a>Transferir um blob

A função *downloadBlob* utiliza o método [getBlobToText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) para transferir os conteúdos do blob para o caminho absoluto do ficheiro especificado.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
A implementação aqui apresentada altera a origem e devolve os conteúdos do blob como uma cadeia. Também pode transferir o blob como uma [transmissão](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest), bem como diretamente para um [ficheiro local](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest).

### <a name="delete-a-blob"></a>Eliminar um blob

A função *deleteBlob* chama a função [deleteBlobIfExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists-string--string--errororresult-boolean--). Tal como o nome sugere, esta função não devolve um erro se o blob já tiver sido eliminado.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>Eliminar um contentor

Os contentores são eliminados ao chamar o método *deleteContainer* no serviço Blob e ao transmitir o nome do contentor.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>Código de chamada

Para suportar a sintaxe de JavaScript *async/await*, todo o código de chamada é encapsulado numa função chamada *execute*. Em seguida, a função execute é chamada e processada como uma promessa.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Todo o código seguinte é executado dentro da função execute onde o comentário `// commands` é colocado.

Em primeiro lugar, as variáveis relevantes são declaradas para atribuir nomes e conteúdo de exemplo, e para apontar para o ficheiro local, para carregar para o armazenamento de blobs.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

Para listar os contentores na conta de armazenamento, a função listContainers é chamada e a lista de contentores devolvida é registada na janela de resultados.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

Assim que a lista de contentores estiver disponível, poderá utilizar o método de Matriz *findIndex* para ver se o contentor que pretende criar já existe. Se o contentor não existir, o contentor será criado.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
Em seguida, uma cadeia e um ficheiro local são carregados para o armazenamento de blobs.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
O processo para listar blobs é idêntico ao da listagem de contentores. A chamada da função *listBlobs* devolve uma matriz de blobs no contentor e os mesmos são registados na janela de resultados.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

Para transferir um blob, a resposta é recolhida e utilizada para aceder ao valor do blob. A partir da resposta, readableStreamBody é convertido numa cadeia e registado na janela de resultados.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

Por fim, o blob e o contentor são eliminados da conta de armazenamento.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Limpar recursos
Todos os dados escritos na conta de armazenamento são eliminados automaticamente no fim do código de exemplo. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Recursos para desenvolver aplicações Node.js com blobs

Veja estes recursos adicionais para o desenvolvimento de Node.js com o Armazenamento de blobs:

### <a name="binaries-and-source-code"></a>Binários e código fonte

- Consulte e instale o [código fonte da biblioteca de cliente do Node.js](https://github.com/Azure/azure-storage-node) para o Armazenamento do Azure no GitHub.

### <a name="client-library-reference-and-samples"></a>Referência e exemplos da biblioteca de cliente

- Veja a [Referência da API de Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage) para obter mais informações sobre a biblioteca de cliente do Node.js.
- Explore os [Exemplos de armazenamento de blobs](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) escritos com a biblioteca de cliente do Node.js.

## <a name="next-steps"></a>Passos seguintes

Este artigo demonstra como carregar um arquivo entre um disco local e o armazenamento de BLOBs do Azure usando o Node. js. Para saber mais sobre a utilização do armazenamento de blobs, avance para o repositório do GitHub.

> [!div class="nextstepaction"]
> [Armazenamento do Microsoft Azure SDK para node. js e JavaScript para navegadores](https://github.com/Azure/azure-storage-node)
