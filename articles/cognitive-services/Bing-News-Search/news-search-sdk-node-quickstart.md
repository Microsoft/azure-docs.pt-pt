---
title: 'Quickstart: Perform a news search using the SDK for Node.js - Bing News Search'
titleSuffix: Azure Cognitive Services
description: Use this quickstart to search for news using the Bing News Search SDK for Node.js, and process the response.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 9bbad7685cf7d562189871bb5fa4a56d69287db4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383891"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Quickstart: Perform a news search with the Bing News Search SDK for Node.js

Use this quickstart to begin searching for news with the Bing News Search SDK for Node.js. While Bing News Search has a REST API compatible with most programming languages, the SDK provides an easy way to integrate the service into your applications. The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/)

To set up a console application using the Bing News Search SDK:
1. Run `npm install ms-rest-azure` in your development environment.
2. Run `npm install azure-cognitiveservices-newssearch` in your development environment.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie uma instância de `CognitiveServicesCredentials`. Create variables for your subscription key, and a search term.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. instantiate the client:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Send a search query

1. Use the client to search with a query term, in this case "Winter Olympics":
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

O código imprime `result.value` itens na consola sem analisar o texto. Os resultados, se existirem por categoria, incluirão:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](tutorial-bing-news-search-single-page-app.md)
