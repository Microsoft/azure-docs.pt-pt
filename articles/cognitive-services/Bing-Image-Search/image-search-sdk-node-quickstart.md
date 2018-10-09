---
title: 'Guia de Início Rápido: procurar imagens com o SDK de Pesquisa de Imagens do Bing para Node.js'
description: Utilize este guia de início rápido para criar a sua primeira pesquisa de imagens com o SDK de Pesquisa de Imagens do Bing, um wrapper para a API com as mesmas funcionalidades. Esta aplicação Node.js simples envia uma consulta de pesquisa de imagens, analisa a resposta JSON e apresenta o URL da primeira imagem devolvida.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 2fa3a221d42a522f19b00a4cb5596d93039a85ac
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220229"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-nodejs"></a>Guia de Início Rápido: procurar imagens com o SDK de Pesquisa de Imagens do Bing e Node.js

Utilize este guia de início rápido para criar a sua primeira pesquisa de imagens com o SDK de Pesquisa de Imagens do Bing, um wrapper para a API com as mesmas funcionalidades. Esta aplicação JavaScript simples envia uma consulta de pesquisa de imagens, analisa a resposta JSON e apresenta o URL da primeira imagem devolvida.

O código fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) com processamento de erros e anotações adicionais.

## <a name="prerequisites"></a>Pré-requisitos

* O [SDK de Pesquisa de Imagens dos Serviços Cognitivos para Node.js](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * Instalar com `npm install azure-cognitiveservices-imagesearch`
* O módulo [Node.js Azure Rest](https://www.npmjs.com/package/ms-rest-azure)
    * Instalar com `npm install ms-rest-azure`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e defina a exatidão, o HTTPS e outros requisitos.

    ```javascript
    'use strict';
    https = require('https');
    const Search = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    ```

2. No método principal do projeto, crie variáveis para a sua chave de subscrição válida, os resultados de imagens a devolver pelo Bing e um termo de pesquisa. Em seguida, inicie o cliente de pesquisa de imagens com a chave.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client 
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new Search.ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Criar uma função auxiliar assíncrona

1. Crie uma função para chamar o cliente de forma assíncrona e devolver a resposta do serviço Pesquisa de Imagens do Bing.  
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
## <a name="send-a-query-and-handle-the-response"></a>Enviar uma consulta e processar a resposta

1. Chame a função auxiliar e processe `promise` para analisar os resultados de imagens devolvidos na resposta.

    Se a resposta contiver os resultados da pesquisa, armazene o primeiro resultado e imprima os detalhes, como um URL de miniatura, o URL original, juntamente com o número total de imagens devolvidas.  
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial de aplicação de página única de Pesquisa de Imagens do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Consulte também

* [O que é a Pesquisa de Imagens do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Experimentar uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obter uma chave de acesso aos Serviços Cognitivos gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Exemplos de Node.js para o SDK dos Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Documentação dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)
