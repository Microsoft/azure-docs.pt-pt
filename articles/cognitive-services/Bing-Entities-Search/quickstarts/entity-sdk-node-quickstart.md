---
title: 'Início rápido: Enviar um pedido de pesquisa com o SDK de pesquisa de entidades do Bing para node. js'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de introdução para procurar entidades com o SDK de pesquisa de entidades do Bing para node. js
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 015a2d344b066bd7b65c3228a2795c3395793f2b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097035"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Início rápido: Enviar um pedido de pesquisa com o SDK de pesquisa de entidades do Bing para node. js

Utilize este guia de introdução para começar a procurar entidades com o SDK de pesquisa de entidades do Bing para node. js. Embora a pesquisa de entidades do Bing tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* O [de pesquisa de entidades do Bing SDK para node. js](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

Para instalar o SDK de pesquisa de entidades do Bing:

1. Executar `npm install ms-rest-azure` no seu ambiente de desenvolvimento.
2. Executar `npm install azure-cognitiveservices-entitysearch` no seu ambiente de desenvolvimento.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Criar um novo ficheiro JavaScript no seu IDE ou editor favorito e adicione os seguintes requisitos. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Criar uma instância do `CognitiveServicesCredentials` utilizando a sua chave de subscrição. Em seguida, crie uma instância do cliente de pesquisa com o mesmo.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Enviar um pedido e receber uma resposta

1. Enviar um pedido de pesquisa de entidades com `entitiesOperations.search()`. Depois de receber uma resposta, imprimir o `queryContext`, o número de resultados retornados e a descrição do primeiro resultado.
      
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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de pesquisa de entidades do Bing?](../overview.md )