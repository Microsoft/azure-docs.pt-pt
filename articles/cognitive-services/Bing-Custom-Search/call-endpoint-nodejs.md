---
title: 'Início rápido: Chamar o ponto final de pesquisa personalizada do Bing com node. js | Documentos da Microsoft'
titlesuffix: Azure Cognitive Services
description: Utilize este guia de introdução para começar a pedir os resultados da pesquisa da sua instância de pesquisa personalizada do Bing com node. js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/04/2018
ms.author: aahi
ms.openlocfilehash: 65b0e6e9fb6f04d4536b642fe58da4e6c0f1aa69
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336586"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Início rápido: Chamar o ponto final de pesquisa personalizada do Bing com node. js

Utilize este guia de introdução para começar a pedir os resultados da pesquisa da sua instância de pesquisa personalizada do Bing. Embora esse aplicativo é escrito em JavaScript, a API de pesquisa personalizada do Bing é um serviço RESTful web compatível com a maioria das linguagens de programação. O código fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de pesquisa personalizada do Bing. Consulte [início rápido: Criar a primeira instância de pesquisa personalizada do Bing](quick-start.md) para obter mais informações.

- [Node.js](https://www.nodejs.org/)

- O [biblioteca de pedido de JavaScript](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro JavaScript em seu IDE ou editor favorito e adicione um `require()` instrução para a biblioteca de pedidos. Crie variáveis para a chave de subscrição, ID de configuração personalizada e um termo de pesquisa. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber um pedido de pesquisa 

1. Crie uma variável para armazenar as informações sendo enviadas no seu pedido. Construir o URL do pedido ao acrescentar o termo de pesquisa para o `q=` parâmetro de consulta e o ID de configuração da sua instância de pesquisa personalizada para `customconfig=`. Separe-os com um `&` caráter. 

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Utilize a biblioteca de pedido de JavaScript para enviar um pedido de pesquisa à sua instância de pesquisa personalizada do Bing e imprimir informações sobre os resultados, incluindo o respetivo nome, url e a data, a página Web foi rastreada pela última vez.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
