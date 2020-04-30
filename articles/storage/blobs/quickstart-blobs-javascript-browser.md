---
title: 'Quickstart: Biblioteca de armazenamento Azure Blob v12 - JavaScript em um navegador'
description: Neste arranque rápido, aprende a usar a versão 12 da biblioteca de clientes de armazenamento Azure Blob para javaScript num browser. Cria-se um recipiente e um objeto no armazenamento blob. Em seguida, aprende-se a listar todas as bolhas num contentor. Finalmente, aprende-se a apagar bolhas e a apagar um recipiente.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/18/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 3b23eac4ab50401e68e17064d0964dacc3c17d32
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120507"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-a-browser"></a>Quickstart: Gerir bolhas com JavaScript v12 SDK em um navegador

O armazenamento da Blob Azure está otimizado para armazenar grandes quantidades de dados não estruturados. As bolhas são objetos que podem conter texto ou dados binários, incluindo imagens, documentos, meios de streaming e dados de arquivo. Neste arranque rápido, aprende-se a gerir bolhas utilizando o JavaScript num browser. Você vai carregar e listar bolhas, e você vai criar e apagar recipientes.

[Documentação de](/javascript/api/@azure/storage-blob) | referência API[Biblioteca Código fonte](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [Pacote (npm)](https://www.npmjs.com/package/@azure/storage-blob) | [Amostras](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

> [!NOTE]
> Para começar com a versão SDK anterior, consulte [Quickstart: Gerencie as bolhas com JavaScript v10 SDK em Node.js](storage-quickstart-blobs-nodejs-legacy.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Uma conta Azure com uma subscrição ativa](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Uma conta de armazenamento azure](../common/storage-account-create.md)
* [Node.js](https://nodejs.org)
* [Código do Estúdio Visual da Microsoft](https://code.visualstudio.com)
* Uma extensão de Código de Estúdio Visual para depuração do navegador, tais como:
    * [Debugger para Microsoft Edge](vscode:extension/msjsdiag.debugger-for-edge)
    * [Debugger para O Cromo](vscode:extension/msjsdiag.debugger-for-chrome)
    * [Debugger para Firefox](vscode:extension/firefox-devtools.vscode-firefox-debug)


[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento blob oferece três tipos de recursos:

* A conta de armazenamento
* Um recipiente na conta de armazenamento
* Uma bolha no recipiente

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento blob](./media/storage-blobs-introduction/blob1.png)

Neste arranque rápido, utilizará as seguintes aulas javaScript para interagir com estes recursos:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): `BlobServiceClient` A classe permite-lhe manipular os recursos de Armazenamento Azure e os recipientes blob.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): `ContainerClient` A classe permite-lhe manipular os recipientes de armazenamento Azure e as suas bolhas.
* [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient): `BlockBlobClient` A classe permite-lhe manipular bolhas de Armazenamento Azure.

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes de armazenamento Azure Blob v12 para javaScript.

### <a name="create-a-cors-rule"></a>Criar uma regra CORS

Antes de a sua aplicação web poder aceder ao armazenamento de blob do cliente, deve configurar a sua conta para permitir a [partilha de recursos de origem cruzada](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services), ou CORS.

No portal Azure, selecione a sua conta de armazenamento. Para definir uma nova regra CORS, navegue para a secção **Definições** e selecione **CORS**. Neste início rápido, vai criar uma regra CORS aberta:

![Definições de CORS da Conta de Armazenamento de Blobs do Azure](media/quickstart-blobs-javascript-browser/azure-blob-storage-cors-settings.png)

A tabela seguinte descreve cada definição de CORS e explica os valores utilizados para definir a regra.

|Definição  |Valor  | Descrição |
|---------|---------|---------|
| **ORIGENS PERMITIDAS** | **\*** | Aceita uma lista delimitada por vírgulas de domínios definidos como origens aceitáveis. Definir o valor como `*` permite que todos os domínios acedam à conta de armazenamento. |
| **MÉTODOS PERMITIDOS** | **DELETE,** **GET,** **HEAD,** **MERGE,** **POST,** **OPTIONS,** AND **PUT** | Apresenta uma lista dos verbos HTTP que podem ser executados na conta de armazenamento. Para efeitos deste início rápido, selecione todas as opções disponíveis. |
| **CABEÇALHOS PERMITIDOS** | **\*** | Define uma lista de cabeçalhos de pedidos (incluindo cabeçalhos com prefixo) que a conta de armazenamento permite. Definir o valor como `*` permite acesso a todos os cabeçalhos. |
| **CABEÇALHOS EXPOSTOS** | **\*** | Apresenta uma lista dos cabeçalhos de resposta que a conta permite. Definir o valor como `*` permite à conta enviar qualquer cabeçalho. |
| **IDADE MÁXIMA** | **86400** | O tempo máximo que o navegador caches o pedido de OPÇÕES de pré-voo em segundos. Um valor de *86400* permite que a cache permaneça por um dia completo. |

Depois de preencher os campos com os valores desta tabela, clique no botão **Guardar.**

> [!IMPORTANT]
> Certifique-se de que quaisquer configurações que utilize na produção expõem a quantidade mínima de acesso necessária à sua conta de armazenamento para manter um acesso seguro. As definições de CORS descritas aqui são adequadas para um início rápido, porque definem uma política de segurança permissiva. No entanto, não são recomendadas para um contexto do mundo real.

### <a name="create-a-shared-access-signature"></a>Criar uma assinatura de acesso partilhado

A assinatura de acesso partilhado (SAS) é utilizada por código que corre no navegador para autorizar pedidos de armazenamento do Azure Blob. Ao utilizar o SAS, o cliente pode autorizar o acesso aos recursos de armazenamento sem a chave de acesso da conta ou sem a cadeia de ligação. Para obter mais informações, veja [Using shared access signatures (SAS)](../common/storage-sas-overview.md) (Utilizar assinaturas de acesso partilhado [SAS]).

Siga estes passos para obter o URL SAS do serviço Blob:

1. No portal Azure, selecione a sua conta de armazenamento.
2. Navegue na secção **Definições** e selecione assinatura de **acesso partilhado**.
3. Desloque-se para baixo e clique no Botão **de linha Generate SAS e ligação.**
4. Percorra mais abaixo e localize o campo **DEURL do serviço Blob SAS**
5. Clique na **Cópia para botão de clipboard** na extremidade extrema-direita do campo **URL do serviço Blob SAS.**
6. Guarde a URL copiada algures para ser utilizada num próximo passo.

### <a name="add-the-azure-blob-storage-client-library"></a>Adicione a biblioteca de clientes de armazenamento Azure Blob

No seu computador local, crie uma nova pasta chamada *azure-blobs-js-browser e abra-a* no Visual Studio Code.

Selecione **Ver > Terminal** para abrir uma janela de consola dentro do Código do Estúdio Visual. Execute o comando seguinte Node.js Package Manager (npm) na janela do terminal para criar um ficheiro [package.json.](https://docs.npmjs.com/files/package.json)

```console
npm init -y
```

O Azure SDK é composto por muitos pacotes separados. Pode escolher quais os pacotes que precisa com base nos serviços que pretende utilizar. Executar `npm` o comando seguinte na `@azure/storage-blob` janela do terminal para instalar a embalagem.

```console
npm install --save @azure/storage-blob
```

#### <a name="bundle-the-azure-blob-storage-client-library"></a>Agregar a biblioteca de clientes de armazenamento Azure Blob

Para utilizar bibliotecas Azure SDK num site, converta o seu código para funcionar dentro do navegador. Faz-se isto usando uma ferramenta chamada bundler. A agregação leva o código JavaScript escrito usando convenções [nonóios](https://nodejs.org) e converte-o num formato que é entendido pelos navegadores. Este artigo de arranque rápido usa o bundler [Parcel.](https://parceljs.org/)

Instale parcela com `npm` o seguinte comando na janela do terminal:

```console
npm install -g parcel-bundler
```

No Visual Studio Code, abra o ficheiro *package.json* e adicione um `browserlist` entre as `license` entradas e `dependencies` as entradas. Isto `browserlist` visa a versão mais recente de três navegadores populares. O ficheiro completo *do pacote.json* deve agora ser assim:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/package.json" highlight="12-16":::

Guarde o ficheiro *package.json.*

### <a name="import-the-azure-blob-storage-client-library"></a>Importar a biblioteca de clientes de armazenamento Azure Blob

Para utilizar bibliotecas Azure SDK `@azure/storage-blob` dentro do JavaScript, importe o pacote. Crie um novo ficheiro no Código do Estúdio Visual contendo o seguinte código JavaScript.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ImportLibrary":::

Guarde o ficheiro como *index.js* no diretório *de navegador azure-blobs-js.*

### <a name="implement-the-html-page"></a>Implementar a página HTML

Crie um novo ficheiro no Código do Estúdio Visual e adicione o seguinte código HTML.

:::code language="html" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.html":::

Guarde o ficheiro como *index.html* na pasta *azure-blobs-js-browser.*

## <a name="code-examples"></a>Exemplos de código

O código de exemplo mostra-lhe como realizar as seguintes tarefas com a biblioteca de clientes de armazenamento Azure Blob para JavaScript:

* [Declarar campos para elementos ui](#declare-fields-for-ui-elements)
* [Adicione as informações da sua conta de armazenamento](#add-your-storage-account-info)
* [Criar objetos de cliente](#create-client-objects)
* [Criar e eliminar um recipiente de armazenamento](#create-and-delete-a-storage-container)
* [Listar blobs](#list-blobs)
* [Carregar bolhas](#upload-blobs)
* [Eliminar blobs](#delete-blobs)

Executará o código depois de adicionar todos os cortes ao ficheiro *index.js.*

### <a name="declare-fields-for-ui-elements"></a>Declarar campos para elementos ui

Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="JavaScript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeclareVariables":::

Guarde o ficheiro *index.js.*

Este código declara campos para cada elemento `reportStatus` HTML e implementa uma função para visualizar a saída.

Nas seguintes secções, adicione cada novo bloco de código JavaScript após o bloco anterior.

### <a name="add-your-storage-account-info"></a>Adicione as informações da sua conta de armazenamento

Adicione código para aceder à sua conta de armazenamento. Substitua o espaço reservado pelo seu URL SAS do serviço Blob que gerou anteriormente. Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_StorageAcctInfo":::

Guarde o ficheiro *index.js.*

### <a name="create-client-objects"></a>Criar objetos de cliente

Crie objetos [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) e [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) para interagir com o serviço de armazenamento Azure Blob. Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateClientObjects":::

Guarde o ficheiro *index.js.*

### <a name="create-and-delete-a-storage-container"></a>Criar e eliminar um recipiente de armazenamento

Crie e elimine o recipiente de armazenamento quando clicar no botão correspondente na página web. Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateDeleteContainer":::

Guarde o ficheiro *index.js.*

### <a name="list-blobs"></a>Listar blobs

Enumera o conteúdo do recipiente de armazenamento quando clicar no botão **'Ficheiros lista'.** Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ListBlobs":::

Guarde o ficheiro *index.js.*

Este código chama a função [ContainerClient.listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) e, em seguida, utiliza um iterator para recuperar o nome de cada [BlobItem](/javascript/api/@azure/storage-blob/blobitem) devolvido. Para `BlobItem`cada um, atualiza a lista **de Ficheiros** com o valor de propriedade do [nome.](/javascript/api/@azure/storage-blob/blobitem#name)

### <a name="upload-blobs"></a>Carregar bolhas

Faça upload de ficheiros para o recipiente de armazenamento quando clicar no botão **Select e carregar ficheiros.** Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_UploadBlobs":::

Guarde o ficheiro *index.js.*

Este código liga o botão Select `file-input` e carregar **ficheiros** ao elemento oculto. O `click` evento do botão `click` aciona o evento de entrada de ficheiros e exibe o apanhador de ficheiros. Depois de selecionar ficheiros e fechar `input` a caixa `uploadFiles` de diálogo, o evento ocorre e a função é chamada. Esta função cria um objeto [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) e, em seguida, liga para a função [de uploadBrowserData](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) apenas para cada ficheiro que selecionou. Cada chamada `Promise`devolve um . Cada `Promise` um é adicionado a uma lista para que todos possam ser aguardados em conjunto, fazendo com que os ficheiros carreguem em paralelo.

### <a name="delete-blobs"></a>Eliminar blobs

Elimine ficheiros do recipiente de armazenamento quando clicar no botão **apagar ficheiros selecionados.** Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeleteBlobs":::

Guarde o ficheiro *index.js.*

Este código chama a função [ContainerClient.deleteBlob](/javascript/api/@azure/storage-blob/containerclient#deleteblob-string--blobdeleteoptions-) para remover cada ficheiro selecionado na lista. Em seguida, `listFiles` chama a função mostrada anteriormente para refrescar o conteúdo da lista **de Ficheiros.**

## <a name="run-the-code"></a>Executar o código

Para executar o código dentro do código do estúdio visual, configure o ficheiro *launch.json* para o seu navegador.

### <a name="configure-the-debugger"></a>Configure o debugger

Para configurar a extensão de debugger no Código do Estúdio Visual:

1. Selecione **executar > adicionar configuração**
2. Selecione **Edge,** **Chrome,** ou **Firefox,** dependendo da extensão que instalou na secção [Pré-requisitos](#prerequisites) anteriormente.

Adicionar uma nova configuração cria um ficheiro *launch.json* e abre-o no editor. Modifique o ficheiro *launch.json* de modo a que o `url` valor seja, `http://localhost:1234/index.html`como mostrado aqui:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/.vscode/launch.json" highlight="11":::

Depois de atualizar, guarde o ficheiro *launch.json.* Esta configuração diz ao Visual Studio Code qual navegador abrir e qual URL para carregar.

### <a name="launch-the-web-server"></a>Lançar o servidor web

Para lançar o servidor web de desenvolvimento local, selecione **'Ver > Terminal'** para abrir uma janela de consola dentro do Código visual do Estúdio e, em seguida, introduza o seguinte comando.

```console
parcel index.html
```

Parcel embala o seu código e inicia `http://localhost:1234/index.html`um servidor de desenvolvimento local para a sua página em . As alterações que fizer ao *index.js* serão automaticamente construídas e refletidas no servidor de desenvolvimento sempre que guardar o ficheiro.

Se receber uma mensagem que diga que a **porta configurada 1234 não pode ser utilizada,** pode alterar a porta executando o comando `parcel -p <port#> index.html`. No ficheiro *launch.json,* atualize a porta no caminho URL para combinar.

### <a name="start-debugging"></a>Comece a depurar

Faça a página no debugger e sinta como o armazenamento de bolhas funciona. Se ocorrerem erros, o painel **'Status'** na página web apresentará a mensagem de erro recebida.

Para abrir o *index.html* no navegador com o debugger visual Studio Code anexado, selecione **Run > Start Debugging** ou prima F5 no Código do Estúdio Visual.

### <a name="use-the-web-app"></a>Use a aplicação web

No [portal Azure,](https://portal.azure.com)pode verificar os resultados das chamadas API à medida que segue os passos abaixo.

#### <a name="step-1---create-a-container"></a>Passo 1 - Criar um recipiente

1. Na aplicação web, selecione **Criar recipiente**. O estado indica que foi criado um contentor.
2. Para verificar no portal Azure, selecione a sua conta de armazenamento. Sob **o serviço Blob,** selecione **Recipientes**. Verifique se o novo recipiente aparece. (Pode ser necessário selecionar **Refresh**.)

#### <a name="step-2---upload-a-blob-to-the-container"></a>Passo 2 - Faça upload de uma bolha para o recipiente

1. No seu computador local, crie e guarde um ficheiro de teste, como *test.txt*.
2. Na aplicação web, clique em **Selecionar e carregar ficheiros**.
3. Navegue no seu ficheiro de teste e, em seguida, selecione **Open**. O estado indica que o ficheiro foi carregado e a lista de ficheiros foi recuperada.
4. No portal Azure, selecione o nome do novo recipiente que criou anteriormente. Verifique se o ficheiro de teste aparece.

#### <a name="step-3---delete-the-blob"></a>Passo 3 - Apagar a bolha

1. Na aplicação web, em **Ficheiros,** selecione o ficheiro de teste.
2. **Selecione Eliminar ficheiros selecionados**. O estado indica que o ficheiro foi apagado e que o recipiente não contém ficheiros.
3. No portal Azure, selecione **Refresh**. Verifique se não vê **bolhas encontradas.**

#### <a name="step-4---delete-the-container"></a>Passo 4 - Apagar o recipiente

1. Na aplicação web, selecione eliminar o **recipiente**. O estado indica que o recipiente foi apagado.
2. No portal Azure, selecione o ** \<nome\> da conta [ Os recipientes** ligam-se à parte superior esquerda do painel do portal.
3. Selecione **Refresh**. O novo contentor desaparece.
4. Feche a aplicação web.

### <a name="clean-up-resources"></a>Limpar recursos

Clique na consola **Terminal** em Código de Estúdio Visual e prima CTRL+C para parar o servidor web.

Para limpar os recursos criados durante este arranque rápido, vá ao [portal Azure](https://portal.azure.com) e elimine o grupo de recursos que criou na secção [Pré-requisitos.](#prerequisites)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a carregar, listar e eliminar bolhas usando o JavaScript. Também aprendeu a criar e eliminar um recipiente de armazenamento de bolhas.

Para tutoriais, amostras, quickstarts e outra documentação, visite:

> [!div class="nextstepaction"]
> [Azure para documentação JavaScript](/azure/javascript/)

* Para saber mais, consulte a biblioteca de clientes de [armazenamento Azure Blob para JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
* Para ver aplicações de amostrade armazenamento Blob, continue a armazenar amostras [de clientes de armazenamento Azure Blob v12 JavaScript.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)
