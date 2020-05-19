---
title: 'Quickstart: Ligue para o seu ponto final de pesquisa personalizada bing usando node.js [ Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para começar a solicitar resultados de pesquisa da sua instância de pesquisa personalizada Bing utilizando o Node.js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: 34d64db9caefd26adc91471ed67e528a6e3196dc
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199853"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Quickstart: Ligue para o seu ponto final de pesquisa personalizada bing usando node.js

Utilize este quickstart para aprender a solicitar resultados de pesquisa a partir da sua instância de Pesquisa Personalizada Bing. Embora esta aplicação esteja escrita no JavaScript, o Bing Custom Search API é um serviço web restful compatível com a maioria dos idiomas de programação. O código fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de pesquisa personalizada bing. Para mais informações, consulte [Quickstart: Crie a sua primeira instância](quick-start.md)de pesquisa personalizada bing .

- O tempo de execução do [Node.js JavaScript](https://www.nodejs.org/).

- A [biblioteca de pedidos JavaScript.](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

- Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e adicione um `require()` comunicado para a biblioteca de pedidos. Crie variáveis para a sua chave de subscrição, ID de configuração personalizada e termo de pesquisa.

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber um pedido de pesquisa 

1. Crie uma variável para armazenar a informação que está a ser enviada a seu pedido. Construa o URL de pedido, afinando o seu termo de pesquisa ao parâmetro de consulta e o ID de configuração personalizado da sua instância de `q=` pesquisa para o `customconfig=` parâmetro. Separe os parâmetros com uma ampersand ( `&` ). Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final de [subdomínio personalizado](../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

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

1. Utilize a biblioteca de pedidos JavaScript para enviar um pedido de pesquisa para a sua instância de Pesquisa Personalizada Bing e imprimir informações sobre os resultados, incluindo o seu nome, url, e a data em que a página web foi rastreada pela última vez.

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa uma aplicação web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
