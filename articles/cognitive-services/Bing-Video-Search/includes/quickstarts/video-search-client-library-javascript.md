---
title: Bing Video Search JavaScript biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: fa1ecb24649d9355e6104bff8add5430fdd64c3c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377492"
---
Use este quickstart para começar a procurar notícias com a biblioteca de clientes Bing Video Search para JavaScript. Enquanto a Bing Video Search tem uma API REST compatível com a maioria das linguagens de programação, a biblioteca do cliente fornece uma maneira fácil de integrar o serviço nas suas aplicações. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Contém mais anotações e características.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).
* O [Bing Video Search SDK para JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-videosearch)
     *  Para instalar, executar `npm install @azure/cognitiveservices-videosearch`
* A `CognitiveServicesCredentials` classe do pacote para `@azure/ms-rest-azure-js` autenticar o cliente.
     * Para instalar, executar `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e adicione uma `require()` declaração para a biblioteca e módulo do cliente Bing Video `CognitiveServicesCredentials` Search. Crie uma variável para a sua chave de subscrição. 
    
    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('@azure/cognitiveservices-videosearch');
    ```

2. Crie um exemplo `CognitiveServicesCredentials` com a sua chave. Em seguida, use-o para criar uma instância do cliente de pesquisa de vídeo.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Enviar o pedido de pesquisa

1. Utilize `client.videosOperations.search()` para enviar um pedido de pesquisa para a API de Pesquisa de Vídeo Bing. Quando os resultados da pesquisa forem devolvidos, utilize `.then()` para registar o resultado.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de uma página única](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Veja também 

* [O que é a API de Pesquisa de Vídeos do Bing?](../../overview.md)
* [Serviços cognitivos .NET SDK amostras](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)