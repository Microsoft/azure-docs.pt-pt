---
title: 'Início rápido: pesquisar vídeos usando o SDK para node. js-Pesquisa de Vídeo do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para enviar solicitações de pesquisa de vídeo usando o SDK do Pesquisa de Vídeo do Bing para node. js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 10d59da26c4ad583e3cb80b7d5cfc0d569f83ac1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75382621"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>Início rápido: executar uma pesquisa de vídeo com o SDK do Pesquisa de Vídeo do Bing para node. js

Use este guia de início rápido para começar a procurar notícias com o SDK do Pesquisa de Vídeo do Bing para node. js. Embora Pesquisa de Vídeo do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Ele contém mais anotações e recursos.

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://www.nodejs.org/)

Para configurar um aplicativo de console usando o SDK do Pesquisa de Vídeo do Bing:
* Execute `npm install ms-rest-azure` em seu ambiente de desenvolvimento.
* Execute `npm install azure-cognitiveservices-videosearch` em seu ambiente de desenvolvimento.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo arquivo JavaScript em seu IDE ou editor favorito e adicione uma instrução `require()` para o SDK do Pesquisa de Vídeo do Bing e `CognitiveServicesCredentials` módulo. Crie uma variável para sua chave de assinatura. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Crie uma instância do `CognitiveServicesCredentials` com sua chave. Em seguida, use-o para criar uma instância do cliente de pesquisa de vídeo.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Enviar a solicitação de pesquisa

1. Use `client.videosOperations.search()` para enviar uma solicitação de pesquisa para o API de Pesquisa de Vídeo do Bing. Quando os resultados da pesquisa forem retornados, use `.then()` para registrar o resultado.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Ver também 

* [O que é o API de Pesquisa de Vídeo do Bing?](../overview.md)
* [Exemplos de SDK .NET nos serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)