---
title: 'Início rápido: Procurar vídeos com a API de REST de pesquisa de vídeo Bing eC#'
titlesuffix: Azure Cognitive Services
description: Utilize este guia de introdução para enviar pedidos de pesquisa de vídeos para utilizar a API de REST de pesquisa do Bing vídeos C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: f4b42ff2639bf7e6a982f7c21b6ac4f2a8c84c5e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538923"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-c"></a>Início rápido: Procurar vídeos com a API de REST de pesquisa de vídeo Bing eC#

Utilize este guia de introdução para efetuar a primeira chamada para a API de pesquisa de vídeos do Bing e exibe um resultado de pesquisa da resposta JSON. Esta simples C# aplicação envia uma consulta de pesquisa de vídeos de HTTP para a API e exibe a resposta. Apesar de esta aplicação estar escrita em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

O código-fonte para este exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingVideoSearchv7.cs) com tratamento de erros adicionais, recursos e anotações de código.

## <a name="prerequisites"></a>Pré-requisitos
* Qualquer edição do [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* O framework [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote NuGet.
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie uma nova solução de consola no Visual Studio. Em seguida, adicione os seguintes espaços de nomes ao ficheiro de código principal.

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Adicione as variáveis para a sua chave de subscrição, o ponto final e a pesquisa termo.

    ```csharp
    const string accessKey = "enter your key here";
    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    const string searchTerm = "kittens";
    ```

### <a name="create-a-struct-to-format-the-bing-video-search-api-response"></a>Criar uma estrutura para formatar a resposta de API de pesquisa de vídeos do Bing

3. Defina uma estrutura `SearchResult` para conter os resultados da pesquisa de imagens e as informações do cabeçalho JSON.

    ```csharp
    struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }
    ```

## <a name="create-and-handle-a-video-search-request"></a>Criar e gerenciar a solicitação de pesquisa de vídeos

Crie um método com o nome `BingVideoSearch` para efetuar a chamada à API e defina o tipo de retorno para a estrutura `SearchResult` criada anteriormente. No método, execute os seguintes passos:

1. Construa o URI do pedido de pesquisa. Tenha em atenção que o toSearch do termo de pesquisa tem de ser formatado antes de a ser acrescentado à cadeia de caracteres.

    ```csharp
    
    static SearchResult BingVideoSearch(string toSearch){
    
        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(toSearch);
    //...
    ```

2. Executar a solicitação da web ao adicionar a chave para o `Ocp-Acpim-Subscription-Key` cabeçalho e a utilizar um `HttpWebResponse` objeto para armazenar a resposta de API. Em seguida, utilize um `StreamReader` para obter a cadeia de caracteres do JSON.

    ```csharp
    //...
    WebRequest request = HttpWebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    //...
    ```

## <a name="process-the-result"></a>Processar o resultado

1. Crie o objeto do resultado de pesquisa e extraia os cabeçalhos HTTP do Bing. Em seguida, volte a `searchResult` objeto. 

    ```csharp
    var searchResult = new SearchResult();
    searchResult.jsonResult = json;
    searchResult.relevantHeaders = new Dictionary<String, String>();

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

2. Em seguida, pode imprimir a resposta.

    ```csharp
    Console.WriteLine(result.jsonResult);
    ```

## <a name="example-json-response"></a>Resposta JSON de exemplo 

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte:

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Ver também 

 [O que é a API de pesquisa de vídeos do Bing?](../overview.md)
