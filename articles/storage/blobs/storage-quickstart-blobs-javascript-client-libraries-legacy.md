---
title: 'Quickstart: Armazenamento Azure Blob para JavaScript v10 no navegador'
description: Aprenda a carregar, listar e eliminar bolhas usando JavaScript v10 SDK numa página HTML.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.author: mhopkins
ms.date: 01/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: 920d3d6c1cfc928efa5daa2d6c0aa3a6b4e81375
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "82161130"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Quickstart: Gerir bolhas com JavaScript v10 SDK no navegador

Neste arranque rápido, aprende-se a gerir bolhas utilizando o código JavaScript que funciona inteiramente no navegador. As bolhas são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, meios de streaming e dados de arquivo. Utilizará as medidas de segurança necessárias para garantir o acesso protegido à sua conta de armazenamento blob.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma conta do Armazenamento do Azure. [Criar uma conta de armazenamento.](../common/storage-account-create.md)
- Um servidor local. Este artigo usa [node.js](https://nodejs.org) para abrir um servidor básico.
- [Código de estúdio visual.](https://code.visualstudio.com)
- Uma extensão de código VS para depuração do navegador, como [Debugger para Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) ou [Debugger para Microsoft Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge).

## <a name="setting-up-storage-account-cors-rules"></a>Configurar regras de CORS da conta de armazenamento

Antes de a sua aplicação web poder aceder a um armazenamento de bolhas do cliente, tem de configurar a sua conta para permitir a [partilha de recursos de origem cruzada](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services), ou CORS.

Regresse ao portal do Azure e selecione a sua conta de armazenamento. Para definir uma nova regra CORS, navegue para a secção **Definições** e clique no link **CORS.** Em seguida, clique no botão **Adicionar** para abrir a janela **Adicionar regra de CORS**. Neste início rápido, vai criar uma regra CORS aberta:

![Definições de CORS da Conta de Armazenamento de Blobs do Azure](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

A tabela seguinte descreve cada definição de CORS e explica os valores utilizados para definir a regra.

|Definição  |Valor  | Descrição |
|---------|---------|---------|
| Origens permitidas | * | Aceita uma lista delimitada por vírgulas de domínios definidos como origens aceitáveis. Definir o valor como `*` permite que todos os domínios acedam à conta de armazenamento. |
| Métodos permitidos     | delete, get, head, merge, post, options, and put | Apresenta uma lista dos verbos HTTP que podem ser executados na conta de armazenamento. Para efeitos deste início rápido, selecione todas as opções disponíveis. |
| Cabeçalhos permitidos | * | Define uma lista de cabeçalhos de pedidos (incluindo cabeçalhos com prefixo) que a conta de armazenamento permite. Definir o valor como `*` permite acesso a todos os cabeçalhos. |
| Cabeçalhos expostos | * | Apresenta uma lista dos cabeçalhos de resposta que a conta permite. Definir o valor como `*` permite à conta enviar qualquer cabeçalho.  |
| Idade máxima (segundos) | 86400 | O período máximo de tempo que o browser coloca em cache o pedido OPTIONS de validação prévia. Um valor de *86400* permite que a cache permaneça por um dia completo. |

> [!IMPORTANT]
> Certifique-se de que quaisquer configurações que utilize na produção expõem a quantidade mínima de acesso necessária à sua conta de armazenamento para manter um acesso seguro. As definições de CORS descritas aqui são adequadas para um início rápido, porque definem uma política de segurança permissiva. No entanto, não são recomendadas para um contexto do mundo real.

Em seguida, vai utilizar o Azure Cloud Shell para criar um token de segurança.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Criar uma assinatura de acesso partilhado

A assinatura de acesso partilhado (SAS) é utilizada pelo código em execução no browser para autorizar pedidos ao armazenamento de Blobs. Ao utilizar o SAS, o cliente pode autorizar o acesso aos recursos de armazenamento sem a chave de acesso da conta ou sem a cadeia de ligação. Para obter mais informações, veja [Using shared access signatures (SAS)](../common/storage-sas-overview.md) (Utilizar assinaturas de acesso partilhado [SAS]).

Pode criar um SAS utilizando o Azure CLI através da casca de nuvem Azure, ou com o portal Azure ou o Azure Storage Explorer. A tabela a seguir descreve os parâmetros necessários para que gere valores para gerar um SAS com o CLI.

| Parâmetro      |Descrição  | Marcador de posição |
|----------------|-------------|-------------|
| *expirar*       | A data de expiração do token de acesso no formato AAAA-MM-DD. Introduza a data de amanhã para utilização neste início rápido. | *FUTURE_DATE* (data futura) |
| *nome da conta* | O nome da conta de armazenamento. Utilize o nome colocado de parte num passo anterior. | *YOUR_STORAGE_ACCOUNT_NAME* (nome da conta de armazenamento) |
| *account-key* (chave da conta)  | A chave da conta de armazenamento. Utilize a chave colocada de parte num passo anterior. | *YOUR_STORAGE_ACCOUNT_KEY* (chave da conta de armazenamento) |

Utilize o seguinte comando CLI, com valores reais para cada espaço reservado, para gerar um SAS que pode utilizar no seu código JavaScript.

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

Poderá achar que as séries de valores a seguir a cada parâmetro são algo crípticas. Estes valores de parâmetros são obtidos a partir da primeira letra da respetiva permissão. A tabela seguinte explica a proveniência dos valores:

| Parâmetro        | Valor   | Descrição  |
|------------------|---------|---------|
| *permissões*    | racwdl  | Esta SAS permite capacidades de *leitura*, *acrescento*, *criação*, *escrita*, *eliminação* e *listagem*. |
| *resource-types* (tipos de recursos) | sco     | Os recursos que a SAS afeta são *serviço*, *contentor* e *objeto*. |
| *serviços*       | b       | O serviço que a SAS afeta é o serviço de *blobs*. |

Agora que o SAS é gerado, copie o valor de retorno e guarde-o em algum lugar para ser usado num próximo passo. Se gerou o seu SAS utilizando um método diferente do ClI Azure, terá de remover a inicial `?` se estiver presente. Este personagem é um separador DEURL que já é fornecido no modelo de URL mais tarde neste tópico onde o SAS é usado.

> [!IMPORTANT]
> Na produção, passe sempre tokens SAS utilizando TLS. Além disso, os tokens SAS devem gerados no servidor e enviados para a página HTML, de modo a serem transmitidos novamente para o Armazenamento de Blobs do Azure. Uma abordagem que pode considerar passa por utilizar uma função sem servidor para gerar os tokens SAS. O portal do Azure inclui modelos de funções que têm a capacidade de gerar um SAS com uma função de JavaScript.

## <a name="implement-the-html-page"></a>Implementar a página HTML

Nesta secção, irá criar uma página web básica e configurar o Código VS para lançar e depurar a página. No entanto, antes de poder lançar, terá de usar o Node.js para iniciar um servidor web local e servir a página quando o seu navegador o solicitar. Em seguida, irá adicionar código JavaScript para ligar para várias APIs de armazenamento blob e mostrar os resultados na página. Pode também ver os resultados destas chamadas no [portal Azure,](https://portal.azure.com) [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer)e na extensão de armazenamento [Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) para código VS.

### <a name="set-up-the-web-application"></a>Configurar a aplicação Web

Primeiro, crie uma nova pasta chamada *azure-blobs-javascript e abra-a* no Código VS. Em seguida, crie um novo ficheiro no Código VS, adicione o seguinte HTML e guarde-o como *index.html* na pasta *azure-blobs-javascript.*

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>Configure o debugger

Para configurar a extensão de debugger no Código VS, selecione **Debug > Adicionar Configuração...**, em seguida, selecione **Chrome** ou **Edge**, dependendo da extensão que instalou na secção Pré-requisitos mais cedo. Esta ação cria um ficheiro *launch.json* e abre-o no editor.

Em seguida, modifique o ficheiro *launch.json* de modo a que o `url` valor inclua `/index.html` como mostrado:

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

Esta configuração diz ao CÓDIGO VS qual o navegador a lançar e qual URL para carregar.

### <a name="launch-the-web-server"></a>Lançar o servidor web

Para lançar o servidor web local Node.js, selecione **Ver > Terminal** para abrir uma janela de consola dentro do Código VS e, em seguida, introduza o seguinte comando.

```console
npx http-server
```

Este comando instalará o pacote *http-servidor* e lançará o servidor, disponibilizando a pasta atual através de URLs predefinidos, incluindo a indicada no passo anterior.

### <a name="start-debugging"></a>Comece a depurar

Para lançar *o index.html* no navegador com o debugger do Código VS ligado, selecione **Debug > Iniciar depuração** ou prima F5 no Código VS.

O UI apresentado ainda não faz nada, mas irá adicionar código JavaScript na seguinte secção para implementar cada função mostrada. Em seguida, pode definir pontos de rutura e interagir com o debugger quando é pausado no seu código.

Quando efizer alterações para *index.html,* certifique-se de recarregar a página para ver as alterações no navegador. No Código VS, também pode selecionar **Debug > Reiniciar Debugging** ou premir CTRL + SHIFT + F5.

### <a name="add-the-blob-storage-client-library"></a>Adicione a biblioteca de clientes de armazenamento blob

Para ativar as chamadas para a API de armazenamento blob, primeiro [descarregue o SDK de Armazenamento Azure para javaScript - biblioteca de clientes Blob,](https://aka.ms/downloadazurestoragejsblob)extraia o conteúdo do fecho e coloque o ficheiro *azure-storage-blob.js* na pasta *azure-blobs-javascript.*

Em seguida, colhe o seguinte HTML `</body>` em *index.html* após a etiqueta de fecho, substituindo o comentário do espaço reservado.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Este código adiciona uma referência ao ficheiro script e fornece um lugar para o seu próprio código JavaScript. Para efeitos deste arranque rápido, estamos a usar o ficheiro de script *azure-storage-blob.js* para que possa abri-lo no Código VS, ler o seu conteúdo e definir pontos de rutura. Na produção, deve utilizar o ficheiro *azure-storage.blob.min.js* mais compacto que também é fornecido no ficheiro zip.

Pode saber mais sobre cada função de armazenamento blob na [documentação de referência](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index). Note que algumas das funções no SDK só estão disponíveis no Node.js ou apenas disponíveis no navegador.

O código em *azure-storage-blob.js* exporta uma variável global chamada `azblob`, que você vai usar no seu código JavaScript para aceder às APIs de armazenamento blob.

### <a name="add-the-initial-javascript-code"></a>Adicione o código JavaScript inicial

Em seguida, colhe o `<script>` seguinte código no elemento mostrado no bloco de código anterior, substituindo o comentário do espaço reservado.

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

Este código cria campos para cada elemento HTML que o `reportStatus` seguinte código utilizará e implementa uma função para visualizar a saída.

Nas seguintes secções, adicione cada novo bloco de código JavaScript após o bloco anterior.

### <a name="add-your-storage-account-info"></a>Adicione as informações da sua conta de armazenamento

Em seguida, adicione código para aceder à sua conta de armazenamento, substituindo os espaços reservados pelo seu nome de conta e o SAS que gerou num passo anterior.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Este código utiliza as informações da sua conta e sas para criar uma instância [De ContainerURL,](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) que é útil para criar e manipular um recipiente de armazenamento.

### <a name="create-and-delete-a-storage-container"></a>Criar e eliminar um recipiente de armazenamento

Em seguida, adicione código para criar e eliminar o recipiente de armazenamento quando premir o botão correspondente.

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

Este código chama o ContainerURL [de criar](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) e [eliminar](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) funções sem utilizar uma instância [abortora.](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) Para manter as coisas simples para este arranque rápido, este código pressupõe que a sua conta de armazenamento foi criada e está ativada. No código de produção, utilize uma instância de Aborter para adicionar a funcionalidade de timeout.

### <a name="list-blobs"></a>Listar blobs

Em seguida, adicione código para listar o conteúdo do recipiente de armazenamento quando premir o botão **de ficheiros Lista.**

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

Este código chama a função [ContainerURL.listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) num loop para garantir que todos os segmentos são recuperados. Para cada segmento, passa a lista de itens blob que contém e atualiza a lista **de Ficheiros.**

### <a name="upload-blobs"></a>Carregar bolhas

Em seguida, adicione código para carregar ficheiros no recipiente de armazenamento quando premir o botão **Select e carregar ficheiros.**

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

Este código liga o botão Select `file-input` e carregar **ficheiros** ao elemento oculto. Desta forma, o `click` evento do botão `click` aciona o evento de entrada de ficheiros e exibe o apanhador de ficheiros. Depois de selecionar ficheiros e fechar `input` a caixa `uploadFiles` de diálogo, o evento ocorre e a função é chamada. Esta função chama a função de [upload BrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) apenas para cada ficheiro que selecionou. Cada chamada devolve uma Promessa, que é adicionada a uma lista para que todos possam ser aguardados de uma só vez, fazendo com que os ficheiros carreguem em paralelo.

### <a name="delete-blobs"></a>Eliminar blobs

Em seguida, adicione código para eliminar ficheiros do recipiente de armazenamento quando premir o botão **de ficheiros selecionado si.**

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

Este código chama a função [BlobURL.delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) para remover cada ficheiro selecionado na lista. Em seguida, `listFiles` chama a função mostrada anteriormente para refrescar o conteúdo da lista **de Ficheiros.**

### <a name="run-and-test-the-web-application"></a>Executar e testar a aplicação web

Neste ponto, você pode lançar a página e experimentar para obter uma sensação de como o armazenamento de bolhas funciona. Se ocorrerem erros (por exemplo, quando tentar listar ficheiros antes de criar o recipiente), o painel **'Status'** apresentará a mensagem de erro recebida. Também pode definir pontos de rutura no código JavaScript para examinar os valores devolvidos pelas APIs de armazenamento.

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados durante este arranque rápido, vá ao [portal Azure](https://portal.azure.com) e elimine o grupo de recursos que criou na secção Pré-requisitos.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um simples website que acede ao armazenamento de blob a partir do JavaScript baseado no navegador. Para saber como pode hospedar um website em si no armazenamento de blob, continue para o seguinte tutorial:

> [!div class="nextstepaction"]
> [Hospedar um site estático no Armazenamento blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
