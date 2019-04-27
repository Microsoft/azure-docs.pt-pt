---
title: 'Início rápido: Procurar vídeos com o SDK de pesquisa de vídeos do Bing para node. js'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de introdução para enviar pedidos de pesquisa de vídeos com o SDK de pesquisa de vídeos do Bing para node. js
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: ffd03a900ec304a930dfc64e7860e28e0374da55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824342"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>Início rápido: Executar uma pesquisa de vídeos com o SDK de pesquisa de vídeos do Bing para node. js

Utilize este guia de introdução para a pesquisa de notícias com o SDK de pesquisa de vídeos do Bing para node. js. Embora a pesquisa de vídeos do Bing tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Ela contém mais anotações e funcionalidades.

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://www.nodejs.org/)

Para configurar uma aplicação de consola com o SDK de pesquisa de vídeos do Bing:
* Executar `npm install ms-rest-azure` no seu ambiente de desenvolvimento.
* Executar `npm install azure-cognitiveservices-videosearch` no seu ambiente de desenvolvimento.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro JavaScript em seu IDE ou editor favorito e adicione um `require()` instrução para o SDK de pesquisa de vídeos do Bing, e `CognitiveServicesCredentials` módulo. Crie uma variável para a sua chave de subscrição. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Criar uma instância do `CognitiveServicesCredentials` com a sua chave. Em seguida, utilizá-lo para criar uma instância do cliente de pesquisa de vídeos.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Enviar a solicitação de pesquisa

1. Utilize `client.videosOperations.search()` para enviar um pedido de pesquisa para a API de pesquisa de vídeos do Bing. Quando os resultados da pesquisa são devolvidos, utilize `.then()` para registar o resultado.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Consulte também 

* [O que é a API de pesquisa de vídeos do Bing?](../overview.md)
* [Exemplos de SDK .NET nos serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)