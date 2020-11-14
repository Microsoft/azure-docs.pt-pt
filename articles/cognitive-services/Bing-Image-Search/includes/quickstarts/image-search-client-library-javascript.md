---
title: Bing Image Search JavaScript biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 037137cf5a6e4ddd66fc15e8ad9775ea77177ef6
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625415"
---
Utilize este quickstart para fazer a sua primeira pesquisa de imagem utilizando a biblioteca do cliente Bing Image Search, que é um invólucro para a API e contém as mesmas funcionalidades. Esta aplicação JavaScript simples envia uma consulta de pesquisa de imagens, analisa a resposta JSON e apresenta o URL da primeira imagem devolvida.

O código-fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) com tratamento de erros adicionais e anotações.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).
* O [SDK de pesquisa de imagem Bing para JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-imagesearch)
     *  Para instalar, executar `npm install @azure/cognitiveservices-imagesearch`
* A `CognitiveServicesCredentials` classe do pacote para `@azure/ms-rest-azure-js` autenticar o cliente.
     * Para instalar, executar `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e defina a exatidão, o HTTPS e outros requisitos.

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('@azure/cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    ```

2. No método principal do projeto, crie variáveis para a sua chave de subscrição válida, os resultados de imagens a devolver pelo Bing e um termo de pesquisa. Em seguida, inicie o cliente de pesquisa de imagens com a chave.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

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
> [Bing Image Search single-page app tutorial](../../tutorial-bing-image-search-single-page-app.md) (Tutorial de aplicação de página única da Pesquisa de Imagens do Bing)

## <a name="see-also"></a>Ver também

* [O que é a Pesquisa de Imagens do Bing?](../../overview.md)
* [Experimentar uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Exemplos de Node.js para o SDK dos Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Documentação dos Serviços Cognitivos do Azure](../../../index.yml)
* [Bing Image Search API reference](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)