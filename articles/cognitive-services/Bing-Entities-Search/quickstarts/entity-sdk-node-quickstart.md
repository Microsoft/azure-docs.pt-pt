---
title: 'Início rápido: Enviar uma solicitação de pesquisa com o SDK para node. js-Pesquisa de Entidade do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para pesquisar entidades com o SDK do Pesquisa de Entidade do Bing para node. js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 9fdb1e68a413adcbf5d27ec3a90cd79fdeb29f21
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75384387"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Início rápido: Enviar uma solicitação de pesquisa com o SDK do Pesquisa de Entidade do Bing para node. js

Use este guia de início rápido para começar a procurar entidades com o SDK do Pesquisa de Entidade do Bing para node. js. Embora Pesquisa de Entidade do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* O [SDK do pesquisa de entidade do Bing para node. js](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

Para instalar o SDK do Pesquisa de Entidade do Bing:

1. Execute `npm install ms-rest-azure` em seu ambiente de desenvolvimento.
2. Execute `npm install azure-cognitiveservices-entitysearch` em seu ambiente de desenvolvimento.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo arquivo JavaScript em seu IDE ou editor favorito e adicione os requisitos a seguir. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Crie uma instância do `CognitiveServicesCredentials` usando sua chave de assinatura. Em seguida, crie uma instância do cliente de pesquisa com ele.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Enviar uma solicitação e receber uma resposta

1. Envie uma solicitação de pesquisa de entidades com `entitiesOperations.search()`. Depois de receber uma resposta, imprima a `queryContext`, o número de resultados retornados e a descrição do primeiro resultado.
      
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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é o API de Pesquisa de Entidade do Bing?](../overview.md )