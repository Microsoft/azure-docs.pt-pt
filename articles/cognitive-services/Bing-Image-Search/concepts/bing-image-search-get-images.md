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
ms.openlocfilehash: 988a1332d03bf2c9563ab0576f7a20ee6b0615aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96342061"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Obtenha imagens da web com a API de Pesquisa de Imagem Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Quando utilizar a API de Pesquisa de Imagem Bing, pode obter imagens da web que estejam relacionadas com o seu termo de pesquisa enviando o seguinte pedido GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Utilize o parâmetro de consulta [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) para o seu termo de pesquisa codificado por url. Por exemplo, se entrar em *botes de vela,* definido `q` para ou `sailing+dinghies` `sailing%20dinghies` .

> [!IMPORTANT]
> * Todos os pedidos devem ser feitos a partir de um servidor, e não de um cliente.
> * Se for a primeira vez que liga para as APIs de pesquisa de Bing, não inclua o cabeçalho de identificação do cliente. Apenas inclua o ID do cliente se já ligou para uma API Bing que devolveu uma identificação do cliente para a combinação do utilizador e do dispositivo.

## <a name="get-images-from-a-specific-web-domain"></a>Obtenha imagens de um domínio web específico

Para obter imagens de um domínio específico, utilize o operador de consulta [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> As respostas às consultas que utilizam o `site:` operador podem incluir conteúdo adulto, independentemente da definição [safeSearch.](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) Só utilizar `site:` se estiver ciente do conteúdo do domínio.

## <a name="filter-images"></a>Filtrar imagens

 Por predefinição, a API de Pesquisa de Imagem devolve todas as imagens relevantes para a consulta. Se pretender filtrar as imagens que bing devolve (por exemplo, para devolver apenas imagens com um fundo transparente ou tamanho específico), utilize os seguintes parâmetros de consulta:

* [aspeto](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)— Filtrar as imagens por relação de aspeto (por exemplo, imagens de ecrã padrão ou largas).
* [cor](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)— Filtrar imagens por cor dominante ou preto e branco.
* [frescura](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)— Filtrar imagens por idade (por exemplo, imagens descobertas por Bing na semana passada).
* [altura,](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height) [largura](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)— Filtrar as imagens por largura e altura.
* [imagemContent](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)— Filtrar imagens por conteúdo (por exemplo, imagens que mostram apenas o rosto de uma pessoa).
* [imagemType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)— Filtrar imagens por tipo (por exemplo, clip art, GIFs animados ou fundos transparentes).
* [licença](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)— Filtrar imagens pelo tipo de licença associada ao site.
* [tamanho](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)— Filtrar imagens por tamanho, como pequenas imagens até 200x200 pixels.

Para obter imagens de um domínio específico, utilize o operador de consulta [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

O exemplo a seguir mostra como obter pequenas imagens de ContosoSailing.com que Bing descobriu na semana passada.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Bing Formato de resposta à procura de imagem

A mensagem de resposta de Bing contém uma resposta [De Imagens](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) que contém uma lista de imagens que os Serviços Cognitivos determinaram ser relevantes para a consulta. Cada objeto [de imagem](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) da lista inclui as seguintes informações sobre a imagem: o URL, o seu tamanho, as suas dimensões, o seu formato de codificação, um URL para uma miniatura da imagem e as dimensões da miniatura.

> [!NOTE]
> * As imagens devem ser exibidas na ordem fornecida na resposta.
> * Como os formatos e parâmetros de URL estão sujeitos a alterações sem aviso prévio, utilize todos os URLs como está. Não deve assumir dependências do formato URL ou parâmetros, exceto quando for anotado.

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

Quando chamar a API de Pesquisa de Imagens do Bing, o Bing devolve uma lista de resultados. A lista é um subconjunto do número total de resultados que são relevantes para a consulta. O campo `totalEstimatedMatches` da resposta contém uma estimativa do número de imagens que estão disponíveis para visualização. Para mais detalhes sobre como página através das restantes imagens, consulte [Imagens paging](../../bing-web-search/paging-search-results.md).

## <a name="next-steps"></a>Passos seguintes

Se ainda não experimentou a API de Pesquisa de Imagem de Bing, experimente um [arranque rápido](../quickstarts/csharp.md). Se procura algo mais complexo, experimente o tutorial para criar uma [aplicação web de uma página.](../tutorial-bing-image-search-single-page-app.md)