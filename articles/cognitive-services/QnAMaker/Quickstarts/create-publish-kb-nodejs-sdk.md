---
title: 'Início rápido: Biblioteca de cliente QnA Maker para node. js'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de cliente QnA Maker para node. js. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: ab3fa02f7285fdbde918f9e365d1ee96ba5e71a1
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802995"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>Início rápido: Biblioteca de cliente QnA Maker para node. js

Introdução à biblioteca de cliente QnA Maker para node. js. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos. 

Use a biblioteca de cliente QnA Maker para node. js para:

* Criar uma base de dados de conhecimento 
* Gerenciar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento

[](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics) | Exemplos de NPM | [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)[](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) [](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)donode.jsdabibliotecadedocumentaçãodereferênciadopacotedecódigo-fonte | 

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [node. js](https://nodejs.org).

## <a name="setting-up"></a>Configurando

### <a name="create-a-qna-maker-azure-resource"></a>Criar um QnA Maker recurso do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para QnA Maker usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. 

Depois de obter uma chave do recurso, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o recurso, `QNAMAKER_SUBSCRIPTION_KEY` chamado `QNAMAKER_HOST`e. Use os valores de chave e ponto de extremidade encontrados nas páginas de **visão geral** e **chaves** do recurso no portal do Azure.

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o `npm init -y` comando para criar um aplicativo de nó com `package.json` um arquivo. 

```console
npm init -y
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Instale os `ms-rest-azure` pacotes `azure-cognitiveservices-qnamaker` e NPM:

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

O arquivo do `package.json` aplicativo é atualizado com as dependências.


## <a name="object-model"></a>Modelo de objeto

O QnA Maker Client é um objeto [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) que se autentica no Azure usando o imclientcredentials, que contém sua chave.

Depois que o cliente for criado, use a propriedade [da base de dados de conhecimento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) criar, gerenciar e publicar sua base de dados de conhecimento. 

Gerencie sua base de dados de conhecimento enviando um objeto JSON. Para operações imediatas, um método geralmente retorna um objeto JSON que indica o status. Para operações de longa execução, a resposta é a ID da operação. Chamar o [cliente. Método Operations. GetDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) com a ID da operação para determinar o [status da solicitação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest). 

 
## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente QnA Maker para node. js:

* [Criar uma base de dados de conhecimento](#create-a-knowledge-base)
* [Atualizar uma base de dados de conhecimento](#update-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Excluir uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Obter o status de uma operação](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Adicionar as dependências

Crie um ficheiro com o nome `index.js`. Adicione a biblioteca de QnA Maker e a biblioteca REST do Azure ao arquivo.

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

Crie variáveis para o ponto de extremidade e a chave do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável.

|Variável de ambiente|Variável node. js|Exemplo|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|GUID de caractere de 32|
|`QNAMAKER_HOST`|`endpoint`|`https://your-resource-name.api.cognitive.microsoft.com`-substitua o subdomínio `your-resource-name` pelo valor do nome do seu recurso|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Em seguida, crie um objeto createclientcredentials com sua chave e use-o com seu ponto de extremidade para criar um objeto [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) . Use o objeto de cliente para obter um objeto [da base de dados de conhecimento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) .


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de dados de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) de três fontes:

* Para **conteúdo editorial**, use o objeto [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) .
* Para **arquivos**, use o objeto [encampoto](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) . 
* Para **URLs**, use uma lista de cadeias de caracteres.

Chame o método [Create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) e, em seguida, passe a ID da operação retornada para o método [Operations. GetDetails](#get-status-of-an-operation) para sondar o status. 

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]

Certifique-se de incluir [`wait_for_operation`](#get-status-of-an-operation) a função, referenciada no código acima, para criar com êxito uma base de dados de conhecimento. 

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Você pode atualizar uma base de dados de conhecimento passando a ID da base de dados de conhecimento e um [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) contendo [Adicionar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [Atualizar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)e [excluir](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) objetos dto para o método [Update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) . Use o método [Operation. getdetail](#get-status-of-an-operation) para determinar se a atualização foi bem-sucedida.

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

Certifique-se de incluir [`wait_for_operation`](#get-status-of-an-operation) a função, referenciada no código acima, para atualizar com êxito uma base de dados de conhecimento. 

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de dados de conhecimento usando o método [Publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) . Isso usa o modelo atual salvo e treinado, referenciado pela ID da base de dados de conhecimento e o publica em um ponto de extremidade. 

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Exclua a base de dados de conhecimento usando o método [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) com um parâmetro da ID da base de dados de conhecimento. 

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>Obter o status de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente que, em vez de aguardar a conclusão do processo, uma [operação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) é retornada. Use a [ID da operação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) da operação para sondagem (com lógica de repetição) para determinar o status do método original. 

A chamada _setTimeout_ no bloco de código a seguir é usada para simular código assíncrono. Substituir pela lógica de repetição. 

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo com `node index.js` o comando do diretório do aplicativo.


Todos os trechos de código neste artigo estão [disponíveis](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) e podem ser executados como um único arquivo.

```console
node index.js
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