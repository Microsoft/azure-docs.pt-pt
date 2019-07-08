---
title: Ponto final de pré-visualização do URL do projeto
titlesuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade de pré-visualização do URL.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 43254db734a48f3e7aaa5a26a7fbf3981c9e9d87
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592871"
---
# <a name="project-url-preview-endpoint"></a>Ponto final de pré-visualização do URL do projeto

A API de pré-visualização do URL contém um ponto final.

## <a name="endpoint"></a>Ponto Final
Para obter uma pré-visualização de URL, envie um pedido para o seguinte ponto de extremidade. Utilize os cabeçalhos e os parâmetros de URL para outras especificações.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Parâmetros de consulta
|Name|Value|Type|Necessário|  
|----------|-----------|----------|--------------|  
|p|URL para a pré-visualização|String |Sim|
|safeSearch|Conteúdo para adultos ilegal ou pirateado conteúdo, é bloqueado com o código de erro 400 e o *isFamilyFriendly* sinalizador não for devolvido. <p>Para o conteúdo para adultos legal, segue-se o comportamento. Código de estado devolve 200 e o *isFamilyFriendly* sinalizador estiver definido como false.<ul><li>safeSearch=strict: Título, descrição, URL e imagem não serão devolvidos.</li><li>pesquisa segura = moderado; Obtenha title, URL e descrição mas não a imagem descritiva.</li><li>pesquisa segura = desativar; Obter todos os resposta objetos/elementos – title, URL, descrição e imagem.</li></ul> |String|Não é necessário. </br> O padrão é safeSearch = rigorosa.| 

## <a name="response-object"></a>Objeto de resposta

A resposta inclui cabeçalhos HTTP e o objeto de página Web com atributos, conforme mostrado no exemplo seguinte: `name`, `url`, `description`, `isFamilyFriendly`, e `primaryImageOfPage`.

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
