---
title: 'Quickstart: Search for videos using the SDK for Node.js - Bing Video Search'
titleSuffix: Azure Cognitive Services
description: Use this quickstart to send video search requests using the Bing Video Search SDK for Node.js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: aahi
ms.openlocfilehash: 5c8bd4ccadcc3c1947905e6bd74b48045a62ab57
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383745"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>Quickstart: Perform a video search with the Bing Video Search SDK for Node.js

Use this quickstart to begin searching for news with the Bing Video Search SDK for Node.js. While Bing Video Search has a REST API compatible with most programming languages, the SDK provides an easy way to integrate the service into your applications. The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). It contains more annotations and features.

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://www.nodejs.org/)

To set up a console application using the Bing Video Search SDK:
* Run `npm install ms-rest-azure` in your development environment.
* Run `npm install azure-cognitiveservices-videosearch` in your development environment.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Create a new JavaScript file in your favorite IDE or editor, and add a `require()` statement for the Bing Video Search SDK, and `CognitiveServicesCredentials` module. Create a variable for your subscription key. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Create an instance of `CognitiveServicesCredentials` with your key. Then use it to create an instance of the video search client.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Send the search request

1. Use `client.videosOperations.search()` to send a search request to the Bing Video Search API. When the search results are returned, use `.then()` to log the result.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Create a single page web app](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Ver também 

* [What is the Bing Video Search API?](../overview.md)
* [Exemplos de SDK .NET nos serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)