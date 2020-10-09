---
title: Termos de pesquisa automáticos - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Emparelhar a API de Pesquisa Web Bing com a API Bing Autosuggest para proporcionar aos utilizadores uma experiência de pesquisa melhorada.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "66384866"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Termos de pesquisa autosusuest Bing na sua aplicação

Se disponibilizar uma caixa de pesquisa na qual o utilizador introduz o seu termo de pesquisa, utilize a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API devolve cadeias de consulta sugerida com base em termos de pesquisa parcial à medida que o utilizador escreve.

Depois de o utilizador introduzir um termo de pesquisa, deve ser codificado URL antes do parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) ser definido. Por exemplo, se o utilizador introduzir *sailing dinghies*, defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

Se o termo de consulta contiver um erro ortográfico, a resposta de pesquisa inclui um objeto [QueryContext.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) O objeto mostra a ortografia original e a ortografia corrigida que o Bing utilizou para a pesquisa.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Pode utilizar estas informações para informar o utilizador de que modificou a sua cadeia de consultas quando apresenta os resultados da pesquisa.

![Exemplo UX de contexto de consulta](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Passos seguintes  

* Rever as [respostas da API de pesquisa web Bing.](search-responses.md)  

## <a name="see-also"></a>Consulte também  

* [Bing Web Search API referência](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
