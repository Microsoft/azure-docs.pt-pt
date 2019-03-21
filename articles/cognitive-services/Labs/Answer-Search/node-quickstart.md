---
title: 'Início rápido: Pesquisa de resposta de projeto, nó'
description: Comece a utilizar a Pesquisa de Respostas do Projeto com Node.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: c44be9779cf2b856ac2f5e9e017fd9d1040a018b
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294047"
---
# <a name="quickstart-project-answer-search-with-node"></a>Início rápido: Pesquisa de resposta de projeto com o nó

O exemplo de Node seguinte cria uma consulta para obter informações sobre o Yosemite National Park.

## <a name="prerequisites"></a>Pré-requisitos

Obter uma chave de acesso da avaliação gratuita dos [Laboratórios dos Serviços Cognitivos](https://aka.ms/answersearchsubscription)

Este exemplo utiliza o Node v8.9.4

## <a name="code-scenario"></a>Cenário de código 

O código a seguir obtém as respostas.
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
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt= + mkt;

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
- [Código de exemplo de C#](c-sharp-quickstart.md)
- [Início Rápido do Java](java-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
