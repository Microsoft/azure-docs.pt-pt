---
title: Bing Visual Search JavaScript biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: a87a5f7208e4966b6afba0b939be42726ca20365
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371738"
---
Utilize este quickstart para começar a obter insights de imagem do serviço de Pesquisa Visual Bing, utilizando a biblioteca de clientes JavaScript. Embora a Bing Visual Search tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca do cliente proporciona uma forma fácil de integrar o serviço nas suas aplicações. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

[Documentação de referência](/javascript/api/@azure/cognitiveservices-visualsearch/?preserve-view=true&view=azure-node-latest)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-visualsearch)  |  [Pacote (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).
* O [Bing Visual Search SDK para JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch)
     *  Para instalar, executar `npm install @azure/cognitiveservices-visualsearch`
* A `CognitiveServicesCredentials` classe do pacote para `@azure/ms-rest-azure-js` autenticar o cliente.
     * Para instalar, executar `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e adicione os seguintes requisitos. Em seguida, crie variáveis para a sua chave de subscrição, ID de configuração personalizada e caminho de arquivo para a imagem que pretende carregar. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('@azure/cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Instantiou o cliente.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Pesquisa de imagens

1. Use `fs.createReadStream()` para ler no seu ficheiro de imagem e crie variáveis para o seu pedido de pesquisa e resultados. Em seguida, use o cliente para pesquisar imagens.

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
> [Criar uma aplicação web de página única](../../tutorial-bing-visual-search-single-page-app.md)