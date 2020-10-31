---
title: Sugerindo termos de pesquisa com a API Bing Autosuggest
titleSuffix: Azure Cognitive Services
description: Este artigo discute o conceito de sugerir termos de consulta utilizando a API Bing Autosuggest e o impacto do comprimento da consulta na relevância.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: cb507df53778e1b432370daa050041625a45e06e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101974"
---
# <a name="suggesting-query-terms"></a>Sugerir termos de consulta

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Normalmente, você chamaria a API Bing Autosuggest cada vez que um utilizador escreve um novo personagem na caixa de pesquisa da sua aplicação. A exatidão da cadeia de consulta afeta a relevância dos termos de consulta sugeridos devolvidos pela API. Quanto mais completa for a cadeia de consulta, mais relevante será a lista de termos de consulta sugeridos. Por exemplo, as sugestões para as que a API pode voltar `s` são provavelmente menos relevantes do que as consultas para as suas devoluções `sailing dinghies` .

## <a name="example-request"></a>Pedido de exemplo

O seguinte exemplo mostra um pedido que devolve as cadeias de consulta sugeridas para *sail* . Não se esqueça de codificar com URL o termo de consulta parcial do utilizador quando definir o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query). Por exemplo, se o utilizador introduzir *sailing les* , defina `q` como `sailing+les` ou `sailing%20les`.

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

## <a name="using-suggested-query-terms"></a>Usando termos de consulta sugeridos

Cada sugestão inclui um campo `displayText`, `query` e `url`. O campo `displayText` contém a consulta sugerida que utiliza para preencher a lista pendente da sua caixa de pesquisa. Tem de apresentar todas as sugestões incluídas na resposta e pela ordem fornecida.

O exemplo a seguir mostra uma caixa de pesquisa suspensa com termos de consulta sugeridos da API Bing Autosuggest.

![Lista da caixa de pesquisa pendente de sugestão automática](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Se o utilizador selecionar uma consulta sugerida na lista pendente, utilizará o termo de consulta no campo `query` para chamar a [API de Pesquisa na Web do Bing](../../bing-web-search/search-the-web.md) e apresentar os resultados. Em alternativa, pode utilizar o URL no campo `url` para direcionar o utilizador para a página de resultados da pesquisa do Bing.

## <a name="next-steps"></a>Passos seguintes

* [O que é a API de Sugestão Automática do Bing?](../get-suggested-search-terms.md)