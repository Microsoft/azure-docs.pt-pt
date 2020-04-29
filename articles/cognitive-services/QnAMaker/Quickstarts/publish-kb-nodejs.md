---
title: 'Quickstart: QnA Maker com APIs REST para Node.js'
description: Este quickstart mostra como começar com as APIs REST DO Fabricante QnA para Node.js. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: ecc3fb144fb4b4e27182567925199f841b1c4357
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851676"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Quickstart: QnA Maker REST APIs para Node.js

Começa com as APIs DE REST do Fabricante qnA para node.js. Siga estes passos para experimentar o código de exemplo para tarefas básicas.  O Criador de FAQ permite-lhe incorporar um serviço de perguntas e respostas a partir do seu conteúdo semiestruturado, como documentos de FAQ, URLs e manuais de produtos.

Utilize as APIs DE REPOUSO DO Fabricante QnA para:

* Criar uma base de dados de conhecimento
* Substituir uma base de conhecimento
* Publicar uma base de dados de conhecimento
* Eliminar uma base de dados de conhecimento
* Descarregue uma base de conhecimento
* Obter o estado de uma operação

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Node.js Amostras](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Node.js.](https://nodejs.org)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.

## <a name="setting-up"></a>Configuração

### <a name="create-a-qna-maker-azure-resource"></a>Criar um recurso QnA Maker Azure

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para o Fabricante QnA utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local.

Depois de obter uma chave do seu recurso, `QNAMAKER_RESOURCE_KEY` [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o recurso, nomeado e `QNAMAKER_AUTHORING_ENDPOINT`. Utilize os valores chave e ponto final encontrados na página **Quickstart** do Recurso no portal Azure.

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue para ela.

```console
mkdir myapp && cd myapp
```

Execute `npm init -y` o comando para `package.json` criar um ficheiro de nó.

```console
npm init -y
```

Adicione `reqeuestretry` os `request` pacotes e npm:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com as APIs REST DO Fabricante QnA para Node.js:

* [Criar uma base de dados de conhecimento](#create-a-knowledge-base)
* [Substituir uma base de conhecimento](#replace-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Eliminar uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Descarregue uma base de conhecimento](#download-the-knowledge-base)
* [Obter o estado de uma operação](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Adicione as dependências

Criar um `rest-apis.js` ficheiro nomeado e adicionar o seguinte _requer_ uma declaração para fazer pedidos HTTP.

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Adicione informações sobre recursos Azure

Crie variáveis para o ponto final e chave azure do seu recurso. Se criou a variável ambiental depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE, ou a shell executá-la para aceder à variável.

Detete os seguintes valores ambientais:

* `QNAMAKER_RESOURCE_KEY`- A **chave** é uma cadeia de caracteres de 32 caracteres e está disponível no portal Azure, no recurso QnA Maker, na página **de arranque rápida.** Isto não é o mesmo que a chave final da previsão.
* `QNAMAKER_AUTHORING_ENDPOINT`- O seu ponto final de `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`autoria, no formato de, inclui o seu nome de **recurso**. Este não é o mesmo URL usado para consultar o ponto final da previsão.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de conhecimento armazena pares de perguntas e respostas, criados a partir de um objeto JSON de:

* **Conteúdo editorial.**
* **Ficheiros** - ficheiros locais que não requerem permissões.
* **URLs** - URLs disponíveis ao público.

Utilize a [API REST para criar uma base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)de conhecimento.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Substituir uma base de conhecimento

Utilize a [API REST para substituir uma base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)de conhecimentos .

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de conhecimento. Este processo disponibiliza a base de conhecimentos a partir de um ponto final de previsão de consulta HTTP.

Utilize a [API REST para publicar uma base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)de conhecimento.


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>Descarregue a base de conhecimento

Utilize a [API REST para descarregar uma base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)de conhecimentos.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Quando terminar usando a base de conhecimento, apague-a.

Utilize a [API REST para eliminar uma base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete)de conhecimentos .

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Obter o estado de uma operação

Processos de longo curso, como o processo de criação, devolvem um ID de operação, que precisa de ser verificado com uma chamada separada da API REST. Esta função leva o corpo da resposta de criação. A chave importante `operationState`é a , que determina se precisa de continuar as sondagens.

Utilize a [API REST para monitorizar as operações numa base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)de conhecimento .


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>Executar a aplicação

Execute a `node rest-apis.js` aplicação com o comando do seu diretório de candidatura.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Tutorial: Criar e responder a um KB](../tutorials/create-publish-query-in-portal.md)

* [O que é a API do Fabricante de QnA?](../Overview/overview.md)
* [Editar uma base de dados de conhecimento](../how-to/edit-knowledge-base.md)
* [Obtenha análises de uso](../how-to/get-analytics-knowledge-base.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).