---
title: Início rápido-criar um blob no armazenamento do Azure usando JavaScript e HTML no navegador
description: Aprenda a carregar, listar e excluir BLOBs usando JavaScript em uma página HTML.
services: storage
author: KarlErickson
ms.custom: mvc
ms.service: storage
ms.author: karler
ms.date: 08/29/2019
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: fc6ccaae698043db631c7724c6aabbca16f4328f
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172847"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-upload-list-and-delete-blobs-using-azure-storage-v10-sdk-for-javascripthtml-in-the-browser"></a>Início rápido: Carregar, listar e excluir BLOBs usando o SDK do V10 de armazenamento do Azure para JavaScript/HTML no navegador

Neste guia de início rápido, você aprenderá a usar o [V10 do SDK de armazenamento do Azure para JavaScript-](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob#readme) biblioteca de BLOBs para gerenciar BLOBs do código JavaScript executado inteiramente no navegador. A abordagem utilizada aqui mostra como utilizar as medidas de segurança necessárias para garantir acesso protegido para a sua conta do blob storage.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

As bibliotecas de cliente JavaScript do armazenamento do Azure não funcionarão diretamente do sistema de arquivos e devem ser servidas de um servidor Web. Este tópico usa o [node. js](https://nodejs.org) para iniciar um servidor básico. Se preferir não instalar o nó, você poderá usar qualquer outro meio de execução de um servidor Web local.

Para seguir as etapas de depuração, você precisará [Visual Studio Code](https://code.visualstudio.com) e o [depurador para o Chrome](vscode:extension/msjsdiag.debugger-for-chrome) ou o [depurador para a extensão do Microsoft Edge](vscode:extension/msjsdiag.debugger-for-edge) .

## <a name="setting-up-storage-account-cors-rules"></a>Configurar regras de CORS da conta de armazenamento

Antes que seu aplicativo Web possa acessar um armazenamento de BLOBs do cliente, você deve configurar sua conta para habilitar o [compartilhamento de recursos entre origens](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)ou CORS.

Regresse ao portal do Azure e selecione a sua conta de armazenamento. Para definir uma nova regra de CORS, navegue até a seção **configurações** e clique no link **CORS** . Em seguida, clique no botão **Adicionar** para abrir a janela **Adicionar regra de CORS**. Neste início rápido, vai criar uma regra CORS aberta:

![Definições de CORS da Conta de Armazenamento de Blobs do Azure](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

A tabela seguinte descreve cada definição de CORS e explica os valores utilizados para definir a regra.

|Definição  |Valor  | Descrição |
|---------|---------|---------|
| Origens permitidas | * | Aceita uma lista delimitada por vírgulas de domínios definidos como origens aceitáveis. Definir o valor como `*` permite que todos os domínios acedam à conta de armazenamento. |
| Verbos permitidos     | delete, get, head, merge, post, options, and put | Apresenta uma lista dos verbos HTTP que podem ser executados na conta de armazenamento. Para efeitos deste início rápido, selecione todas as opções disponíveis. |
| Cabeçalhos permitidos | * | Define uma lista de cabeçalhos de pedidos (incluindo cabeçalhos com prefixo) que a conta de armazenamento permite. Definir o valor como `*` permite acesso a todos os cabeçalhos. |
| Cabeçalhos expostos | * | Apresenta uma lista dos cabeçalhos de resposta que a conta permite. Definir o valor como `*` permite à conta enviar qualquer cabeçalho.  |
| Idade máxima (segundos) | 86400 | O período máximo de tempo que o browser coloca em cache o pedido OPTIONS de validação prévia. Um valor de *86400* permite que a cache permaneça por um dia completo. |

> [!IMPORTANT]
> Verifique se as configurações usadas na produção expõem a quantidade mínima de acesso necessário à sua conta de armazenamento para manter o acesso seguro. As definições de CORS descritas aqui são adequadas para um início rápido, porque definem uma política de segurança permissiva. No entanto, não são recomendadas para um contexto do mundo real.

Em seguida, vai utilizar o Azure Cloud Shell para criar um token de segurança.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Criar uma assinatura de acesso compartilhado

A assinatura de acesso partilhado (SAS) é utilizada pelo código em execução no browser para autorizar pedidos ao armazenamento de Blobs. Ao utilizar o SAS, o cliente pode autorizar o acesso aos recursos de armazenamento sem a chave de acesso da conta ou sem a cadeia de ligação. Para obter mais informações, veja [Using shared access signatures (SAS)](../common/storage-sas-overview.md) (Utilizar assinaturas de acesso partilhado [SAS]).

Você pode criar uma SAS usando o CLI do Azure por meio do Azure cloud Shell ou com o portal do Azure ou Gerenciador de Armazenamento do Azure. A tabela a seguir descreve os parâmetros para os quais você precisa fornecer valores para gerar uma SAS com a CLI.

| Parâmetro      |Descrição  | Marcador de posição |
|----------------|-------------|-------------|
| *expiry* (expira)       | A data de expiração do token de acesso no formato AAAA-MM-DD. Introduza a data de amanhã para utilização neste início rápido. | *FUTURE_DATE* (data futura) |
| *account-name* (nome da conta) | O nome da conta de armazenamento. Utilize o nome colocado de parte num passo anterior. | *YOUR_STORAGE_ACCOUNT_NAME* (nome da conta de armazenamento) |
| *account-key* (chave da conta)  | A chave da conta de armazenamento. Utilize a chave colocada de parte num passo anterior. | *YOUR_STORAGE_ACCOUNT_KEY* (chave da conta de armazenamento) |

Use o comando da CLI a seguir, com valores reais para cada espaço reservado, para gerar uma SAS que você pode usar em seu código JavaScript.

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

| Parâmetro        | Value   | Descrição  |
|------------------|---------|---------|
| *permissões* (permissões)    | racwdl  | Esta SAS permite capacidades de *leitura*, *acrescento*, *criação*, *escrita*, *eliminação* e *listagem*. |
| *resource-types* (tipos de recursos) | sco     | Os recursos que a SAS afeta são *serviço*, *contentor* e *objeto*. |
| *services* (serviços)       | b       | O serviço que a SAS afeta é o serviço de *blobs*. |

Agora que a SAS é gerada, copie o valor de retorno e salve-o em algum lugar para uso em uma etapa futura. Se você gerou a SAS usando um método diferente do CLI do Azure, será necessário remover a inicial `?` se ela estiver presente. Esse caractere é um separador de URL que já foi fornecido no modelo de URL mais adiante neste tópico onde a SAS é usada.

> [!IMPORTANT]
> Na produção, transmita sempre tokens os SAS através de SSL. Além disso, os tokens SAS devem gerados no servidor e enviados para a página HTML, de modo a serem transmitidos novamente para o Armazenamento de Blobs do Azure. Uma abordagem que pode considerar passa por utilizar uma função sem servidor para gerar os tokens SAS. O portal do Azure inclui modelos de funções que têm a capacidade de gerar um SAS com uma função de JavaScript.

## <a name="implement-the-html-page"></a>Implementar a página HTML

Nesta seção, você criará uma página da Web básica e configurará VS Code para iniciar e depurar a página. Para poder iniciar, no entanto, você precisará usar o Node. js para iniciar um servidor Web local e servir a página quando seu navegador solicitar. Em seguida, você adicionará o código JavaScript para chamar várias APIs de armazenamento de BLOBs e exibir os resultados na página. Você também pode ver os resultados dessas chamadas no [portal do Azure](https://portal.azure.com), [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer)e a extensão de [armazenamento do Azure](vscode:extension/ms-azuretools.vscode-azurestorage) para vs Code.

### <a name="set-up-the-web-application"></a>Configurar a aplicação Web

Primeiro, crie uma nova pasta chamada *Azure-BLOBs-JavaScript* e abra-a no vs Code. Em seguida, crie um novo arquivo em VS Code, adicione o seguinte HTML e salve-o como *index. html* na pasta *Azure-BLOBs-JavaScript* .

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

### <a name="configure-the-debugger"></a>Configurar o depurador

Para configurar a extensão do depurador no VS Code, selecione **depurar > adicionar configuração...** e, em seguida, selecione **Chrome** ou **Edge**, dependendo de qual extensão você instalou na seção pré-requisitos anteriormente. Essa ação cria um arquivo *Launch. JSON* e o abre no editor.

Em seguida, modifique o arquivo *Launch. JSON* para que `url` o valor `/index.html` inclua, conforme mostrado:

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

Essa configuração informa VS Code qual navegador deve ser iniciado e qual URL deve ser carregada.

### <a name="launch-the-web-server"></a>Iniciar o servidor Web

Para iniciar o servidor Web node. js local, selecione **exibir > terminal** para abrir uma janela de console dentro de vs Code e, em seguida, digite o comando a seguir.

```console
npx http-server
```

Esse comando instalará o pacote *http-Server* e iniciará o servidor, disponibilizando a pasta atual por meio de URLs padrão, incluindo aquela indicada na etapa anterior.

### <a name="start-debugging"></a>Iniciar Depuração

Para iniciar o *index. html* no navegador com o depurador de vs Code anexado, selecione **Depurar > Iniciar Depuração** ou pressione F5 no vs Code.

A interface do usuário exibida não faz nada ainda, mas você adicionará o código JavaScript na seção a seguir para implementar cada função mostrada. Em seguida, você pode definir pontos de interrupção e interagir com o depurador quando ele estiver em pausa no código.

Quando você fizer alterações no *index. html*, certifique-se de recarregar a página para ver as alterações no navegador. Em VS Code, você também pode selecionar **depurar > reiniciar a depuração** ou pressionar Ctrl + Shift + F5.

### <a name="add-the-blob-storage-client-library"></a>Adicionar a biblioteca de cliente de armazenamento de BLOBs

Para habilitar chamadas para a API de armazenamento de BLOBs, primeiro [Baixe a biblioteca de cliente do SDK de armazenamento do Azure para JavaScript-blob](https://aka.ms/downloadazurestoragejsblob), extraia o conteúdo do zip e coloque o arquivo *Azure-Storage. blob. js* na pasta *Azure-BLOBs-JavaScript* .

Em seguida, Cole o seguinte HTML em *index. html* após `</body>` a marca de fechamento, substituindo o comentário do espaço reservado.

```html
<script src="azure-storage.blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Esse código adiciona uma referência ao arquivo de script e fornece um local para seu próprio código JavaScript. Para os fins deste guia de início rápido, estamos usando o arquivo de script *Azure-Storage. blob. js* para que você possa abri-lo em vs Code, ler seu conteúdo e definir pontos de interrupção. Em produção, você deve usar o arquivo mais Compact *Azure-Storage. blob. min. js* que também é fornecido no arquivo zip.

Você pode saber mais sobre cada função de armazenamento de BLOBs na [documentação de referência](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index). Observe que algumas das funções no SDK estão disponíveis somente no node. js ou disponíveis apenas no navegador.

O código em *Azure-Storage. blob. js* exporta uma variável global chamada `azblob`, que você usará em seu código JavaScript para acessar as APIs de armazenamento de BLOBs.

### <a name="add-the-initial-javascript-code"></a>Adicionar o código JavaScript inicial

Em seguida, Cole o código a seguir `<script>` no elemento mostrado no bloco de código anterior, substituindo o comentário do espaço reservado.

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

Esse código cria campos para cada elemento HTML que será usado pelo código a seguir e implementa uma `reportStatus` função para exibir a saída.

Nas seções a seguir, adicione cada novo bloco de código JavaScript após o bloco anterior.

### <a name="add-your-storage-account-info"></a>Adicionar as informações da conta de armazenamento

Em seguida, adicione o código para acessar sua conta de armazenamento, substituindo os espaços reservados pelo nome da sua conta e a SAS que você gerou em uma etapa anterior.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Esse código usa as informações de sua conta e a SAS para criar uma instância de [ContainerURL](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) , que é útil para criar e manipular um contêiner de armazenamento.

### <a name="create-and-delete-a-storage-container"></a>Criar e excluir um contêiner de armazenamento

Em seguida, adicione o código para criar e excluir o contêiner de armazenamento quando você pressionar o botão correspondente.

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

Esse código chama as funções de [criação](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#create-aborter--icontainercreateoptions-) e [exclusão](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#delete-aborter--icontainerdeletemethodoptions-) de ContainerURL sem usar uma instância de [anulador](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) . Para manter as coisas simples para este guia de início rápido, esse código pressupõe que sua conta de armazenamento foi criada e está habilitada. No código de produção, use uma instância de anulador para adicionar a funcionalidade de tempo limite.

### <a name="list-blobs"></a>Listar blobs

Em seguida, adicione o código para listar o conteúdo do contêiner de armazenamento quando você pressionar o botão **listar arquivos** .

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

Esse código chama a função [ContainerURL. listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL?view=azure-node-preview#listblobflatsegment-aborter--undefined---string--icontainerlistblobssegmentoptions-) em um loop para garantir que todos os segmentos sejam recuperados. Para cada segmento, ele executa um loop sobre a lista de itens de BLOB que ele contém e atualiza a lista de **arquivos** .

### <a name="upload-blobs"></a>Carregar BLOBs

Em seguida, adicione o código para carregar arquivos no contêiner de armazenamento quando você pressionar o botão **selecionar e carregar arquivos** .

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

Esse código conecta o botão **selecionar e carregar arquivos** ao elemento oculto `file-input` . Dessa forma, o evento de `click` botão dispara o evento de `click` entrada de arquivo e exibe o seletor de arquivo. Depois de selecionar arquivos e fechar a caixa de diálogo, `input` o evento ocorre e `uploadFiles` a função é chamada. Essa função chama a função [uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/#uploadbrowserdatatoblockblob-aborter--blob---arraybuffer---arraybufferview--blockbloburl--iuploadtoblockbloboptions-) somente de navegador para cada arquivo selecionado. Cada chamada retorna uma promessa, que é adicionada a uma lista para que todos possam ser aguardados de uma só vez, fazendo com que os arquivos sejam carregados em paralelo.

### <a name="delete-blobs"></a>Eliminar blobs

Em seguida, adicione o código para excluir arquivos do contêiner de armazenamento quando você pressionar o botão **Excluir arquivos selecionados** .

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

Esse código chama a função [BlobURL. Delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) para remover cada arquivo selecionado na lista. Em seguida, ele `listFiles` chama a função mostrada anteriormente para atualizar o conteúdo da lista de **arquivos** .

### <a name="run-and-test-the-web-application"></a>Executar e testar o aplicativo Web

Neste ponto, você pode iniciar a página e experimentar para ter uma ideia de como o armazenamento de blob funciona. Se ocorrerem erros (por exemplo, quando você tentar listar arquivos antes de criar o contêiner), o painel **status** exibirá a mensagem de erro recebida. Você também pode definir pontos de interrupção no código JavaScript para examinar os valores retornados pelas APIs de armazenamento.

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados durante este guia de início rápido, vá para o [portal do Azure](https://portal.azure.com) e exclua o grupo de recursos que você criou na seção pré-requisitos.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um site simples que acessa o armazenamento de BLOBs do JavaScript baseado em navegador. Para saber como você pode hospedar um site em si no armazenamento de BLOBs, prossiga para o tutorial a seguir:

> [!div class="nextstepaction"]
> [Hospedar um site estático no armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
