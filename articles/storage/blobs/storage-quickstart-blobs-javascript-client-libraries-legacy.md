---
title: 'Quickstart: Armazenamento Azure Blob para JavaScript v10 no navegador'
description: Aprenda a carregar, listar e apagar bolhas usando JavaScript v10 SDK numa página HTML.
services: storage
author: mhopkins-msft
ms.custom: mvc, devx-track-js
ms.service: storage
ms.author: mhopkins
ms.date: 07/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: d212029936bcd257ef5a78eeedc98c2d6e1df514
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012787"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Quickstart: Gerir blobs com JavaScript v10 SDK no navegador

Neste arranque rápido, aprende-se a gerir as bolhas utilizando o código JavaScript que corre inteiramente no navegador. As bolhas são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, streaming de meios e dados de arquivo. Utilizará as medidas de segurança necessárias para garantir o acesso protegido à sua conta de armazenamento de bolhas.

> [!NOTE]
> Este quickstart usa uma versão antiga da biblioteca de clientes de armazenamento Azure Blob. Para começar com a versão mais recente, consulte [Quickstart: Gerencie blobs com JavaScript v12 SDK num browser](quickstart-blobs-javascript-browser.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma conta do Armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- Um servidor web local. Este artigo utiliza [Node.js](https://nodejs.org) para abrir um servidor básico.
- [Visual Studio Code](https://code.visualstudio.com).
- Uma extensão vs Code para depurar o navegador, como [Debugger para Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) ou [Debugger para o Microsoft Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge).

## <a name="setting-up-storage-account-cors-rules"></a>Configurar regras de CORS da conta de armazenamento

Antes de a sua aplicação web poder aceder a um armazenamento de bolhas do cliente, tem de configurar a sua conta para permitir a [partilha de recursos de origem cruzada,](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)ou CORS.

Regresse ao portal do Azure e selecione a sua conta de armazenamento. Para definir uma nova regra CORS, navegue na secção **Definições** e clique no link **CORS.** Em seguida, clique no botão **Adicionar** para abrir a janela **Adicionar regra de CORS**. Neste início rápido, vai criar uma regra CORS aberta:

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
> Certifique-se de que quaisquer configurações que utilize na produção exponham a quantidade mínima de acesso necessária à sua conta de armazenamento para manter um acesso seguro. As definições de CORS descritas aqui são adequadas para um início rápido, porque definem uma política de segurança permissiva. No entanto, não são recomendadas para um contexto do mundo real.

Em seguida, vai utilizar o Azure Cloud Shell para criar um token de segurança.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Criar uma assinatura de acesso partilhado

A assinatura de acesso partilhado (SAS) é utilizada pelo código em execução no browser para autorizar pedidos ao armazenamento de Blobs. Ao utilizar o SAS, o cliente pode autorizar o acesso aos recursos de armazenamento sem a chave de acesso da conta ou sem a cadeia de ligação. Para obter mais informações, veja [Using shared access signatures (SAS)](../common/storage-sas-overview.md) (Utilizar assinaturas de acesso partilhado [SAS]).

Pode criar um SAS utilizando o Azure CLI através da casca de nuvem Azure, ou com o portal Azure ou o Azure Storage Explorer. A tabela a seguir descreve os parâmetros necessários para fornecer valores para gerar um SAS com o CLI.

| Parâmetro      |Descrição  | Marcador de posição |
|----------------|-------------|-------------|
| *expiração*       | A data de expiração do token de acesso no formato AAAA-MM-DD. Introduza a data de amanhã para utilização neste início rápido. | *FUTURE_DATE* (data futura) |
| *nome de conta* | O nome da conta de armazenamento. Utilize o nome colocado de parte num passo anterior. | *YOUR_STORAGE_ACCOUNT_NAME* (nome da conta de armazenamento) |
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

Agora que o SAS é gerado, copie o valor de retorno e guarde-o em algum lugar para ser usado em um próximo passo. Se gerou o seu SAS utilizando um método diferente do Azure CLI, terá de remover a inicial `?` se estiver presente. Este personagem é um separador URL que já é fornecido no modelo de URL mais tarde neste tópico onde o SAS é usado.

> [!IMPORTANT]
> Na produção, passe sempre fichas SAS usando TLS. Além disso, os tokens SAS devem gerados no servidor e enviados para a página HTML, de modo a serem transmitidos novamente para o Armazenamento de Blobs do Azure. Uma abordagem que pode considerar passa por utilizar uma função sem servidor para gerar os tokens SAS. O portal do Azure inclui modelos de funções que têm a capacidade de gerar um SAS com uma função de JavaScript.

## <a name="implement-the-html-page"></a>Implementar a página HTML

Nesta secção, você vai criar uma página web básica e configurar o Código VS para lançar e depurar a página. Antes de poder lançar, no entanto, terá de usar Node.js para iniciar um servidor web local e servir a página quando o seu navegador o solicitar. Em seguida, irá adicionar código JavaScript para ligar para várias APIs de armazenamento de bolhas e exibir os resultados na página. Pode também ver os resultados destas chamadas no [portal Azure](https://portal.azure.com), [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer)e na extensão de Armazenamento [Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) para o Código VS.

### <a name="set-up-the-web-application"></a>Configurar a aplicação Web

Em primeiro lugar, crie uma nova pasta chamada *azure-blobs-javascript e abra-a* no Código VS. Em seguida, crie um novo ficheiro no Código VS, adicione o seguinte HTML e guarde-o como *index.html* na pasta *azure-blobs-javascript.*

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

### <a name="configure-the-debugger"></a>Configure o depurador

Para configurar a extensão de depurante em Código VS, selecione **Debug > Add Configuration...** em seguida, selecione **Chrome** ou **Edge**, dependendo da extensão instalada na secção Pré-Requisitos anteriormente. Esta ação cria uma *launch.jsno* ficheiro e abre-a no editor.

Em seguida, modifique a *launch.jsno* ficheiro de modo a que o `url` valor inclua como `/index.html` mostrado:

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

Esta configuração diz ao Vs Code qual o navegador a lançar e qual URL carregar.

### <a name="launch-the-web-server"></a>Lançar o servidor web

Para lançar o servidor web Node.js local, selecione **Ver > Terminal** para abrir uma janela de consola dentro do Código VS e, em seguida, insira o seguinte comando.

```console
npx http-server
```

Este comando instalará o pacote *de servidor de http e* lançará o servidor, disponibilizando a pasta atual através de URLs predefinidos, incluindo o indicado no passo anterior.

### <a name="start-debugging"></a>Comece a depurar

Para lançar *index.html* no navegador com o depurante do Código VS anexado, selecione **Debug > Iniciar a depurar** ou prima F5 em Código VS.

A UI apresentada ainda não faz nada, mas irá adicionar código JavaScript na secção seguinte para implementar cada função mostrada. Em seguida, pode definir pontos de rutura e interagir com o depurador quando estiver parado no seu código.

Quando escoar alterações para *index.html*, certifique-se de recarregar a página para ver as alterações no navegador. No Código VS, também pode selecionar **Debug > Reiniciar depurar** ou premir CTRL + SHIFT + F5.

### <a name="add-the-blob-storage-client-library"></a>Adicione a biblioteca do cliente de armazenamento blob

Para permitir chamadas para a API de armazenamento de bolhas, primeiro [descarregue o SDK de armazenamento Azure para a biblioteca do cliente Dolb - Blob](https://aka.ms/downloadazurestoragejsblob), extraia o conteúdo do fecho de correr e coloque o ficheiro *azure-storage-blob.js* na pasta *azure-blobs-javascript.*

Em seguida, cole o html seguinte em *index.html* após a etiqueta `</body>` de fecho, substituindo o comentário do espaço reservado.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Este código adiciona uma referência ao ficheiro de script e fornece um lugar para o seu próprio código JavaScript. Para efeitos deste arranque rápido, estamos *a* usar oazure-storage-blob.jsficheiro de scripts para que possas abri-lo no Código VS, ler o seu conteúdo e definir pontos de rutura. Na produção, deve utilizar o ficheiro *deazure-storage.blob.min.js* mais compacto que também é fornecido no ficheiro zip.

Pode saber mais sobre cada função de armazenamento de bolhas na [documentação de referência.](/javascript/api/%40azure/storage-blob/index) Note que algumas das funções no SDK só estão disponíveis em Node.js ou apenas disponíveis no navegador.

O código em *azure-storage-blob.js* exporta uma variável global chamada `azblob` , que você usará no seu código JavaScript para aceder às APIs de armazenamento de bolhas.

### <a name="add-the-initial-javascript-code"></a>Adicione o código JavaScript inicial

Em seguida, cole o seguinte código no `<script>` elemento indicado no bloco de código anterior, substituindo o comentário do espaço reservado.

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

Este código cria campos para cada elemento HTML que o seguinte código utilizará e implementa uma `reportStatus` função para exibir a saída.

Nas seguintes secções, adicione cada novo bloco de código JavaScript após o bloco anterior.

### <a name="add-your-storage-account-info"></a>Adicione a informação da sua conta de armazenamento

Em seguida, adicione código para aceder à sua conta de armazenamento, substituindo os espaços reservados pelo nome da sua conta e o SAS que gerou num passo anterior.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Este código utiliza a informação da sua conta e SAS para criar uma instância [ContainerURL,](/javascript/api/@azure/storage-blob/ContainerURL) que é útil para criar e manipular um recipiente de armazenamento.

### <a name="create-and-delete-a-storage-container"></a>Criar e apagar um recipiente de armazenamento

Em seguida, adicione código para criar e apagar o recipiente de armazenamento quando premir o botão correspondente.

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

Este código chama o ContainerURL [de criar](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) e [eliminar](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) funções sem utilizar uma instância [aborter.](/javascript/api/@azure/storage-blob/aborter) Para manter as coisas simples para este arranque rápido, este código pressupõe que a sua conta de armazenamento foi criada e está ativada. No código de produção, utilize uma instância Aborter para adicionar funcionalidade de timeout.

### <a name="list-blobs"></a>Listar blobs

Em seguida, adicione código para listar o conteúdo do recipiente de armazenamento quando premir o botão **Ficheiros Lista.**

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

Este código chama a função [ContainerURL.listBlobFlatSegment](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) num loop para garantir que todos os segmentos são recuperados. Para cada segmento, ele dá a volta à lista de itens blob que contém e atualiza a lista **de Ficheiros.**

### <a name="upload-blobs"></a>Carregar bolhas

Em seguida, adicione código para carregar ficheiros no recipiente de armazenamento quando premir o botão **'Selecionar' e carregar ficheiros.**

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

Este código liga o botão **'Selecionar' e carregar ficheiros** ao `file-input` elemento oculto. Desta forma, o evento do botão `click` aciona o evento de entrada de `click` ficheiros e exibe o selecionador de ficheiros. Depois de selecionar ficheiros e fechar a caixa de diálogo, o `input` evento ocorre e a `uploadFiles` função é chamada. Esta função chama a [função de uploadBrowserDataToBlockBlob apenas](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) para cada ficheiro selecionado. Cada chamada devolve uma Promessa, que é adicionada a uma lista para que todos possam ser aguardados de uma só vez, fazendo com que os ficheiros sejam carregados em paralelo.

### <a name="delete-blobs"></a>Eliminar blobs

Em seguida, adicione código para eliminar ficheiros do recipiente de armazenamento quando premir o botão **Descodiá-lo.**

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

Este código chama a função [BlobURL.delete](/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) para remover cada ficheiro selecionado na lista. Em seguida, chama a `listFiles` função mostrada anteriormente para refrescar o conteúdo da lista **de Ficheiros.**

### <a name="run-and-test-the-web-application"></a>Executar e testar a aplicação web

Neste ponto, você pode lançar a página e experimentar para obter uma sensação de como o armazenamento de bolhas funciona. Se ocorrerem erros (por exemplo, quando tentar listar ficheiros antes de criar o recipiente), o painel **de estado** mostrará a mensagem de erro recebida. Também pode definir pontos de rutura no código JavaScript para examinar os valores devolvidos pelas APIs de armazenamento.

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos criados durante este arranque rápido, vá ao [portal Azure](https://portal.azure.com) e elimine o grupo de recursos que criou na secção Pré-Requisitos.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um site simples que acede ao armazenamento de blob a partir do JavaScript baseado no navegador. Para saber como pode hospedar um website em armazenamento de bolhas, continue até ao seguinte tutorial:

> [!div class="nextstepaction"]
> [Hospedar um site estático no Blob Storage](./storage-blob-static-website-host.md)