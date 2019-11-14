---
title: Início rápido – enviar uma consulta para a API de pesquisa de negócios local do Bing usando o Node. js
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para começar a enviar solicitações para a API de pesquisa de negócios local do Bing, que é um serviço de cognitiva do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: 925e3bfb4a89832d5cde7e35d5ce5e3be3a5bcbb
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075632"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Início rápido: Enviar uma consulta para a API de pesquisa de negócios local do Bing usando o Node. js

Use este guia de início rápido para começar a enviar solicitações para a API de pesquisa de negócios local do Bing, que é um serviço de cognitiva do Azure. Embora esse aplicativo simples seja escrito em node. js, a API é um serviço Web RESTful compatível com qualquer linguagem de programação capaz de fazer solicitações HTTP e analisar JSON.

Este aplicativo de exemplo obtém dados de resposta locais da API para a consulta de pesquisa `hotel in Bellevue`.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* A [biblioteca de solicitações do JavaScript](https://github.com/request/request)

Você deve ter uma [conta de API de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com APIs do Bing. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente para este guia de início rápido. Use a chave de acesso fornecida pela avaliação gratuita.  Consulte também [dos serviços cognitivos preços - API de pesquisa Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Cenário de código

O código a seguir obtém e envia a solicitação. É implementado nos passos seguintes:

1. Declare variáveis para especificar o ponto final por anfitrião e por caminho.
2. Especifique a consulta e adicione o parâmetro de consulta.
3. Crie uma função de processador para a resposta.
4. Defina a função de pesquisa que cria a solicitação e adiciona o cabeçalho OCP-APIM-Subscription-Key.
5. Execute a função Pesquisa.

Segue-se o código completo para esta demonstração:

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Passos seguintes

* [Início rápido de pesquisa comercial local](local-quickstart.md)
* [Início rápido do Java da pesquisa de negócios local](local-search-java-quickstart.md)
* [Início rápido do Python de pesquisa comercial local](local-search-python-quickstart.md)
