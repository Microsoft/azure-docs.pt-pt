---
title: Bing News Search JavaScript biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: d9234d4a184a0a09ff8c4a7c08e4035668870e22
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91304100"
---
Use este quickstart para começar a procurar notícias com a biblioteca de clientes Bing News Search para JavaScript. Embora a Bing News Search tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca do cliente proporciona uma forma fácil de integrar o serviço nas suas aplicações. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/)

Para configurar uma aplicação de consola utilizando a biblioteca do cliente Bing News Search:
1. Corra `npm install ms-rest-azure` no seu ambiente de desenvolvimento.
2. Corra `npm install azure-cognitiveservices-newssearch` no seu ambiente de desenvolvimento.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie uma instância de `CognitiveServicesCredentials`. Crie variáveis para a sua chave de subscrição e um termo de pesquisa.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. instantaneamente o cliente:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Enviar uma consulta de pesquisa

1. Use o cliente para pesquisar com um termo de consulta, neste caso "Jogos Olímpicos de inverno":
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

O código imprime `result.value` itens na consola sem analisar o texto. Os resultados, se existirem por categoria, incluirão:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../../tutorial-bing-news-search-single-page-app.md)
