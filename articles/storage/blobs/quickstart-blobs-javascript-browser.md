---
title: 'Quickstart: Azure Blob storage library v12 - JavaScript in a browser'
description: Neste arranque rápido, você aprende a usar a versão 12 da biblioteca de clientes de armazenamento Azure Blob para JavaScript num browser. Você cria um recipiente e um objeto no armazenamento Blob. Em seguida, aprende-se a listar todas as bolhas num recipiente. Finalmente, aprende-se a apagar bolhas e a apagar um recipiente.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 998d49e91d38a1f2fdc2503165ee99635e153027
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001903"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-a-browser"></a>Quickstart: Gerir blobs com JavaScript v12 SDK num browser

O armazenamento Azure Blob está otimizado para armazenar grandes quantidades de dados não estruturados. As bolhas são objetos que podem conter dados de texto ou binários, incluindo imagens, documentos, meios de streaming e dados de arquivo. Neste arranque rápido, aprende-se a gerir as bolhas utilizando o JavaScript num browser. Você vai carregar e listar bolhas, e você vai criar e apagar recipientes.

Recursos adicionais:

* [Documentação de referência da API](/javascript/api/@azure/storage-blob)
* [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Pacote (npm)](https://www.npmjs.com/package/@azure/storage-blob)
* [Amostras](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Pré-requisitos

* [Uma conta Azure com uma subscrição ativa](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Uma conta de armazenamento Azure](../common/storage-account-create.md)
* [Node.js](https://nodejs.org)
* [Código do Estúdio Visual da Microsoft](https://code.visualstudio.com)
* Uma extensão visual Studio Code para depurar o navegador, tais como:
    * [Debugger para o Microsoft Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge)
    * [Debugger para o Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)
    * [Debugger para Firefox](https://marketplace.visualstudio.com/items?itemName=firefox-devtools.vscode-firefox-debug)


[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento blob oferece três tipos de recursos:

* A conta de armazenamento
* Um recipiente na conta de armazenamento
* Uma bolha no recipiente

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento blob](./media/storage-blobs-introduction/blob1.png)

Neste arranque rápido, você usará as seguintes aulas de JavaScript para interagir com estes recursos:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): A `BlobServiceClient` classe permite-lhe manipular os recursos de armazenamento do Azure e os recipientes blob.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): A `ContainerClient` classe permite-lhe manipular recipientes de armazenamento Azure e suas bolhas.
* [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient): A `BlockBlobClient` classe permite-lhe manipular as bolhas de armazenamento do Azure.

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes de armazenamento Azure Blob v12 para JavaScript.

### <a name="create-a-cors-rule"></a>Criar uma regra CORS

Antes de a sua aplicação web poder aceder ao armazenamento de blob do cliente, tem de configurar a sua conta para permitir a [partilha de recursos de origem cruzada,](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)ou CORS.

No portal Azure, selecione a sua conta de armazenamento. Para definir uma nova regra CORS, navegue na secção **Definições** e selecione **CORS**. Neste início rápido, vai criar uma regra CORS aberta:

![Definições de CORS da Conta de Armazenamento de Blobs do Azure](media/quickstart-blobs-javascript-browser/azure-blob-storage-cors-settings.png)

A tabela seguinte descreve cada definição de CORS e explica os valores utilizados para definir a regra.

|Definição  |Valor  | Descrição |
|---------|---------|---------|
| **ORIGENS PERMITIDAS** | **\** _ | Aceita uma lista delimitada por vírgulas de domínios definidos como origens aceitáveis. Definir o valor como `_` permite que todos os domínios acedam à conta de armazenamento. |
| **MÉTODOS PERMITIDOS** | **EXCLUIR,** **GET,** **CABEÇA,** **FUSÃO,** **POST,** **OPÇÕES,** e **PUT** | Apresenta uma lista dos verbos HTTP que podem ser executados na conta de armazenamento. Para efeitos deste início rápido, selecione todas as opções disponíveis. |
| **CABEÇALHOS PERMITIDOS** | **\** _ | Define uma lista de cabeçalhos de pedidos (incluindo cabeçalhos com prefixo) que a conta de armazenamento permite. Definir o valor como `_` permite acesso a todos os cabeçalhos. |
| **CABEÇALHOS EXPOSTOS** | **\** _ | Apresenta uma lista dos cabeçalhos de resposta que a conta permite. Definir o valor como `_` permite à conta enviar qualquer cabeçalho. |
| **IDADE MÁXIMA** | **86400** | O tempo máximo de tempo que o navegador cache o pedido de OPÇÕES de pré-voo em segundos. Um valor de *86400* permite que a cache permaneça por um dia completo. |

Depois de preencher os campos com os valores desta tabela, clique no botão **Guardar.**

> [!IMPORTANT]
> Certifique-se de que quaisquer configurações que utilize na produção exponham a quantidade mínima de acesso necessária à sua conta de armazenamento para manter um acesso seguro. As definições de CORS descritas aqui são adequadas para um início rápido, porque definem uma política de segurança permissiva. No entanto, não são recomendadas para um contexto do mundo real.

### <a name="create-a-shared-access-signature"></a>Criar uma assinatura de acesso partilhado

A assinatura de acesso partilhado (SAS) é usada por código em execução no navegador para autorizar pedidos de armazenamento Azure Blob. Ao utilizar o SAS, o cliente pode autorizar o acesso aos recursos de armazenamento sem a chave de acesso da conta ou sem a cadeia de ligação. Para obter mais informações, veja [Using shared access signatures (SAS)](../common/storage-sas-overview.md) (Utilizar assinaturas de acesso partilhado [SAS]).

Siga estes passos para obter o SERVIÇO BLOB SAS URL:

1. No portal Azure, selecione a sua conta de armazenamento.
2. Navegue na secção **Definições** e **selecione assinatura de acesso partilhado**.
3. Desloque-se para baixo e clique no **botão De gerar SAS e fio de ligação.**
4. Percorra ainda mais e localize o campo **URL do serviço Blob SAS**
5. Clique no botão **Copy para clipboard** na extremidade extrema-direita do campo URL do **serviço Blob.**
6. Guarde o URL copiado em algum lugar para ser usado em um próximo passo.

### <a name="add-the-azure-blob-storage-client-library"></a>Adicione a biblioteca de clientes de armazenamento Azure Blob

No seu computador local, crie uma nova pasta chamada *azure-blobs-js-browser e abra-a* no Código do Estúdio Visual.

Selecione **Ver > Terminal** para abrir uma janela da consola dentro do Código do Estúdio Visual. Executar o seguinte comando Node.js Package Manager (npm) na janela do terminal para criar uma [package.jsno](https://docs.npmjs.com/files/package.json) ficheiro.

```console
npm init -y
```

O Azure SDK é composto por muitos pacotes separados. Pode escolher quais os pacotes de que necessita com base nos serviços que pretende utilizar. Executar o `npm` comando na janela do terminal para instalar a `@azure/storage-blob` embalagem.

```console
npm install --save @azure/storage-blob
```

#### <a name="bundle-the-azure-blob-storage-client-library"></a>Embrulhe a biblioteca de clientes de armazenamento Azure Blob

Para utilizar bibliotecas Azure SDK num site, converta o seu código para trabalhar dentro do navegador. Faz-se isto usando uma ferramenta chamada "bundler". A agregação leva o código JavaScript escrito usando convenções [ deNode.js](https://nodejs.org) e converte-o num formato que é entendido pelos navegadores. Este artigo de arranque rápido utiliza o [pacote de encomendas.](https://parceljs.org/)

Instale a parcela executando o seguinte `npm` comando na janela do terminal:

```console
npm install -g parcel-bundler
```

No Código do Estúdio Visual, abra a *package.jsno* ficheiro e adicione uma entre as entradas `browserlist` e `license` `dependencies` entradas. Isto `browserlist` visa a versão mais recente de três navegadores populares. O *package.js* completo no ficheiro deve agora ser assim:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/package.json" highlight="12-16":::

Guarde o *package.jsarquivado.*

### <a name="import-the-azure-blob-storage-client-library"></a>Importe a biblioteca de clientes de armazenamento Azure Blob

Para utilizar bibliotecas Azure SDK dentro do JavaScript, importe o `@azure/storage-blob` pacote. Crie um novo ficheiro no Código do Estúdio Visual contendo o seguinte código JavaScript.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ImportLibrary":::

Guarde o ficheiro como *index.js* no diretório do *navegador azure-blobs-js.*

### <a name="implement-the-html-page"></a>Implementar a página HTML

Crie um novo ficheiro no Código do Estúdio Visual e adicione o seguinte código HTML.

:::code language="html" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.html":::

Guarde o ficheiro como *index.html* na pasta *azure-blobs-js-browser.*

## <a name="code-examples"></a>Exemplos de código

O código de exemplo mostra-lhe como realizar as seguintes tarefas com a biblioteca de clientes de armazenamento Azure Blob para JavaScript:

* [Declarar campos para elementos de UI](#declare-fields-for-ui-elements)
* [Adicione a informação da sua conta de armazenamento](#add-your-storage-account-info)
* [Criar objetos de cliente](#create-client-objects)
* [Criar e apagar um recipiente de armazenamento](#create-and-delete-a-storage-container)
* [Listar blobs](#list-blobs)
* [Carregar bolhas](#upload-blobs)
* [Eliminar blobs](#delete-blobs)

Vai executar o código depois de adicionar todos os cortes ao ficheiro *index.js.*

### <a name="declare-fields-for-ui-elements"></a>Declarar campos para elementos de UI

Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="JavaScript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeclareVariables":::

Guarde o ficheiro *index.js.*

Este código declara campos para cada elemento HTML e implementa uma `reportStatus` função para exibir a saída.

Nas seguintes secções, adicione cada novo bloco de código JavaScript após o bloco anterior.

### <a name="add-your-storage-account-info"></a>Adicione a informação da sua conta de armazenamento

Adicione código para aceder à sua conta de armazenamento. Substitua o espaço reservado pelo seu SERVIÇO BLOB SAS URL que gerou anteriormente. Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_StorageAcctInfo":::

Guarde o ficheiro *index.js.*

### <a name="create-client-objects"></a>Criar objetos de cliente

Criar objetos [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) e [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) para interagir com o serviço de armazenamento Azure Blob. Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateClientObjects":::

Guarde o ficheiro *index.js.*

### <a name="create-and-delete-a-storage-container"></a>Criar e apagar um recipiente de armazenamento

Crie e elimine o recipiente de armazenamento quando clicar no botão correspondente na página web. Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateDeleteContainer":::

Guarde o ficheiro *index.js.*

### <a name="list-blobs"></a>Listar blobs

Enuite o conteúdo do recipiente de armazenamento quando clicar no botão **Ficheiros Lista.** Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ListBlobs":::

Guarde o ficheiro *index.js.*

Este código chama a função [ContainerClient.listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) e, em seguida, utiliza um iterador para recuperar o nome de cada [BlobItem](/javascript/api/@azure/storage-blob/blobitem) devolvido. Para cada `BlobItem` um, atualiza a lista **de Ficheiros** com o valor da propriedade [do nome.](/javascript/api/@azure/storage-blob/blobitem#name)

### <a name="upload-blobs"></a>Carregar bolhas

Faça o upload de ficheiros para o recipiente de armazenamento quando clicar no botão **'Selecionar' e carregar ficheiros.** Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_UploadBlobs":::

Guarde o ficheiro *index.js.*

Este código liga o botão **'Selecionar' e carregar ficheiros** ao `file-input` elemento oculto. O evento do botão `click` aciona o evento de entrada de `click` ficheiros e exibe o selecionador de ficheiros. Depois de selecionar ficheiros e fechar a caixa de diálogo, o `input` evento ocorre e a `uploadFiles` função é chamada. Esta função cria um objeto [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) e, em seguida, chama a função [de uploadBrowserData apenas](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) para o navegador para cada ficheiro selecionado. Cada chamada retorna a `Promise` . Cada `Promise` um é adicionado a uma lista para que todos possam ser aguardados em conjunto, fazendo com que os ficheiros sejam carregados em paralelo.

### <a name="delete-blobs"></a>Eliminar blobs

Elimine os ficheiros do recipiente de armazenamento quando clicar no botão **Eliminar ficheiros selecionados.** Adicione o seguinte código ao fim do ficheiro *index.js.*

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeleteBlobs":::

Guarde o ficheiro *index.js.*

Este código chama a função [ContainerClient.deleteBlob](/javascript/api/@azure/storage-blob/containerclient#deleteblob-string--blobdeleteoptions-) para remover cada ficheiro selecionado na lista. Em seguida, chama a `listFiles` função mostrada anteriormente para refrescar o conteúdo da lista **de Ficheiros.**

## <a name="run-the-code"></a>Executar o código

Para executar o código dentro do depurador Visual Studio Code, configuure o *launch.jsno* ficheiro para o seu navegador.

### <a name="configure-the-debugger"></a>Configure o depurador

Para configurar a extensão do depurgger no Código do Estúdio Visual:

1. Selecione **executar > adicionar configuração**
2. Selecione **Edge**, **Chrome** ou **Firefox**, dependendo da extensão instalada na secção [Pré-requisitos](#prerequisites) anteriormente.

Adicionar uma nova configuração cria uma *launch.jsno* ficheiro e abre-a no editor. Modifique a *launch.jsno* ficheiro de modo a que o valor seja , como `url` mostrado `http://localhost:1234/index.html` aqui:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/.vscode/launch.json" highlight="11":::

Depois de atualizar, guarde as *launch.jsno* ficheiro. Esta configuração diz ao Código do Estúdio Visual qual o navegador a abrir e qual URL carregar.

### <a name="launch-the-web-server"></a>Lançar o servidor web

Para lançar o servidor web de desenvolvimento local, selecione **Ver > Terminal** para abrir uma janela de consola dentro do Código do Estúdio Visual e, em seguida, insira o seguinte comando.

```console
parcel index.html
```

A encomenda agrega o seu código e inicia um servidor de desenvolvimento local para a sua página em `http://localhost:1234/index.html` . As alterações que fizer para *index.js* serão automaticamente construídas e refletidas no servidor de desenvolvimento sempre que guardar o ficheiro.

Se receber uma mensagem que diga que **a porta 1234 configurada não pode ser utilizada,** pode alterar a porta executando o comando `parcel -p <port#> index.html` . No *launch.jsem* arquivo, atualize a porta no caminho URL para combinar.

### <a name="start-debugging"></a>Comece a depurar

Faça a página no depurar e sinta como funciona o armazenamento de bolhas. Se ocorrerem erros, o **painel de estado** na página web apresentará a mensagem de erro recebida.

Para abrir *index.html* no navegador com o depurante visual Studio Code anexado, selecione Executar > Iniciar **a depurar** ou prima F5 no Código do Estúdio Visual.

### <a name="use-the-web-app"></a>Use a aplicação web

No [portal Azure,](https://portal.azure.com)pode verificar os resultados das chamadas da API à medida que segue os passos abaixo.

#### <a name="step-1---create-a-container"></a>Passo 1 - Criar um recipiente

1. Na aplicação web, selecione **Criar o recipiente.** O estado indica que um contentor foi criado.
2. Para verificar no portal Azure, selecione a sua conta de armazenamento. Em **Serviço Blob**, selecione **Contentores**. Verifique se o novo recipiente aparece. (Pode ter de selecionar **Refresh**.)

#### <a name="step-2---upload-a-blob-to-the-container"></a>Passo 2 - Carre faça o upload de uma bolha para o recipiente

1. No seu computador local, crie e guarde um ficheiro de teste, como *test.txt*.
2. Na aplicação web, clique em **Selecionar e carregar ficheiros.**
3. Navegue no seu ficheiro de teste e, em seguida, **selecione Abrir**. O estado indica que o ficheiro foi carregado e a lista de ficheiros foi recuperada.
4. No portal Azure, selecione o nome do novo recipiente que criou anteriormente. Verifique se o ficheiro de teste aparece.

#### <a name="step-3---delete-the-blob"></a>Passo 3 - Apagar a bolha

1. Na aplicação web, em **Files,** selecione o ficheiro de teste.
2. Selecione **Eliminar ficheiros selecionados**. O estado indica que o ficheiro foi apagado e que o contentor não contém ficheiros.
3. No portal Azure, selecione **Refresh**. Verifique se não vê **nenhuma bolha encontrada.**

#### <a name="step-4---delete-the-container"></a>Passo 4 - Apagar o recipiente

1. Na aplicação web, selecione **Delete container**. O estado indica que o contentor foi apagado.
2. No portal Azure, selecione o **\<account-name\> / Os recipientes** ligam-se na parte superior esquerda do painel do portal.
3. Selecione **Refresh**. O novo contentor desaparece.
4. Feche a aplicação web.

### <a name="clean-up-resources"></a>Limpar os recursos

Clique na consola **Terminal** no Código do Estúdio Visual e prima CTRL+C para parar o servidor web.

Para limpar os recursos criados durante este arranque rápido, vá ao [portal Azure](https://portal.azure.com) e elimine o grupo de recursos que criou na secção [Pré-Requisitos.](#prerequisites)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a carregar, listar e apagar bolhas usando JavaScript. Também aprendeu a criar e apagar um recipiente de armazenamento de bolhas.

Para tutoriais, amostras, quickstarts e outra documentação, visite:

> [!div class="nextstepaction"]
> [Azure para documentação JavaScript](/azure/developer/javascript/)

* Para saber mais, consulte a biblioteca de [clientes de armazenamento Azure Blob para JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
* Para ver as aplicações de amostra de armazenamento Blob, continue até a [biblioteca de clientes de armazenamento Azure Blob v12 Amostras JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).