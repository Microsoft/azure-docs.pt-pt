---
title: Guia de introdução do Node.js – Criar uma nova base de dados de conhecimento – para a API do Criador de FAQ da Microsoft (V4) – Serviços Cognitivos do Azure | Microsoft Docs
description: Crie uma base de dados de conhecimento em Node.js para manter as suas FAQs e manuais de produtos para que possa começar a utilizar o Criador de FAQ.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: cbf89aa3a10012ea2d99972c6a83dd351739c5a6
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/06/2018
ms.locfileid: "43771398"
---
# <a name="create-a-new-knowledge-base-in-nodejs"></a>Criar uma nova base de dados de conhecimento em Node.js

Este guia de introdução descreve a criação programática de uma base de dados de conhecimento do Criador de FAQ de exemplo, que será apresentada no seu Dashboard do Azure, da sua conta de API dos Serviços Cognitivos.

Abaixo, indicamos dois URLs de FAQs de exemplo (em "urls" de **req={}**) que irão fornecer conteúdos. O Criador de FAQ extrai automaticamente perguntas e respostas destes conteúdos semiestruturados, como é explicado neste documento de [origens de dados](../Concepts/data-sources-supported.md). Também pode utilizar os seus próprios URLs de FAQs neste guia de introdução.

## <a name="prerequisites"></a>Pré-requisitos

Irá precisar da [versão 6 ou superior do Node.js](https://nodejs.org/en/download/) para executar este código.

Tem de ter uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com a **API do Criador de FAQ da Microsoft**. Tem de ter uma chave de subscrição paga a partir do seu [dashboard do Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices). Qualquer chave funciona neste guia de introdução.

![Chave de serviço do dashboard do Azure](../media/sub-key.png)

Para obter mais ajuda com o Visual Studio e o Node.js, aceda a [Quickstart: Use Visual Studio to create your first Node.js app](https://docs.microsoft.com/en-us/visualstudio/ide/quickstart-nodejs) (Guia de introdução: utilizar o Visual Studio para criar a sua primeira aplicação Node.js).

## <a name="create-knowledge-base"></a>Criar uma base de dados de conhecimento

O seguinte código cria uma nova base de dados de conhecimento, através do método [Criar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff).

1. Crie um novo projeto de Node.js no seu IDE preferido.
2. Adicione o código indicado abaixo.
3. Substitua o valor `subscriptionKey` por uma chave de subscrição válida.
4. Execute o programa.

```nodejs
'use strict';

let fs = require('fs');
let https = require('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'YOUR SUBSCRIPTION KEY HERE';

// Components used to create HTTP request URIs for QnA Maker operations.
let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/create';

// Formats and indents JSON for display.
let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
};

// The 'callback' is called from the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        // Call the 'callback' with the status code, headers, and body of the response.
        callback({ status: response.statusCode, headers: response.headers, body: body });
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls 'callback' from the entire response.
let get_response_handler = function (callback) {
    // Return a function that takes an HTTP response and is closed over the specified callback.
    // This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler(callback, response);
    };
};

// Call 'callback' when we have the entire response from the POST request.
let post = function (path, content, callback) {
    let request_params = {
        method: 'POST',
        hostname: host,
        path: path,
        headers: {
            'Content-Type': 'application/json',
            'Content-Length': content.length,
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };

    // Pass the 'callback' function to the response handler.
    let req = https.request(request_params, get_response_handler(callback));
    req.write(content);
    req.end();
};

// Call 'callback' when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };

    // Pass the callback function to the response handler.
    let req = https.request(request_params, get_response_handler(callback));
    req.end();
};

// Call 'callback' when we have the response from the /knowledgebases/create POST method.
let create_kb = function (path, req, callback) {
    console.log('Calling ' + host + path + '.');
    // Send the POST request.
    post(path, req, function (response) {
        // Extract the data we want from the POST response and pass it to the callback function.
        callback({ operation: response.headers.location, response: response.body });
    });
};

// Call 'callback' when we have the response from the GET request to check the status.
let check_status = function (path, callback) {
    console.log('Calling ' + host + path + '.');
    // Send the GET request.
    get(path, function (response) {
        // Extract the data we want from the GET response and pass it to the callback function.
        callback({ wait: response.headers['retry-after'], response: response.body });
    });
};

// Dictionary that holds the knowledge base.
// The data source includes a QnA pair with metadata, the URL for the
// QnA Maker FAQ article, and the URL for the Azure Bot Service FAQ article.
let req = {
    "name": "QnA Maker FAQ",
    "qnaList": [
        {
            "id": 0,
            "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa",
            "source": "Custom Editorial",
            "questions": [
                "How do I programmatically update my Knowledge Base?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ],
    "urls": [
        "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
    ],
    "files": []
};

// Build your path URL.
var path = service + method;
// Convert the request to a string.
let content = JSON.stringify(req);
create_kb(path, content, function (result) {
    // Formats and indents the JSON response from the /knowledgebases/create method for display.
    console.log(pretty_print(result.response));
    // Loop until the operation is complete.
    let loop = function () {
        path = service + result.operation;
        // Check the status of the operation.
        check_status(path, function (status) {
            // Formats and indents the JSON for display.
            console.log(pretty_print(status.response));
            // Convert the status into an object and get the value of the 'operationState'.
            var state = (JSON.parse(status.response)).operationState;
            // If the operation isn't complete, wait and query again.
            if (state === 'Running' || state === 'NotStarted') {
                console.log('Waiting ' + status.wait + ' seconds...');
                setTimeout(loop, status.wait * 1000);
            }
        });
    };
    // Begin the loop.
    loop();
});
```

## <a name="understand-what-qna-maker-returns"></a>Compreender os resultados do Criador de FAQ

É devolvida uma resposta com êxito em JSON, tal como apresentado no seguinte exemplo. Os seus resultados poderão ser ligeiramente diferentes. Se o resultado final devolver o estado "Com êxito (Succeeded)", indica que a sua base de dados de conhecimento foi criada com êxito. Para a resolução de problemas, veja [Get Operation Details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) (Obter Detalhes da Operação) da API do Criador de FAQ.

```json
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/knowledgebases/create.
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917tt4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917tt4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Waiting 30 seconds...
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917tt4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Press any key to continue.
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)