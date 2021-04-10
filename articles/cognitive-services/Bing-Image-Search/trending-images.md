---
title: Obtenha imagens de tendência com a API de Pesquisa de Imagem Bing
titleSuffix: Azure Cognitive Services
description: Procure as imagens de tendência de hoje da web com a API de Pesquisa de Imagem Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: cf7d1baf895d44730eb913b658ee4c7fe7eb7b11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96341619"
---
# <a name="get-trending-images-from-the-web"></a>Obtenha imagens de tendência da web

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Para obter as imagens de tendência de hoje, envie o seguinte pedido GET:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A API de Imagens de Tendência suporta apenas os seguintes mercados:  

- en-US (Inglês, Estados Unidos)  
- en-CA (Inglês, Canadá)  
- en-AU (Inglês, Austrália)  
- zh-CN (Chinês, China)

A resposta contém um objeto [TrendingImages](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) que lista imagens por categoria. Utilize as categorias `title` para agrupar as imagens na sua experiência de utilizador. As categorias podem mudar diariamente.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

Cada azulejo contém uma imagem e opções para obter imagens relacionadas. Para obter as imagens relacionadas, pode utilizar a consulta `text` para ligar para a [API de Pesquisa de Imagem](./overview.md) e exibir as imagens relacionadas. Ou, pode utilizar o URL `webSearchUrl` para levar o utilizador à página de resultados de pesquisa de imagens de Bing, que contém as imagens relacionadas.

Se ligar para a API de Pesquisa de Imagem para obter as imagens relacionadas, desafine o parâmetro de consulta de [identificação](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) para o ID no `id` campo. Especificar o ID garante que a resposta contém a imagem (é a primeira imagem na resposta) e as suas imagens relacionadas. Além disso, desajei o parâmetro de consulta [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) ao texto no `query` campo do `text` objeto.

O exemplo a seguir mostra como usar o ID de imagem para obter imagens relacionadas do Sr. Smith na resposta aPI de Imagens de Tendências anteriores.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```