---
title: Obter imagens a partir da web - API de pesquisa de imagens do Bing
titleSuffix: Azure Cognitive Services
description: Utilize a API de pesquisa de imagens do Bing para pesquisar e obter imagens relevantes da web.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 309bbca762149f8804742d9ef02d4c3e8dfcdc6b
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542769"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Obter imagens a partir da web com a API de pesquisa de imagens do Bing

Quando utiliza a API de REST de pesquisa de imagens Bing, pode obter imagens da web que estão relacionados com o termo de pesquisa ao enviar o pedido GET seguinte:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Utilize o [p](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) parâmetro de consulta para o termo de pesquisa com codificação url. Por exemplo, se introduzir *velejar dinghies*, defina `q` para `sailing+dinghies` ou `sailing%20dinghies`.

> [!IMPORTANT]
> * Todos os pedidos devem ser feitos a partir de um servidor e não a partir de um cliente.
> * Se for a primeira vez que chamar qualquer uma das APIs de pesquisa da Bing, não inclua o cabeçalho de ID de cliente. Inclua o ID de cliente apenas se já tenha chamado uma API do Bing que devolveu um ID de cliente para o utilizador e a combinação de dispositivo.

## <a name="get-images-from-a-specific-web-domain"></a>Obter imagens de um domínio web específico

Para obter imagens de um domínio específico, utilize o operador de consulta [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> As respostas para consultas com o `site:` operador pode incluir conteúdo para adultos, independentemente do [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) definição. Utilize apenas `site:` se estiver ciente de que o conteúdo no domínio.

## <a name="filter-images"></a>Filtrar imagens

 Por predefinição, a API de pesquisa de imagens devolve todas as imagens que são relevantes para a consulta. Se pretender filtrar as imagens que o Bing devolve (por exemplo, para devolver apenas imagens com um fundo transparente ou tamanho específico), utilize os seguintes parâmetros de consulta:

* [aspecto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)— filtrar imagens por taxa de proporção (por exemplo, imagens de ecrã grande ou padrão).
* [cor](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)— filtrar imagens por cor dominante ou preto e branco.
* [atualização](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)— filtrar imagens por idade (por exemplo, imagens detetadas pelo Bing na última semana).
* [altura](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [largura](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)— filtrar imagens da largura e altura.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)— filtrar imagens por conteúdo (por exemplo, imagens que mostram apenas os rostos de uma pessoa).
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)— filtrar imagens por tipo (por exemplo, clip-art, GIF animados ou planos de fundo transparentes).
* [licença](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)— filtrar imagens pelo tipo de licença associada ao site.
* [tamanho](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)— até 200 x 200 pixels de imagens de imagens de filtro por tamanho, tais como pequenas.

Para obter imagens de um domínio específico, utilize o operador de consulta [site:](https://msdn.microsoft.com/library/ff795613.aspx).

O exemplo seguinte mostra como obter imagens pequenas de ContosoSailing.com Bing detetado na última semana.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Formato de resposta de pesquisa de imagens Bing

A mensagem de resposta do Bing contém um [imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) resposta que contém uma lista de imagens que os serviços cognitivos identificados como sendo relevantes para a consulta. Cada [imagem](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) objeto na lista inclui as seguintes informações sobre a imagem: o URL, seu tamanho, suas dimensões, seu formato de codificação, um URL para uma miniatura da imagem e dimensões a miniatura.

> [!NOTE]
> * Imagens devem ser exibidas na ordem disposta na resposta.
> * Uma vez que formatos de URL e os parâmetros estão sujeitos a alterações sem aviso prévio, utilize todos os URLs como-é. Não deve levar as dependências no formato de URL ou parâmetros exceto onde indicado.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Quando chamar a API de Pesquisa de Imagens do Bing, o Bing devolve uma lista de resultados. A lista é um subconjunto do número total de resultados que são relevantes para a consulta. O campo `totalEstimatedMatches` da resposta contém uma estimativa do número de imagens que estão disponíveis para visualização. Para obter detalhes sobre como a página ao longo do resto das imagens, consulte [paginação imagens](../paging-images.md).

## <a name="next-steps"></a>Passos Seguintes

Se ainda não tiver tentado a API de pesquisa de imagens do Bing antes, experimente um [guia de introdução](../quickstarts/csharp.md). Se estiver procurando por algo mais complexo, experimente o tutorial para criar uma [aplicação web de página única](../tutorial-bing-image-search-single-page-app.md).
