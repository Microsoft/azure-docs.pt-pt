---
title: Pesquisa de vídeos utilizando a API de Pesquisa de Vídeo Bing
titleSuffix: Azure Cognitive Services
description: O Bing Video Search APIfinds e devolve vídeos relevantes da web, fornece várias funcionalidades para a recuperação de vídeo inteligente e focada na web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 10277efe1f06de3633b2d614e2ee5ec0cc351c76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351934"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Pesquisa de vídeos com a API de Pesquisa de Vídeo Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

A API de Pesquisa de Vídeo Bing facilita a integração das capacidades de pesquisa de notícias cognitivas de Bing nas suas aplicações. Embora a API encontre e devolva vídeos relevantes da web, fornece várias funcionalidades para a recuperação de vídeo inteligente e focada na web.

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

Para obter vídeos de um domínio específico, utilize o operador de consulta [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

A resposta contém uma resposta [Vídeos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) que contém uma lista de vídeos que o Bing considerou relevantes para a consulta. Cada objeto de [Vídeo](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) na lista inclui o URL, a duração, as dimensões e o formato de codificação do vídeo, entre outros atributos. O objeto de vídeo também inclui o URL de uma miniatura do vídeo e as dimensões da miniatura.

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

Pode exibir tudo ou um subconjunto das miniaturas de vídeo devolvidas pela API de Pesquisa de Vídeo Bing. Se for apresentado um subconjunto, dê ao utilizador uma opção para ver os vídeos restantes. como parte dos requisitos de [utilização e visualização](../../bing-web-search/use-display-requirements.md)da API Bing, deve exibir os vídeos na ordem fornecida na resposta. Para obter informações sobre o redimensionamento da miniatura, veja [Redimensionar e Recortar Miniaturas](../../bing-web-search/resize-and-crop-thumbnails.md). 

À medida que o utilizador coloca o cursor sobre a miniatura, pode utilizar [motionThumbnailUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl) para reproduzir uma versão em miniatura do vídeo. Certifique-se de que adiciona um atributo à miniatura de movimento quando a apresentar.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Quando uma miniatura é clicada, existem três opções para visualizar o vídeo:

- Utilize [hostPageUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl) para ver o vídeo no site anfitrião (por exemplo, o YouTube)
- Utilize [webSearchUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl) para ver o vídeo no browser de vídeos do Bing
- Utilize [embdedHtml](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml) para incorporar o vídeo na sua própria experiência 

Certifique-se de que utiliza o editor e criador para adicionar um atributo ao vídeo quando o reproduzir.

Para obter detalhes sobre como utilizar o [videoId](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid) para obter informações sobre o vídeo, veja [Informações de Vídeo](../video-insights.md).

## <a name="filtering-videos"></a>Filtrar vídeos

Por predefinição, a API de Pesquisa de Vídeos devolve todos os vídeos que são relevantes para a consulta. Se quiser apenas vídeos gratuitos ou com menos de cinco minutos de duração, utilize os seguintes parâmetros de consulta de filtro:

- [preços](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing) &mdash; Filtrar vídeos por preços (por exemplo, vídeos gratuitos ou que tenha de pagar)
- [resolução](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution) &mdash; Filtrar vídeos por resolução (por exemplo, vídeos com uma resolução de 720p ou superior)
- [videoLength](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength) &mdash; Filtrar vídeos por comprimento de vídeo (por exemplo, vídeos com menos de cinco minutos de comprimento)
- [frescura](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness) &mdash; Filtrar vídeos por idade (por exemplo, vídeos descobertos por Bing na semana passada)

Para obter vídeos de um domínio específico, inclua o operador de consulta [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) na cadeia de consulta.

> [!NOTE]
> Consoante a consulta, se utilizar o operador `site:`, existe a possibilidade de a resposta incluir conteúdo para adultos, independentemente da definição [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch). Deve utilizar `site:` apenas se tiver conhecimento do conteúdo do site e se o seu cenário suportar a possibilidade de conteúdo para adultos.

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

Se o Bing conseguir expandir a consulta para restringir a pesquisa original, o objeto [Vídeos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) irá conter o campo `queryExpansions`. Por exemplo, se a consulta tiver sido *Limpeza de Algeroz*, as consultas expandidas poderão ser: **Ferramentas** de Limpeza de Algerozes, Limpeza de Algerozes **a Partir do Chão**, **Máquina** de Limpeza de Algerozes e **Fácil** Limpeza de Algerozes.

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

O campo `queryExpansions` contém uma lista de objetos de [Consulta](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj). O campo `text` contém a consulta expandida e o campo `displayText` contém o termo de expansão. Pode utilizar os campos text (texto) e thumbnail (miniatura) para apresentar as cadeias de consulta expandidas ao utilizador no caso de a cadeia de consulta expandida ser realmente aquilo que procura. Torne a miniatura e o texto clicáveis com o URL `webSearchUrl` ou o URL `searchLink`. Utilize `webSearchUrl` para encaminhar o utilizador para os resultados de pesquisa do Bing ou `searchLink` se fornecer a sua própria página de resultados.

## <a name="pivoting-the-query"></a>Dinamizar a consulta

Se o Bing puder segmentar a consulta de pesquisa original, o objeto [Vídeos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contém o campo `pivotSuggestions`. Por exemplo, se a consulta original era *Limpeza de Algerozes*, o Bing poderá segmentar a consulta em *Limpeza* e *Algerozes*.

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

Para cada pivô, a resposta contém uma lista de objetos de [Consulta](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) que contêm as consultas sugeridas. O campo `text` contém a consulta sugerida e o campo `displayText` contém o termo que substitui o pivô na consulta original. Por exemplo, Limpeza de Janelas.

Pode utilizar os campos `text` e `thumbnail` para apresentar as cadeias de consulta expandidas ao utilizador no caso de a cadeia de consulta expandida ser realmente aquilo que procura. Torne a miniatura e o texto clicáveis com o URL `webSearchUrl` ou o URL `searchLink`. Utilize `webSearchUrl` para encaminhar o utilizador para os resultados de pesquisa do Bing ou `searchLink` se fornecer a sua própria página de resultados.

## <a name="throttling-requests"></a>Limitar pedidos

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]