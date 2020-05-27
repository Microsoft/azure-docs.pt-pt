---
title: Quickstart - Envie uma consulta à API usando Node.js - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Use este quickstart para começar a enviar pedidos para a API local de pesquisa de negócios Bing, que é um Serviço Cognitivo Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.openlocfilehash: 3bb31c36e8c614a72b86f95cb7e7d1c588692f97
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873092"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Quickstart: Envie uma consulta à API de Pesquisa de Negócios Local Bing usando node.js

Use este quickstart para aprender a enviar pedidos para a API de Pesquisa De Negócios Local Bing, que é um Serviço Cognitivo Azure. Embora esta aplicação simples esteja escrita no Node.js, a API é um serviço Web RESTful compatível com qualquer linguagem de programação capaz de fazer pedidos HTTP e analisar jSON.

Esta aplicação de exemplo obtém dados de resposta local da API para uma consulta de pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).
* A [Biblioteca de Pedidos JavaScript.](https://github.com/request/request)
* Uma [conta de API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com APIs de Pesquisa bing. Para este início rápido, o [julgamento gratuito](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente. Guarde a chave API fornecida quando ativar o seu teste gratuito. Para mais informações, consulte [Preços dos Serviços Cognitivos - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).


## <a name="code-scenario"></a>Cenário de código

O seguinte código define e envia o pedido, que é implementado nas seguintes etapas:

1. Declare variáveis para especificar o ponto final por anfitrião e por caminho.
2. Especifique a consulta e adicione o parâmetro de consulta.
3. Crie uma função de processador para a resposta.
4. Defina a função De Pesquisa que cria o pedido e adiciona o `Ocp-Apim-Subscription-Key` cabeçalho.
5. Executar a função de pesquisa.


```javascript
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

* [Pesquisa de negócios local C# quickstart](local-quickstart.md)
* [Pesquisa de negócios locais Java quickstart](local-search-java-quickstart.md)
* [Pesquisa de negócios local Python quickstart](local-search-python-quickstart.md)
