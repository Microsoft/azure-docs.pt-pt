---
title: Enviar consultas de imagem - API de pesquisa de imagens do Bing
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre como personalizar as consultas de pesquisa que são enviados para a API de pesquisa de imagens do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: b2d142783146edcaf40125ce58e43fe001909412
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337656"
---
# <a name="send-queries-to-the-bing-image-search-api"></a>Enviar consultas para a API de pesquisa de imagens do Bing

A API de pesquisa de imagens do Bing fornece uma experiência semelhante à Bing.com/Images. Pode usá-lo para enviar uma consulta de pesquisa para o Bing e obter uma lista de imagens relevantes.

## <a name="use-and-suggest-search-terms"></a>Utilizar e sugerir termos de pesquisa

Depois de introduzir um termo de pesquisa, codificação de URL o termo antes de definir o [ **p** ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) parâmetro de consulta. Por exemplo, se introduzir *velejar dinghies*, defina `q` para `sailing+dinghies` ou `sailing%20dinghies`.

Se a sua aplicação tem uma caixa de pesquisa em que são introduzidos os termos de pesquisa, pode utilizar o [a API de sugestão automática do Bing](../../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API pode apresentar os termos de pesquisa sugerida em tempo real. A API devolve as cadeias de caracteres de consulta sugerida com base em termos de pesquisa parcial e os serviços cognitivos.

## <a name="pivot-the-query"></a>Dinamizar a consulta

Se o Bing pode segmentar a consulta de pesquisa original, retornada [imagens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objeto contém `pivotSuggestions`. Sugestões de dinâmicas podem ser apresentados como termos de pesquisa opcional para o usuário. Por exemplo, se a consulta original era *Microsoft Surface*, Bing pode segmentar a consulta no *Microsoft* e *superfície* e fornecer tabelas dinâmicas sugeridas para cada. Estas sugestões podem ser apresentados como termos de consulta opcionais para o usuário.

O exemplo seguinte mostra as sugestões de pivot para *Microsoft Surface*:  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

O campo `pivotSuggestions` contém a lista de segmentos (pivôs) em que a consulta original foi dividida. Para cada pivô, a resposta contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) que contêm as consultas sugeridas. O `text` campo contém a consulta sugerida. O `displayText` campo contém o termo que substitui o pivô na consulta original. Um exemplo é a data de lançamento de superfície.

Se a cadeia de consulta dinâmica é o que o utilizador que está procurando, utilize o `text` e `thumbnail` cadeias de caracteres de consulta de campos a apresentar o pivô. Tornar a miniatura e texto clicáveis utilizando o `webSearchUrl` URL ou o `searchLink` URL. Utilize `webSearchUrl` para enviar o utilizador para os resultados da pesquisa do Bing. Se fornecer sua própria página de resultados, utilize `searchLink`.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Expanda a consulta

Se o Bing puder expandir a consulta para restringir a pesquisa original, o objeto [Imagens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) contém o campo `queryExpansions`. Por exemplo, se a consulta foi *Microsoft Surface*, as consultas expandidas poderão ser:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **Hub**.

O exemplo seguinte mostra as consultas expandidas para *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

O campo `queryExpansions` contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj). O `text` campo contém a consulta expandida. O `displayText` campo contém o termo de expansão. Se a cadeia de consulta expandida é o que o utilizador que está procurando, utilize o `text` e `thumbnail` campos a apresentar as cadeias de caracteres de consulta expandida. Tornar a miniatura e texto clicáveis utilizando o `webSearchUrl` URL ou o `searchLink` URL. Utilize `webSearchUrl` para enviar o utilizador para os resultados da pesquisa do Bing. Se fornecer sua própria página de resultados, utilize `searchLink`.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Limitar pedidos

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passos Seguintes

Se ainda não tiver tentado a API de pesquisa de imagens do Bing antes, experimente um [guia de introdução](../quickstarts/csharp.md). Se estiver procurando por algo mais complexo, experimente o tutorial para criar uma [aplicação web de página única](../tutorial-bing-image-search-single-page-app.md).
