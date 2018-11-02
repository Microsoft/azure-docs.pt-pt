---
title: Início rápido - enviar uma consulta para o Local Business API de pesquisa Bing com node. js | Documentos da Microsoft
titleSuffix: Azure Cognitive Services
description: Começar a utilizar a API de pesquisa de negócios locais do Bing no nó.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 571ac9f593e51d66a905f34be9ead2f42753635b
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748697"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Início rápido: Enviar uma consulta para o Local Business API de pesquisa Bing com node. js

Utilize este guia de introdução para começar a enviar pedidos para o Local Business API de pesquisa Bing, que é um serviço cognitivos do Azure. Embora este aplicativo simples é escrito em node. js, a API é um serviço RESTful Web compatível com qualquer linguagem de programação capaz de fazer pedidos de HTTP e analisar JSON.
 
Esta aplicação de exemplo obtém os dados do local de resposta da API para a consulta de pesquisa `hotel in Bellevue`.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* O [biblioteca de pedido de JavaScript](https://github.com/request/request)

Tem de ter uma [conta de API dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com as APIs do Bing. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente para este guia de início rápido. Utilize a chave de acesso fornecida pela versão de avaliação gratuita.

##<a name="code-scenario"></a>Cenário de código
O código a seguir obtém define e envia o pedido. É implementado nos passos seguintes:

1. Declare variáveis para especificar o ponto final por anfitrião e por caminho.
2. Especifique a consulta e adicione o parâmetro de consulta. 
3. Crie uma função de processador para a resposta.
4. Defina a função de pesquisa que cria o pedido e adiciona o cabeçalho de Ocp-Apim-Subscription-Key.
5. Execute a função Pesquisa. 

Segue-se o código completo para esta demonstração:

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'www.bingapis.com';
let path = '/api/v7/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&appid=" + accessKey + "&traffictype=Internal_monitor&mkt=" + mkt;

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

````

## <a name="next-steps"></a>Passos Seguintes
- [Guia de introdução de pesquisa de negócios local](local-quickstart.md)
- [Início rápido de Java de pesquisa de negócios local](local-search-java-quickstart.md)
- [Guia de introdução de Python de pesquisa de negócios local](local-search-python-quickstart.md)
