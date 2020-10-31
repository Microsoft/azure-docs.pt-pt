---
title: Quickstart - Envie uma consulta à API utilizando Node.js - Pesquisa local de negócios
titleSuffix: Azure Cognitive Services
description: Use este quickstart para começar a enviar pedidos para a Bing Local Business Search API, que é um Serviço Cognitivo Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 512ecd44d0d6fd6edc07fb7dc1508c062dd27f78
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095174"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Quickstart: Envie uma consulta para a API de Pesquisa de Negócios Locais de Bing utilizando Node.js

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Use este quickstart para aprender a enviar pedidos para a Bing Local Business Search API, que é um Serviço Cognitivo Azure. Embora esta aplicação simples esteja escrita em Node.js, a API é um serviço Web RESTful compatível com qualquer linguagem de programação capaz de fazer pedidos HTTP e analisar o JSON.

Este exemplo de aplicação obtém dados de resposta local da API para uma consulta de pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão mais recente do [Node.js](https://nodejs.org/en/download/).
* A [Biblioteca de Pedidos JavaScript.](https://github.com/request/request)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" Crie um recurso Bing Search crie um "  target="_blank"> recurso Bing Search no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso** .


## <a name="code-scenario"></a>Cenário de código

O seguinte código define e envia o pedido, que é implementado nas seguintes etapas:

1. Declare variáveis para especificar o ponto final por anfitrião e por caminho.
2. Especifique a consulta e adicione o parâmetro de consulta.
3. Crie uma função de processador para a resposta.
4. Defina a função 'Procurar' que cria o pedido e adiciona o `Ocp-Apim-Subscription-Key` cabeçalho.
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

* [Pesquisa de negócios locais C# quickstart](local-quickstart.md)
* [Pesquisa de negócios locais Java quickstart](local-search-java-quickstart.md)
* [Pesquisa de negócios locais Python quickstart](local-search-python-quickstart.md)
