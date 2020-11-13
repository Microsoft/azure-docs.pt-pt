---
title: 'Quickstart: Procure imagens utilizando a API de pesquisa de imagem Bing e Node.js'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para enviar pedidos de pesquisa de imagem para a API de pesquisa de imagem Bing usando respostas JavaScript e JSON.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: 335568ec04a970aa1e5926c02e6ff83898b46358
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593540"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>Quickstart: Procure imagens utilizando a API de pesquisa de imagem Bing e Node.js

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Utilize este quickstart para aprender a enviar pedidos de pesquisa para a API de Pesquisa de Imagem Bing. Esta aplicação JavaScript envia uma consulta de pesquisa para a API e apresenta o URL da primeira imagem nos resultados. Embora esta aplicação esteja escrita em JavaScript, a API é um serviço web RESTful compatível com a maioria das linguagens de programação.

O código fonte deste exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) com processamento de erros e anotações de código adicionais.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* A [Biblioteca de Pedidos JavaScript.](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Para mais informações, consulte [preços dos serviços cognitivos - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e desaprove os requisitos de rigor e HTTPS.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Crie variáveis para o ponto final da API, o caminho de pesquisa da API de imagens, a sua chave de subscrição e o termo de pesquisa. Para `host` , pode utilizar o ponto final global no seguinte código, ou utilizar o ponto final [de subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>Crie a consulta e o pedido de pesquisa.

1. Utilize as variáveis do último passo para formatar um URL de pesquisa para o pedido da API. URL codificar o seu termo de pesquisa antes de enviá-lo para a API.

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

2. Utilize a biblioteca de pedidos para enviar a sua consulta para a API. 
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Processar e analisar a resposta

1. Defina uma função nomeada `response_handler` que recebe uma chamada HTTP, como `response` parâmetro. 

2. Dentro desta função, definir uma variável para conter o corpo da resposta JSON. 

    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. Guarde o corpo da resposta quando a `data` bandeira for chamada.

    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. Quando uma `end` bandeira é sinalizada, obtenha o primeiro resultado da resposta JSON. Imprima o URL para a primeira imagem, juntamente com o número total de imagens devolvidas.

    ```javascript
    response.on('end', function () {
        let firstImageResult = imageResults.value[0];
        console.log(`Image result count: ${imageResults.value.length}`);
        console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
        console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
     });
    ```

## <a name="example-json-response"></a>Exemplo JSON resposta

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação de uma página](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Ver também

* [O que é a API de Pesquisa de Imagens do Bing?](../overview.md)  
* [Experimentar uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Detalhes dos preços das APIs de pesquisa de Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Documentação dos Serviços Cognitivos da Azure](../../index.yml)
* [Bing Image Search API reference](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)