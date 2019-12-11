---
title: 'Início rápido: chamar seu ponto de extremidade de Pesquisa Personalizada do Bing usando node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para começar a solicitar resultados de pesquisa de sua instância de Pesquisa Personalizada do Bing usando o Node. js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 97bf62c61aee63ce97496e54b99ad641d6150876
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972711"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Início rápido: chamar seu ponto de extremidade de Pesquisa Personalizada do Bing usando node. js

Use este guia de início rápido para começar a solicitar resultados de pesquisa de sua instância de Pesquisa Personalizada do Bing. Embora esse aplicativo seja escrito em JavaScript, o API de Pesquisa Personalizada do Bing é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de Pesquisa Personalizada do Bing. Consulte [início rápido: criar sua primeira instância de pesquisa personalizada do Bing](quick-start.md) para obter mais informações.

- [Node.js](https://www.nodejs.org/)

- A [biblioteca de solicitações do JavaScript](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo arquivo JavaScript em seu IDE ou editor favorito e adicione uma instrução `require()` para a biblioteca de solicitações. Crie variáveis para sua chave de assinatura, ID de configuração personalizada e um termo de pesquisa. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber uma solicitação de pesquisa 

1. Crie uma variável para armazenar as informações que estão sendo enviadas em sua solicitação. Construa a URL de solicitação acrescentando seu termo de pesquisa ao parâmetro de consulta `q=` e a ID de configuração personalizada da sua instância de pesquisa para `customconfig=`. Separe os parâmetros com um caractere de `&`. Você pode usar o ponto de extremidade global abaixo ou o ponto de extremidade de [subdomínio personalizado](../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.

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

1. Use a biblioteca de solicitações do JavaScript para enviar uma solicitação de pesquisa para sua instância do Pesquisa Personalizada do Bing e imprimir informações sobre os resultados, incluindo seu nome, URL e a data em que a página da Web foi rastreada pela última vez.

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
> [Criar um aplicativo Web de pesquisa personalizado](./tutorials/custom-search-web-page.md)
