---
title: Obter imagens populares com a API de pesquisa de imagens do Bing
titleSuffix: Azure Cognitive Services
description: Pesquisa de imagens populares de hoje da web com a API de pesquisa de imagens do Bing.
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
ms.openlocfilehash: 024e007a34f71256d5cd148720412a0c57bb74ed
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342229"
---
# <a name="get-trending-images-from-the-web"></a>Obter imagens populares da web

Para obter imagens populares da atualidade, envie o pedido GET seguinte:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A API de imagens populares atualmente suporta apenas os mercados seguintes:  

- EN-US (inglês, Estados Unidos)  
- en-CA (inglês, Canadá)  
- EN-AU (inglês, Austrália)  
- zh-CN (Chinese, China)

A resposta contém um [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#trendingimages) objeto que apresenta uma lista de imagens por categoria. Utilize a categoria `title` para agrupar as imagens na sua experiência de utilizador. As categorias podem ser alterados diariamente.  

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

Cada mosaico contém uma imagem e opções para obter imagens relacionadas. Para obter as imagens relacionadas, pode utilizar a consulta `text` para chamar o [API de pesquisa de imagens](./search-the-web.md) e apresentar as imagens relacionadas por conta própria. Em alternativa, pode utilizar o URL na `webSearchUrl` para levar o usuário a página resultados do Bing imagens pesquisa, que contém as imagens relacionadas.

Se chamar a API de pesquisa de imagens para obter as imagens relacionadas, definir o [id](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#id) parâmetro para o ID de consulta o `id` campo. Especificação do ID garante que a resposta contém a imagem (é a primeira imagem na resposta) e suas imagens relacionadas. Além disso, defina o [p](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) parâmetro no texto da consulta a `query` do objeto `text` campo.

O exemplo seguinte mostra como utilizar o ID da imagem para obter imagens relacionadas do SR. Smith na resposta da API de imagens populares anterior.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
