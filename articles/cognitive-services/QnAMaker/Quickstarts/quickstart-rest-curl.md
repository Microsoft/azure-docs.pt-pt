---
title: 'Quickstart: Use cURL e REST para gerir base de conhecimento - QnA Maker'
description: Este quickstart mostra-lhe como criar, publicar e consultar a sua base de conhecimentos usando as APIs REST.
ms.topic: quickstart
ms.date: 12/16/2019
ms.openlocfilehash: d2542a0307387cef1c96ecfb426a8e7c01621ee2
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165461"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Quickstart: Use cURL e REST para gerir base de conhecimento

Este quickstart leva-o através da criação, publicação e consulta da sua base de conhecimento. O Criador de FAQ extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como FAQs, a partir de [origens de dados](../Concepts/knowledge-base.md). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo do pedido da API.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* A versão atual do [cURL](https://curl.haxx.se/). Vários interruptores de linha de comando são utilizados nos quickstarts, que são anotados na [documentação cURL](https://curl.haxx.se/docs/manpage.html).
* Deve ter um [recurso QnA Maker.](../How-To/set-up-qnamaker-service-azure.md) Para recuperar o seu nome de chave e recursos, selecione **Quickstart** para o seu recurso no portal Azure. O nome do recurso é a primeira parte do URL final:

    `https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> Os seguintes exemplos bash usam o caráter de continuação da linha `\`. Se consolar ou terminal utilizar um personagem de continuação de linha diferente, use este personagem.

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Para criar uma base de conhecimento com as APIs e cURL rest, precisa de ter as seguintes informações:

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso qna maker|do IdP|usado para construir URL|
|Chave de recursos qnA Maker|`-h` para o cabeçalho do `Ocp-Apim-Subscription-Key`|Serviço Authenticate to QnA Maker|
|JSON descrevendo base de conhecimento|`-d` param|[Exemplos](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) de JSON|
|Tamanho do JSON em bytes|`-h` para o cabeçalho do `Content-Size`||

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e valores JSON e tamanho da JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

A resposta cURL da Fabricante QnA inclui o `operationId` , que é necessário para [obter o estado da operação](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>Obter o estado de operação

Quando se cria uma base de conhecimento, porque a operação é asincronizada, a resposta inclui informações para determinar o estado.

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso qna maker|do IdP|usado para construir URL|
|Operação Id|Rota do URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Chave de recursos qnA Maker|`-h` para o cabeçalho do `Ocp-Apim-Subscription-Key`|Serviço Authenticate to QnA Maker|

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e ID de operação.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

A resposta cURL inclui o estado. Se o estado de operação for bem sucedido, então o `resourceLocation` inclui a identificação da base de conhecimento.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>Publicar base de dados de conhecimento

Antes de consultar a base de conhecimento, precisa:
* Publicar base de dados de conhecimento
* Obtenha a chave final do ponto de execução

Esta tarefa publica a base de conhecimento. Obter a chave final do ponto final do tempo de execução é uma [tarefa separada](#get-published-knowledge-bases-runtime-endpoint-key).

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso qna maker|do IdP|usado para construir URL|
|Chave de recursos qnA Maker|`-h` para o cabeçalho do `Ocp-Apim-Subscription-Key`|Serviço Authenticate to QnA Maker|
|Id base de conhecimento|Rota do URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e ID da base de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

O estado de resposta é 204 sem resultados. Utilize o parâmetro de linha de comando `-v` para verboso para o comando cURL. Isto incluirá o estatuto HTTP.

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Obtenha a chave final do ponto final da base de conhecimento publicada

Antes de consultar a base de conhecimento, precisa:
* Publicar base de dados de conhecimento
* Obtenha a chave final do ponto de execução

Esta tarefa obtém a chave final do ponto de execução. Publicar a base de conhecimento é uma [tarefa separada.](#publish-knowledge-base)

A chave final do ponto de execução é a mesma chave para todas as bases de conhecimento que utilizam o recurso QnA Maker.

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso qna maker|do IdP|usado para construir URL|
|Chave de recursos qnA Maker|`-h` para o cabeçalho do `Ocp-Apim-Subscription-Key`|Serviço Authenticate to QnA Maker|

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


A resposta cURL inclui as teclas de ponto final do tempo de execução. Use apenas uma das chaves quando consultar para obter uma resposta da base de conhecimento.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>Consulta para resposta da base de conhecimento publicada

Obter uma resposta do conhecimento é feito a partir de um tempo de execução separado do que gerir a base de conhecimento. Como é um tempo de funcionação separado, você precisa autenticar com uma chave de tempo de funcionação.

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso qna maker|do IdP|usado para construir URL|
|QnA Maker chave de tempo de execução|`-h` para o cabeçalho do `Authorization`|A chave faz parte de uma corda que inclui a palavra `Endpointkey `. Serviço Authenticate to QnA Maker|
|Id base de conhecimento|Rota do URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON descrevendo consulta|`-d` param|[Solicitar parâmetros corporais](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) e [exemplos](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) de JSON|
|Tamanho do JSON em bytes|`-h` para o cabeçalho do `Content-Size`||

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e ID da base de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Uma resposta bem sucedida inclui a resposta superior juntamente com outras informações que uma aplicação do cliente, como um chat bot, precisa de apresentar uma resposta ao utilizador.

## <a name="delete-knowledge-base"></a>Eliminar base de conhecimento

Quando terminar com a base de conhecimento, apague-a.

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso qna maker|do IdP|usado para construir URL|
|Chave de recursos qnA Maker|`-h` para o cabeçalho do `Ocp-Apim-Subscription-Key`|Serviço Authenticate to QnA Maker|
|Id base de conhecimento|Rota do URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e ID da base de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

O estado de resposta é 204 sem resultados. Utilize o parâmetro de linha de comando `-v` para verboso para o comando cURL. Isto incluirá o estatuto HTTP.

## <a name="additional-resources"></a>Recursos adicionais

* [Autoria](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) Documentação de referência
* [Tempo de execução](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/) Documentação de referência
* [Exemplo de scripts BASH usando cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência da API REST para o Criador de FAQ (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
