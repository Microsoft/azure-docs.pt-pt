---
title: Obtenha imagens da web - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Utilize a API de Pesquisa de Imagem Bing para procurar e obter imagens relevantes a partir da web.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67542769"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Obtenha imagens da web com a API de Pesquisa de Imagem Bing

Quando utilizar a API de Pesquisa de Imagem Bing, pode obter imagens da web que estejam relacionadas com o seu termo de pesquisa enviando o seguinte pedido GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Utilize o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) para o seu termo de pesquisa codificado por url. Por exemplo, se entrar em *botes de vela,* definido `q` para `sailing+dinghies` ou `sailing%20dinghies`.

> [!IMPORTANT]
> * Todos os pedidos devem ser feitos a partir de um servidor, e não de um cliente.
> * Se é a primeira vez que liga para qualquer uma das APIs de pesquisa do Bing, não inclua o cabeçalho de identificação do cliente. Apenas inclua o ID do cliente se já ligou para um Bing API que devolveu um ID do cliente para a combinação de utilizador e dispositivo.

## <a name="get-images-from-a-specific-web-domain"></a>Obtenha imagens de um domínio web específico

Para obter imagens de um domínio específico, utilize o operador de consulta [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> As respostas às `site:` consultas que utilizam o operador podem incluir conteúdo adulto, independentemente da definição [de segurançaSearch.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) Só `site:` utilize se tiver conhecimento do conteúdo no domínio.

## <a name="filter-images"></a>Filtrar imagens

 Por padrão, a Image Search API devolve todas as imagens relevantes para a consulta. Se pretender filtrar as imagens que bing retorna (por exemplo, para devolver apenas imagens com um fundo transparente ou tamanho específico), utilize os seguintes parâmetros de consulta:

* [aspeto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)— Filtrar imagens por relação de aspeto (por exemplo, imagens de ecrã padrão ou de ecrã largo).
* [cor](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)— Filtrar imagens por cor dominante ou preto e branco.
* [frescura](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)— Filtrar imagens por idade (por exemplo, imagens descobertas por Bing na semana passada).
* [altura,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height) [largura](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)— Filtrar imagens por largura e altura.
* [imagemConteúdo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)— Filtrar imagens por conteúdo (por exemplo, imagens que mostram apenas o rosto de uma pessoa).
* [imagemTipo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)— Filtrar imagens por tipo (por exemplo, clip art, GIFs animados ou fundos transparentes).
* [licença](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)— Filtrar imagens pelo tipo de licença associada ao site.
* [tamanho](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)— Filtrar imagens por tamanho, como pequenas imagens até 200x200 pixels.

Para obter imagens de um domínio específico, utilize o operador de consulta [site:](https://msdn.microsoft.com/library/ff795613.aspx).

O exemplo que se segue mostra como obter pequenas imagens de ContosoSailing.com que Bing descobriu na semana passada.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Formato de resposta de pesquisa de imagem bing

A mensagem de resposta de Bing contém uma resposta [imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) que contém uma lista de imagens que os Serviços Cognitivos determinaram ser relevantes para a consulta. Cada objeto [de imagem](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) na lista inclui as seguintes informações sobre a imagem: o URL, o seu tamanho, as suas dimensões, o seu formato de codificação, um URL para uma miniatura da imagem e as dimensões da miniatura.

> [!NOTE]
> * As imagens devem ser apresentadas na ordem fornecida na resposta.
> * Como os formatos e parâmetros de URL estão sujeitos a alterações sem aviso prévio, utilize todos os URLs como está. Não deve assumir dependências do formato url ou dos parâmetros, exceto quando se nota.

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

Quando chamar a API de Pesquisa de Imagens do Bing, o Bing devolve uma lista de resultados. A lista é um subconjunto do número total de resultados que são relevantes para a consulta. O campo `totalEstimatedMatches` da resposta contém uma estimativa do número de imagens que estão disponíveis para visualização. Para mais detalhes sobre como página rindo através do resto das imagens, consulte [Imagens Paging](../paging-images.md).

## <a name="next-steps"></a>Passos seguintes

Se ainda não experimentou a API de Pesquisa de Imagem bing antes, tente um [arranque rápido](../quickstarts/csharp.md). Se procura algo mais complexo, experimente o tutorial para criar uma [aplicação web de uma página única.](../tutorial-bing-image-search-single-page-app.md)
