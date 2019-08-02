---
title: Ponto de extremidade de visualização da URL do projeto
titlesuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade de visualização de URL.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 3ef5ebd4ec88deac8c49430f36956d3711c8c535
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706944"
---
# <a name="project-url-preview-endpoint"></a>Ponto de extremidade de visualização da URL do projeto

A API de visualização de URL inclui um ponto de extremidade.

## <a name="endpoint"></a>Ponto Final
Para obter uma versão prévia da URL, envie uma solicitação para o ponto de extremidade a seguir. Use os cabeçalhos e os parâmetros de URL para outras especificações.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Parâmetros de consulta
|Nome|Valor|Type|Requerido|  
|----------|-----------|----------|--------------|  
|p|URL para visualização|Cadeia |Sim|
|safeSearch|Conteúdo adulto ilegal, ou conteúdo pirateado, está bloqueado com o código de erro 400 e o sinalizador *isFamilyFriendly* não é retornado. <p>Para conteúdo de adulto legal, abaixo está o comportamento. O código de status retorna 200 e o sinalizador *isFamilyFriendly* é definido como false.<ul><li>safeSearch=strict: O título, a descrição, a URL e a imagem não serão retornados.</li><li>Adulto = moderada; Obtenha o título, a URL e a descrição, mas não a imagem descritiva.</li><li>Adulto = desativado; Obtenha todos os objetos/elementos de resposta – título, URL, descrição e imagem.</li></ul> |Cadeia|Não é necessário. </br> O padrão é o assegurado = estrito.| 

## <a name="response-object"></a>Objeto de resposta

A resposta inclui cabeçalhos HTTP e objeto webpage com atributos, conforme mostrado no exemplo a seguir `name`: `url` `description`, `isFamilyFriendly`,, e `primaryImageOfPage`.

```
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

## <a name="next-steps"></a>Passos Seguintes
- [Início rápido do C#](csharp.md)
- [Início rápido do Java](java-quickstart.md)
- [Início Rápido do JavaScript](javascript.md)
- [Início Rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
