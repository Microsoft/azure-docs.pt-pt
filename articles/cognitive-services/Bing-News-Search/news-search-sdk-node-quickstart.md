---
title: 'Início rápido: executar uma pesquisa de notícias usando o SDK para node. js-Pesquisa de Notícias do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para pesquisar notícias usando o SDK do Pesquisa de Notícias do Bing para node. js e processar a resposta.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 9bbad7685cf7d562189871bb5fa4a56d69287db4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383891"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Início rápido: executar uma pesquisa de notícias com o SDK do Pesquisa de Notícias do Bing para node. js

Use este guia de início rápido para começar a procurar notícias com o SDK do Pesquisa de Notícias do Bing para node. js. Embora Pesquisa de Notícias do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/)

Para configurar um aplicativo de console usando o SDK do Pesquisa de Notícias do Bing:
1. Execute `npm install ms-rest-azure` em seu ambiente de desenvolvimento.
2. Execute `npm install azure-cognitiveservices-newssearch` em seu ambiente de desenvolvimento.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie uma instância de `CognitiveServicesCredentials`. Crie variáveis para sua chave de assinatura e um termo de pesquisa.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. criar instância do cliente:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Enviar uma consulta de pesquisa

1. Use o cliente para pesquisar com um termo de consulta, neste caso, "Olimpíadas de inverno":
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

O código imprime `result.value` itens para a consola sem analisar qualquer texto. Os resultados, se ocorrerem por categoria, irão incluir:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](tutorial-bing-news-search-single-page-app.md)
