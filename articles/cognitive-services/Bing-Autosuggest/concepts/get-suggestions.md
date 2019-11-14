---
title: Sugerindo termos de pesquisa com o API de Sugestão Automática do Bing
titleSuffix: Azure Cognitive Services
description: Este artigo discute o conceito de sugestão de termos de consulta usando o API de Sugestão Automática do Bing e o impacto do comprimento da consulta em relevância.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 060dbd29ee4ddb78e8ae9b2ed4e7814da3c4eebf
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072895"
---
# <a name="suggesting-query-terms"></a>Sugerir termos de consulta

Normalmente, você chamaria o API de Sugestão Automática do Bing cada vez que um usuário digitasse um novo caractere na caixa de pesquisa do seu aplicativo. A exatidão da cadeia de consulta afeta a relevância dos termos de consulta sugeridos devolvidos pela API. Quanto mais completa for a cadeia de consulta, mais relevante será a lista de termos de consulta sugeridos. Por exemplo, as sugestões que a API pode retornar para `s` provavelmente serão menos relevantes do que as consultas que retorna para `sailing dinghies`.

## <a name="example-request"></a>Pedido de exemplo

O seguinte exemplo mostra um pedido que devolve as cadeias de consulta sugeridas para *sail*. Não se esqueça de codificar com URL o termo de consulta parcial do utilizador quando definir o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query). Por exemplo, se o utilizador introduzir *sailing les*, defina `q` como `sailing+les` ou `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

A seguinte resposta contém uma lista de objetos [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction) com os termos de consulta sugeridos.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Usando os termos de consulta sugeridos

Cada sugestão inclui um campo `displayText`, `query` e `url`. O campo `displayText` contém a consulta sugerida que utiliza para preencher a lista pendente da sua caixa de pesquisa. Tem de apresentar todas as sugestões incluídas na resposta e pela ordem fornecida.

O exemplo a seguir mostra uma caixa de pesquisa suspensa com os termos de consulta sugeridos do API de Sugestão Automática do Bing.

![Lista da caixa de pesquisa pendente de sugestão automática](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Se o utilizador selecionar uma consulta sugerida na lista pendente, utilizará o termo de consulta no campo `query` para chamar a [API de Pesquisa na Web do Bing](../../bing-web-search/search-the-web.md) e apresentar os resultados. Em alternativa, pode utilizar o URL no campo `url` para direcionar o utilizador para a página de resultados da pesquisa do Bing.

## <a name="next-steps"></a>Passos seguintes

* [O que é o API de Sugestão Automática do Bing?](../get-suggested-search-terms.md)