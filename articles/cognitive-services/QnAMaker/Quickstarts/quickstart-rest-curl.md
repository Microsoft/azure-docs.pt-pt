---
title: 'Quickstart: Use cURL & REST para gerir a base de conhecimentos - QnA Maker'
description: Este quickstart mostra-lhe como criar, publicar e consultar a sua base de conhecimentos utilizando as APIs REST.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 11/09/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: 7592b9fb509f39504ad2399d0e939ceca1156221
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351100"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Quickstart: Use cURL e REST para gerir a base de conhecimentos

Este quickstart acompanha-o através da criação, publicação e consulta da sua base de conhecimentos. O Criador de FAQ extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como FAQs, a partir de [origens de dados](../index.yml). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo do pedido da API.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

* A versão atual do [cURL.](https://curl.haxx.se/) São utilizados vários interruptores de linha de comando nos arranques rápidos, que são indicados na [documentação cURL](https://curl.haxx.se/docs/manpage.html).
* Você deve ter um [recurso QnA Maker](../How-To/set-up-qnamaker-service-azure.md), para usar a chave e o nome do recurso. Inseriu o **nome** de recurso durante a criação de recursos, então a chave foi criada para si. O nome do recurso é usado como subdomínio para o seu ponto final. Para recuperar a sua chave e o nome do recurso, selecione **Quickstart** para o seu recurso no portal Azure. O nome do recurso é o primeiro subdomínio do URL do ponto final:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> Os seguintes exemplos BASH usam o `\` carácter de continuação da linha. Se utilizar uma consola ou terminal diferente, utilize este personagem.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

* A versão atual do [cURL.](https://curl.haxx.se/) São utilizados vários interruptores de linha de comando nos arranques rápidos, que são indicados na [documentação cURL](https://curl.haxx.se/docs/manpage.html).
* Você deve ter um [recurso QnA Maker](../How-To/set-up-qnamaker-service-azure.md), para usar a chave e o nome do recurso. Inseriu o **nome** de recurso durante a criação de recursos, então a chave foi criada para si. O nome do recurso é usado como subdomínio para o seu ponto final. Para recuperar a sua chave e o nome do recurso, selecione **Quickstart** para o seu recurso no portal Azure. O nome do recurso é o primeiro subdomínio do URL do ponto final:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1`

> [!CAUTION]
> Os seguintes exemplos BASH usam o `\` carácter de continuação da linha. Se utilizar uma consola ou terminal diferente, utilize este personagem.

---

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Para criar uma base de conhecimento com as APIs REST e cURL, é necessário ter as seguintes informações:

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso do Fabricante QnA|URL|usado para construir URL|
|Chave de recursos do Fabricante QnA|`-h` para o `Ocp-Apim-Subscription-Key` cabeçalho|Autenticar para o serviço QnA Maker|
|JSON descrevendo a base do conhecimento|`-d` param|[Exemplos](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) de JSON|
|Tamanho do JSON em bytes|`-h` para o `Content-Size` cabeçalho||

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e valores JSON e tamanho de JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

A resposta cURL da QnA Maker inclui o `operationId` , que é necessário para obter o estado da [operação](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)


Para criar uma base de conhecimento com as APIs REST e cURL, é necessário ter as seguintes informações:

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso do Fabricante QnA|URL|usado para construir URL|
|Chave de recursos do Fabricante QnA|`-h` para o `Ocp-Apim-Subscription-Key` cabeçalho|Autenticar para o serviço QnA Maker|
|JSON descrevendo a base do conhecimento|`-d` param|[Exemplos](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) de JSON|
|Tamanho do JSON em bytes|`-h` para o `Content-Size` cabeçalho||

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e valores JSON e tamanho de JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

A resposta cURL da QnA Maker inclui o `operationId` , que é necessário para obter o estado da [operação](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```
---

## <a name="get-status-of-operation"></a>Obter estado de funcionamento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Quando se cria uma base de conhecimento, porque a operação é async, a resposta inclui informações para determinar o estado.

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso do Fabricante QnA|URL|usado para construir URL|
|ID da Operação|Rota URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Chave de recursos do Fabricante QnA|`-h` para o `Ocp-Apim-Subscription-Key` cabeçalho|Autenticar para o serviço QnA Maker|

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e ID de operação.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

A resposta cURL inclui o estado. Se o estado de operação for bem sucedido, então `resourceLocation` inclui o ID da base de conhecimento.

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
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)


Quando se cria uma base de conhecimento, porque a operação é async, a resposta inclui informações para determinar o estado.

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso do Fabricante QnA|URL|usado para construir URL|
|ID da Operação|Rota URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Chave de recursos do Fabricante QnA|`-h` para o `Ocp-Apim-Subscription-Key` cabeçalho|Autenticar para o serviço QnA Maker|

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e ID de operação.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

A resposta cURL inclui o estado. Se o estado de operação for bem sucedido, então `resourceLocation` inclui o ID da base de conhecimento.

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

---

## <a name="publish-knowledge-base"></a>Publicar base de dados de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Antes de consultar a base de conhecimentos, tem de:
* Publicar base de dados de conhecimento
* Obtenha a chave de ponto final de tempo de execução

Esta tarefa publica a base de conhecimento. Obter a chave de ponto final de tempo de execução é uma [tarefa separada](#get-published-knowledge-bases-runtime-endpoint-key).

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso do Fabricante QnA|URL|usado para construir URL|
|Chave de recursos do Fabricante QnA|`-h` para o `Ocp-Apim-Subscription-Key` cabeçalho|Autenticar para o serviço QnA Maker|
|ID da base de conhecimento|Rota URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e identificação de base de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

O estado de resposta é 204 sem resultados. Utilize o `-v` parâmetro da linha de comando para ver a saída verbose para o comando cURL. Isto incluirá o estado HTTP.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Antes de consultar a base de conhecimentos, tem de:
* Publicar base de dados de conhecimento
* Obtenha a chave de ponto final de tempo de execução

Esta tarefa publica a base de conhecimento. Obter a chave de ponto final de tempo de execução é uma [tarefa separada](#get-published-knowledge-bases-runtime-endpoint-key).

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso do Fabricante QnA|URL|usado para construir URL|
|Chave de recursos do Fabricante QnA|`-h` para o `Ocp-Apim-Subscription-Key` cabeçalho|Autenticar para o serviço QnA Maker|
|ID da base de conhecimento|Rota URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e identificação de base de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

O estado de resposta é 204 sem resultados. Utilize o `-v` parâmetro da linha de comando para ver a saída verbose para o comando cURL. Isto incluirá o estado HTTP.

---

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Obtenha a chave final da base de conhecimento publicada

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Antes de consultar a base de conhecimentos, tem de:
* Publicar base de dados de conhecimento
* Obtenha a chave de ponto final de tempo de execução

Esta tarefa obtém a chave de ponto final de tempo de execução. Publicar a base de conhecimento é uma [tarefa separada.](#publish-knowledge-base)

A chave de ponto final de tempo de execução é a mesma chave para todas as bases de conhecimento utilizando o recurso QnA Maker.

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso do Fabricante QnA|URL|usado para construir URL|
|Chave de recursos do Fabricante QnA|`-h` para o `Ocp-Apim-Subscription-Key` cabeçalho|Autenticar para o serviço QnA Maker|

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


A resposta cURL inclui as teclas do ponto final de tempo de execução. Use apenas uma das chaves quando perguntar para obter uma resposta da base de conhecimento.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Antes de consultar a base de conhecimentos, tem de:
* Publicar base de dados de conhecimento
* Obtenha a chave de ponto final de tempo de execução

Esta tarefa obtém a chave de ponto final de tempo de execução. Publicar a base de conhecimento é uma [tarefa separada.](#publish-knowledge-base)

A chave de ponto final de tempo de execução é a mesma chave para todas as bases de conhecimento utilizando o recurso QnA Maker.

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso do Fabricante QnA|URL|usado para construir URL|
|Chave de recursos do Fabricante QnA|`-h` para o `Ocp-Apim-Subscription-Key` cabeçalho|Autenticar para o serviço QnA Maker|

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


A resposta cURL inclui as teclas do ponto final de tempo de execução. Use apenas uma das chaves quando perguntar para obter uma resposta da base de conhecimento.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

---

## <a name="query-for-answer-from-published-knowledge-base"></a>Consulta para resposta a partir da base de conhecimentos publicada

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Obter uma resposta do conhecimento é feito a partir de um tempo de execução separado do que gerir a base de conhecimento. Por ser um tempo de execução separado, é necessário autenticar com uma chave de tempo de execução.

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso do Fabricante QnA|URL|usado para construir URL|
|Chave de tempo de execução do Fabricante QnA|`-h` para o `Authorization` cabeçalho|A chave é parte de uma corda que inclui a palavra `Endpointkey ` . Autenticar para o serviço QnA Maker|
|ID da base de conhecimento|Rota URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON descrevendo consulta|`-d` param|[Solicitar parâmetros](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) e [exemplos](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) de JSON|
|Tamanho do JSON em bytes|`-h` para o `Content-Size` cabeçalho||

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e identificação de base de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Uma resposta bem sucedida inclui a resposta de topo juntamente com outras informações que uma aplicação do cliente, como um chat bot, precisa de apresentar uma resposta ao utilizador.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Obter uma resposta do conhecimento é feito a partir de um tempo de execução separado do que gerir a base de conhecimento. Por ser um tempo de execução separado, é necessário autenticar com uma chave de tempo de execução.

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso do Fabricante QnA|URL|usado para construir URL|
|Chave de recursos do Fabricante QnA|`-h` para o `Ocp-Apim-Subscription-Key` cabeçalho|Autenticar para o serviço QnA Maker|
|ID da base de conhecimento|Rota URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON descrevendo consulta|`-d` param|[Solicitar parâmetros](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) e [exemplos](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) de JSON|
|Tamanho do JSON em bytes|`-h` para o `Content-Size` cabeçalho||

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e identificação de base de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Uma resposta bem sucedida inclui a resposta de topo juntamente com outras informações que uma aplicação do cliente, como um chat bot, precisa de apresentar uma resposta ao utilizador.


---

## <a name="delete-knowledge-base"></a>Eliminar base de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Quando terminar a base de conhecimento, elimine-a.

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso do Fabricante QnA|URL|usado para construir URL|
|Chave de recursos do Fabricante QnA|`-h` para o `Ocp-Apim-Subscription-Key` cabeçalho|Autenticar para o serviço QnA Maker|
|ID da base de conhecimento|Rota URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e identificação de base de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

O estado de resposta é 204 sem resultados. Utilize o `-v` parâmetro da linha de comando para ver a saída verbose para o comando cURL. Isto incluirá o estado HTTP.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Quando terminar a base de conhecimento, elimine-a.

|Informações|configuração cURL|Objetivo|
|--|--|--|
|Nome de recurso do Fabricante QnA|URL|usado para construir URL|
|Chave de recursos do Fabricante QnA|`-h` para o `Ocp-Apim-Subscription-Key` cabeçalho|Autenticar para o serviço QnA Maker|
|ID da base de conhecimento|Rota URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

O comando cURL é executado a partir de uma concha BASH. Edite este comando com o seu próprio nome de recurso, chave de recursos e identificação de base de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

O estado de resposta é 204 sem resultados. Utilize o `-v` parâmetro da linha de comando para ver a saída verbose para o comando cURL. Isto incluirá o estado HTTP.

---

## <a name="additional-resources"></a>Recursos adicionais

* [Autoria](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) Documentação de referência
* [Tempo de execução](/rest/api/cognitiveservices/qnamaker4.0/runtime) Documentação de referência
* [Guiões BASH de amostra usando cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)