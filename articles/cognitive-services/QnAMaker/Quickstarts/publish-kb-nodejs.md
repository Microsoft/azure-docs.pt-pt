---
title: 'Quickstart: QnA Maker com APIs REST para Node.js'
description: Este quickstart mostra como começar com as APIs DE REST do Criador QnA para Node.js. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-js
ms.topic: how-to
ms.openlocfilehash: 645f9ffb1d907eb576f5414b593ef57679d26daa
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351151"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Quickstart: QnA Maker REST APIs para Node.js

Começa com as APIs de REST do Criador QnA para Node.js. Siga estes passos para experimentar o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos.

Utilize as APIs REST DO Fabricante QNA para Node.js:

* Criar uma base de dados de conhecimento
* Substitua uma base de conhecimento
* Publicar uma base de dados de conhecimento
* Eliminar uma base de dados de conhecimento
* Descarregue uma base de conhecimento
* Obter o estado de uma operação

[Documentação de referência](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [amostras deNode.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [Node.js. ](https://nodejs.org)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.

## <a name="setting-up"></a>Configuração

### <a name="create-a-qna-maker-azure-resource"></a>Criar um recurso QnA Maker Azure

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para o QnA Maker utilizando o [portal Azure](../../cognitive-services-apis-create-account.md) ou [O CLI Azure](../../cognitive-services-apis-create-account-cli.md) na sua máquina local.

Depois de obter uma chave do seu recurso, [crie variáveis ambientais](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para o recurso, nomeado `QNAMAKER_RESOURCE_KEY` e `QNAMAKER_AUTHORING_ENDPOINT` . Utilize os valores de chave e ponto final encontrados na página **Quickstart** do Recurso no portal Azure.

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela.

```console
mkdir myapp && cd myapp
```

Executar o `npm init -y` comando para criar um `package.json` nó.

```console
npm init -y
```

Adicione os `reqeuestretry` pacotes e `request` NPM:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer o seguinte com as APIs DO QNA Maker REST para Node.js:

* [Criar uma base de conhecimento](#create-a-knowledge-base)
* [Substitua uma base de conhecimento](#replace-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Eliminar uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Descarregue uma base de conhecimento](#download-the-knowledge-base)
* [Obter o estado de uma operação](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Adicione as dependências

Crie um ficheiro com o nome `rest-apis.js` e adicione as seguintes dependências.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="dependencies":::

## <a name="add-utility-functions"></a>Adicionar funções de utilidade

Adicione as seguintes funções de utilidade.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="utility":::

## <a name="add-azure-resource-information"></a>Adicionar informações sobre recursos Azure

Crie variáveis para o ponto final e chave Azure do seu recurso. Se criou a variável ambiental depois de ter lançado a aplicação, terá de fechar e reabrir o editor, o IDE ou a shell que a executa para aceder à variável.

Definir os seguintes valores ambientais:

* `QNAMAKER_RESOURCE_KEY`- A **chave** é uma cadeia de 32 caracteres e está disponível no portal Azure, no recurso QnA Maker, na página **de arranque Quick.** Isto não é o mesmo que a chave final de previsão.
* `QNAMAKER_AUTHORING_ENDPOINT` - O seu ponto final de autoria, no formato `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` de, inclui o seu **nome de recurso**. Este não é o mesmo URL usado para consultar o ponto final de previsão.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="authorization":::

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de conhecimento armazena pares de perguntas e respostas, criados a partir de um objeto JSON de:

* **Conteúdo editorial.**
* **Ficheiros** - ficheiros locais que não requerem permissões.
* **URLs** - URLs publicamente disponíveis.

Utilize a [API REST para criar uma base de conhecimento.](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="createKb":::

## <a name="replace-a-knowledge-base"></a>Substitua uma base de conhecimento

Utilize a [API REST para substituir uma base de conhecimento.](/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="replaceKb":::

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de conhecimentos. Este processo disponibiliza a base de conhecimento a partir de um ponto final de previsão de consulta HTTP.

Utilize a [API REST para publicar uma base de conhecimento.](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="publishKb":::

## <a name="download-the-knowledge-base"></a>Descarregue a base de conhecimentos

Utilize a [API REST para descarregar uma base de conhecimentos.](/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="downloadKb":::

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Quando terminar de usar a base de conhecimento, elimine-a.

Utilize a [API REST para eliminar uma base de conhecimento.](/rest/api/cognitiveservices/qnamaker/knowledgebase/delete)

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="deleteKb":::

## <a name="get-status-of-an-operation"></a>Obter o estado de uma operação

Processos de longo prazo, como o processo de criação, devolvem um ID de operação, que precisa de ser verificado com uma chamada de API rest separada. Esta função requer o corpo da resposta de criar. A chave importante é o `operationState` , que determina se precisa de continuar as sondagens.

Utilize a [API REST para monitorizar as operações numa base de conhecimento.](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="operationDetails":::

## <a name="add-main-method"></a>Adicione o método principal

Adicione o seguinte `main` método.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="main":::

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com `node rest-apis.js` o comando do seu diretório de candidaturas.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Tutorial: Criar e responder a um KB](./create-publish-knowledge-base.md)

* [O que é a API do Fabricante QNA?](../Overview/overview.md)
* [Editar uma base de dados de conhecimento](../how-to/edit-knowledge-base.md)
* [Obtenha análises de utilização](../how-to/get-analytics-knowledge-base.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).