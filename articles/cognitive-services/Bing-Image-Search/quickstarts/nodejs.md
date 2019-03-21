---
title: 'Início rápido: Procure imagens - API de REST de pesquisa de imagens do Bing e node. js'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de introdução para enviar pedidos de pesquisa de imagem para a API de pesquisa de imagens do Bing REST usando JavaScript e receber respostas JSON.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: f3b174fa00c6f91c4e4840f2abcb14f95451d7f5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118481"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>Início rápido: Procure imagens usando a API de REST de pesquisa de imagens do Bing e o node. js

Utilize este guia de introdução para começar a enviar pedidos de pesquisa para a API de pesquisa de imagens do Bing. Esta aplicação JavaScript envia uma consulta de pesquisa para a API e apresenta o URL da imagem do primeiro nos resultados. Embora esse aplicativo é escrito em Javascript, a API é um serviço RESTful web compatível com a maioria das linguagens de programação.

O código fonte deste exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) com processamento de erros e anotações de código adicionais.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* O [biblioteca de pedido de JavaScript](https://github.com/request/request)  
  [!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Consulte também [dos serviços cognitivos preços - API de pesquisa Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e defina os requisitos de exatidão e HTTPS.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Crie variáveis para o ponto final da API, o caminho de pesquisa da API de imagens, a sua chave de subscrição e o termo de pesquisa.
    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>Crie a consulta e o pedido de pesquisa.

1. Utilize as variáveis do último passo para formatar um URL de pesquisa para o pedido da API. O termo de pesquisa tem de ser codificados de URL antes de serem enviados para a API.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. Utilize a biblioteca de pedidos para enviar a sua consulta para a API. A função `response_handler` será definida na secção seguinte.
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Processar e analisar a resposta

1. Defina uma função denominada `response_handler` que recebe uma chamada HTTP `response` como um parâmetro. Execute os seguintes passos dentro desta função:

    1. Defina uma variável para incluir o corpo da resposta JSON.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Armazene o corpo da resposta quando o sinalizador **data** for chamado
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Quando um **final** sinalizador é sinalizado, obter o primeiro resultado da resposta JSON. O URL para a primeira imagem, juntamente com o número total de imagens retornados de impressão.

        ```javascript
        response.on('end', function () {
            let firstImageResult = imageResults.value[0];
            console.log(`Image result count: ${imageResults.value.length}`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
         });
        ```

## <a name="example-json-response"></a>Resposta JSON de exemplo

As respostas da API de Pesquisa de Imagens do Bing são devolvidas como JSON. Esta resposta de amostra foi truncada para mostrar um único resultado.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação de página única](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Consulte também

* [O que é a Pesquisa de Imagens do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Experimentar uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* [Os detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) para as APIs de pesquisa do Bing. 
* [Obter uma chave de acesso aos Serviços Cognitivos gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Documentação dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)
