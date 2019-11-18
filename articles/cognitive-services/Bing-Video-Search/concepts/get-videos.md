---
title: Pesquisar vídeos usando o API de Pesquisa de Vídeo do Bing
titleSuffix: Azure Cognitive Services
description: O Pesquisa de Vídeo do Bing APIfinds e retorna vídeos relevantes da Web, fornece vários recursos para recuperação de vídeo inteligente e focada na Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 5add9597924aa77ede875d0056e83eceb4f99598
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111435"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Pesquisar vídeos com o API de Pesquisa de Vídeo do Bing

O API de Pesquisa de Vídeo do Bing facilita a integração dos recursos de pesquisa de notícias cognitivas do Bing em seus aplicativos. Embora a API Localize principalmente e retorne vídeos relevantes da Web, ela fornece vários recursos para recuperação de vídeo inteligente e focada na Web.

## <a name="getting-videos"></a>Obter vídeos

Para obter vídeos relacionados com o termo de pesquisa do utilizador a partir da Web, envie o seguinte pedido GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Todos os pedidos têm de ser feitos a partir de um servidor.

Se for a primeira vez que está a chamar qualquer uma das APIs do Bing, não inclua o cabeçalho de ID de cliente. Inclua apenas o ID de cliente se tiver chamado anteriormente uma API do Bing e o Bing tiver devolvido um ID de cliente para o utilizador e a combinação de dispositivo.

Para obter vídeos de um domínio específico, utilize o operador de consulta [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

A resposta contém uma resposta [Vídeos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) que contém uma lista de vídeos que o Bing considerou relevantes para a consulta. Cada objeto de [Vídeo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) na lista inclui o URL, a duração, as dimensões e o formato de codificação do vídeo, entre outros atributos. O objeto de vídeo também inclui o URL de uma miniatura do vídeo e as dimensões da miniatura.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

## <a name="video-thumbnails"></a>Miniaturas de vídeo

Você pode exibir todos ou um subconjunto das miniaturas de vídeo retornadas pelo API de Pesquisa de Vídeo do Bing. Se for apresentado um subconjunto, dê ao utilizador uma opção para ver os vídeos restantes. como parte dos [requisitos de uso e exibição](../UseAndDisplayRequirements.md)da API do Bing, você deve exibir os vídeos na ordem fornecida na resposta. Para obter informações sobre o redimensionamento da miniatura, veja [Redimensionar e Recortar Miniaturas](../../bing-web-search/resize-and-crop-thumbnails.md). 

À medida que o utilizador coloca o cursor sobre a miniatura, pode utilizar [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl) para reproduzir uma versão em miniatura do vídeo. Certifique-se de que adiciona um atributo à miniatura de movimento quando a apresentar.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Quando uma miniatura é clicada, há três opções para exibir o vídeo:

- Utilize [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl) para ver o vídeo no site anfitrião (por exemplo, o YouTube)
- Utilize [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl) para ver o vídeo no browser de vídeos do Bing
- Utilize [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml) para incorporar o vídeo na sua própria experiência 

Certifique-se de que utiliza o editor e criador para adicionar um atributo ao vídeo quando o reproduzir.

Para obter detalhes sobre como utilizar o [videoId](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid) para obter informações sobre o vídeo, veja [Informações de Vídeo](../video-insights.md).

## <a name="filtering-videos"></a>Filtrar vídeos

Por predefinição, a API de Pesquisa de Vídeos devolve todos os vídeos que são relevantes para a consulta. Se quiser apenas vídeos gratuitos ou com menos de cinco minutos de duração, utilize os seguintes parâmetros de consulta de filtro:

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing) – filtrar os vídeos por preço (por exemplo, vídeos gratuitos ou pelos quais tenha de pagar)
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution) – filtrar vídeos por resolução (por exemplo, vídeos com uma resolução de 720p ou superior)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength) – filtrar vídeos por duração do vídeo (por exemplo, vídeos com menos de cinco minutos de duração)
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness) – filtrar vídeos por antiguidade (por exemplo, vídeos descobertos pelo Bing na semana anterior)

Para obter vídeos de um domínio específico, inclua o operador de consulta [site:](https://msdn.microsoft.com/library/ff795613.aspx) na cadeia de consulta.

> [!NOTE]
> Consoante a consulta, se utilizar o operador `site:`, existe a possibilidade de a resposta incluir conteúdo para adultos, independentemente da definição [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch). Deve utilizar `site:` apenas se tiver conhecimento do conteúdo do site e se o seu cenário suportar a possibilidade de conteúdo para adultos.

O exemplo seguinte mostra como obter vídeos gratuitos de ContosoSailing.com que tenham uma resolução de 720p ou superior e que o Bing tenha descoberto no mês anterior.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Expandir a consulta

Se o Bing conseguir expandir a consulta para restringir a pesquisa original, o objeto [Vídeos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) irá conter o campo `queryExpansions`. Por exemplo, se a consulta tiver sido *Limpeza de Algeroz*, as consultas expandidas poderão ser: **Ferramentas** de Limpeza de Algerozes, Limpeza de Algerozes **a Partir do Chão**, **Máquina** de Limpeza de Algerozes e **Fácil** Limpeza de Algerozes.

O exemplo seguinte mostra as consultas expandidas para *Limpeza de Algerozes*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

O campo `queryExpansions` contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj). O campo `text` contém a consulta expandida e o campo `displayText` contém o termo de expansão. Pode utilizar os campos text (texto) e thumbnail (miniatura) para apresentar as cadeias de consulta expandidas ao utilizador no caso de a cadeia de consulta expandida ser realmente aquilo que procura. Torne a miniatura e o texto clicáveis com o URL `webSearchUrl` ou o URL `searchLink`. Utilize `webSearchUrl` para encaminhar o utilizador para os resultados de pesquisa do Bing ou `searchLink` se fornecer a sua própria página de resultados.

## <a name="pivoting-the-query"></a>Dinamizar a consulta

Se o Bing puder segmentar a consulta de pesquisa original, o objeto [Vídeos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contém o campo `pivotSuggestions`. Por exemplo, se a consulta original era *Limpeza de Algerozes*, o Bing poderá segmentar a consulta em *Limpeza* e *Algerozes*.

O exemplo seguinte mostra as sugestões de dinamização para *Limpeza de Algerozes*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

Para cada pivô, a resposta contém uma lista de objetos de [Consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) que contêm as consultas sugeridas. O campo `text` contém a consulta sugerida e o campo `displayText` contém o termo que substitui o pivô na consulta original. Por exemplo, Limpeza de Janelas.

Pode utilizar os campos `text` e `thumbnail` para apresentar as cadeias de consulta expandidas ao utilizador no caso de a cadeia de consulta expandida ser realmente aquilo que procura. Torne a miniatura e o texto clicáveis com o URL `webSearchUrl` ou o URL `searchLink`. Utilize `webSearchUrl` para encaminhar o utilizador para os resultados de pesquisa do Bing ou `searchLink` se fornecer a sua própria página de resultados.

## <a name="throttling-requests"></a>Limitar pedidos

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]
