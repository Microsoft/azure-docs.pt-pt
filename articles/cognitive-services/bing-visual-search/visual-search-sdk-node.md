---
title: 'Início rápido: obter informações de imagem usando o SDK para node. js-Pesquisa Visual do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para começar a obter informações de imagem do serviço de Pesquisa Visual do Bing, usando o SDK do node. js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 676427ca2985de51767939891b41f413446d4bb1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446571"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>Início rápido: obter informações de imagem usando o SDK do Pesquisa Visual do Bing para node. js

Use este guia de início rápido para começar a obter informações de imagem do serviço de Pesquisa Visual do Bing, usando o SDK do node. js. Embora Pesquisa Visual do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

## <a name="prerequisites"></a>Pré-requisitos
* [Node.js](https://www.nodejs.org/)
* O SDK do Pesquisa Visual do Bing para node. js
    * Para configurar um aplicativo de console usando o SDK do Pesquisa Visual do Bing, execute os seguintes comandos:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-search-visualSearch`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo arquivo JavaScript em seu IDE ou editor favorito e adicione os requisitos a seguir. Em seguida, crie variáveis para sua chave de assinatura, ID de configuração personalizada e caminho de arquivo para a imagem que você deseja carregar. 

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

2. Crie uma instância do cliente.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Pesquisar por imagens

1. Use `fs.createReadStream()` para ler o arquivo de imagem e criar variáveis para os resultados e a solicitação de pesquisa. Em seguida, use o cliente para pesquisar imagens.

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](tutorial-bing-visual-search-single-page-app.md)
