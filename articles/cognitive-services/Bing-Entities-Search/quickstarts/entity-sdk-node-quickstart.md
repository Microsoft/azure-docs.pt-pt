---
title: 'Quickstart: Send a search request with the SDK for Node.js - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Use this quickstart to search for entities with the Bing Entity Search SDK for Node.js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 86c0507c3796693f29170f6059d7774312c2b3f0
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74323835"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Quickstart: Send a search request with the Bing Entity Search SDK for Node.js

Use this quickstart to begin searching for entities with the Bing Entity Search SDK for Node.js. While Bing Entity Search has a REST API compatible with most programming languages, the SDK provides an easy way to integrate the service into your applications. The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* The [Bing Entity Search SDK for Node.js](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

To install the Bing Entity Search SDK:

1. Run `npm install ms-rest-azure` in your development environment.
2. Run `npm install azure-cognitiveservices-entitysearch` in your development environment.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Create a new JavaScript file in your favorite IDE or editor, and add the following requirements. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Create an instance of `CognitiveServicesCredentials` using your subscription key. Then create an instance of the search client with it.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Send a request and receive a response

1. Send an entities search request with `entitiesOperations.search()`. After receiving a response, print out the `queryContext`, number of returned results, and the description of the first result.
      
    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [What is the Bing Entity Search API?](../overview.md )