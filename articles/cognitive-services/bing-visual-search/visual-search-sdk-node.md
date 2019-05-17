---
title: 'Início rápido: Obtenha informações de imagem com o SDK de pesquisa Visual do Bing para node. js'
titleSuffix: Azure Cognitive Services
description: Carregar uma imagem usando o SDK de pesquisa Visual do Bing e obter conhecimentos sobre ele.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: v-gedod
ms.openlocfilehash: 1bcd885d744cd2c97b0941b5a3bb498d68b5f644
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796311"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>Início rápido: Obtenha informações de imagem com o SDK de pesquisa Visual do Bing para node. js

Utilize este guia de introdução para iniciar a obtenção de informações de imagem do serviço de pesquisa Visual do Bing, utilizando o SDK de node. js. Embora a pesquisa Visual do Bing tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

## <a name="prerequisites"></a>Pré-requisitos
* [Node.js](https://www.nodejs.org/)
* A pesquisa de Visual do Bing SDK para node. js
    * Para configurar uma aplicação de consola com o SDK de pesquisa Visual do Bing, execute os seguintes comandos:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-search-visualSearch`.


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Criar um novo ficheiro JavaScript no seu IDE ou editor favorito e adicione os seguintes requisitos. Em seguida, crie variáveis para a sua chave de subscrição, o ID de configuração personalizada e o caminho de ficheiro para a imagem que pretende carregar. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Criar uma instância do cliente.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Pesquisa de imagens

1. Utilize `fs.createReadStream()` para ler em seu arquivo de imagem e criar variáveis para sua solicitação de pesquisa e os resultados. Em seguida, utilize o cliente para imagens de pesquisa.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. Analise os resultados da consulta anterior:

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](tutorial-bing-visual-search-single-page-app.md)
