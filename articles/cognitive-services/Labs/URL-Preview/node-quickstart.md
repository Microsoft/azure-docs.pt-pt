---
title: 'Início rápido: URL do projeto pré-visualização, node. js'
titlesuffix: Azure Cognitive Services
description: Comece a utilizar a Pré-visualização do URL nos Serviços Cognitivos da Microsoft no Azure.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 5c373505cd381108366206c21ff09f25516d7969
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712407"
---
# <a name="quickstart-url-preview-with-nodejs"></a>Início rápido: Pré-visualização de URL com node. js 

O exemplo de Node seguinte cria uma Pré-visualização do URL do site da SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Pré-requisitos

Obter uma chave de acesso da avaliação gratuita dos [Laboratórios dos Serviços Cognitivos](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Cenário de código 

O código seguinte obtém os dados da Pré-visualização do URL.
É implementado nos passos seguintes:
1. Declare variáveis para especificar o ponto final por anfitrião e por caminho.
2. Especifique o URL de consulta da pré-visualização e adicione o parâmetro de consulta.  
3. Crie uma função de processador para a resposta.
4. Defina a função Pesquisa que cria o pedido e adiciona o cabeçalho *Ocp-Apim-Subscription-Key*.
5. Execute a função Pesquisa. 

Segue-se o código completo para esta demonstração:

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-ACCESS-KEY'; 
let host = 'api.labs.cognitive.microsoft.com';
let path = '/urlpreview/v7.0/search';

let mkt = 'en-US';
let q = 'https://swiftkey.com/';

let params = '?q=' + encodeURI(q);

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

## <a name="next-steps"></a>Passos Seguintes
- [Código de exemplo de C#](csharp.md)
- [Início rápido do Java](java-quickstart.md)
- [Início rápido do JavaScript](javascript.md)
- [Início rápido do Python](python-quickstart.md)
