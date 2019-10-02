---
title: 'Início rápido: QnA Maker com APIs REST para node. js'
titleSuffix: Azure Cognitive Services
description: Introdução às APIs REST do QnA Maker para node. js. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 4393609bf426c6ae99c48a5d84162526aeff6fb7
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803523"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Início rápido: QnA Maker APIs REST para node. js

Introdução às APIs REST do QnA Maker para node. js. Siga estas etapas para experimentar o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos. 

Use as APIs REST do QnA Maker para o Node. js para:

* Criar uma base de dados de conhecimento
* Substituir uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Eliminar uma base de dados de conhecimento
* Baixar uma base de dados de conhecimento
* Obter o status de uma operação

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)@no__t exemplos de 1[node. js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [node. js](https://nodejs.org).
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **início rápido** para seu recurso no portal do Azure.

## <a name="setting-up"></a>Configurando

### <a name="create-a-qna-maker-azure-resource"></a>Criar um QnA Maker recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para QnA Maker usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. 

Depois de obter uma chave do recurso, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o recurso, `QNAMAKER_RESOURCE_KEY` chamado `QNAMAKER_AUTHORING_ENDPOINT`e. Use os valores de chave e ponto de extremidade encontrados na página **início rápido** do recurso na portal do Azure.

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init -y` para criar um arquivo de nó `package.json`. 

```console
npm init -y
```

Adicione os pacotes `reqeuestretry` e `request` NPM:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com as APIs REST do QnA Maker para node. js:

* [Criar uma base de dados de conhecimento](#create-a-knowledge-base)
* [Substituir uma base de dados de conhecimento](#replace-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Excluir uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Baixar uma base de dados de conhecimento](#download-the-knowledge-base)
* [Obter o status de uma operação](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Adicionar as dependências



Crie um arquivo chamado `rest-apis.js` e adicione a instrução _Requires_ a seguir para fazer solicitações HTTP. 

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Adicionar informações de recursos do Azure

Crie variáveis para o ponto de extremidade e a chave do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de dados de conhecimento armazena pares de perguntas e respostas, criados a partir de um objeto JSON de:

* **Conteúdo editorial**. 
* **Arquivos** -arquivos locais que não exigem nenhuma permissão. 
* **URLs** -URLs disponíveis publicamente.

Use a [API REST para criar uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). 

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Substituir uma base de dados de conhecimento

Use a [API REST para substituir uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de dados de conhecimento. Esse processo torna a base de dados de conhecimento disponível de um ponto de extremidade de previsão de consulta HTTP.

Use a [API REST para publicar uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>Baixar a base de dados de conhecimento 

Use a [API REST para baixar uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Quando terminar de usar a base de dados de conhecimento, exclua-a.

Use a [API REST para excluir uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Obter o status de uma operação

Processos de execução longa, como o processo de criação, retornam uma ID de operação, que precisa ser verificada com uma chamada à API REST separada. Essa função usa o corpo da resposta de criação. A chave importante é a `operationState`, que determina se você precisa continuar a sondagem.

Use a [API REST para monitorar operações em uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo com `node rest-apis.js` o comando do diretório do aplicativo.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Tutorial: Criar e responder a um KB](../tutorials/create-publish-query-in-portal.md)

* [O que é o API de QnA Maker?](../Overview/overview.md)
* [Editar uma base de dados de conhecimento](../how-to/edit-knowledge-base.md)
* [Obter análise de uso](../how-to/get-analytics-knowledge-base.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).