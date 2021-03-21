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
ms.openlocfilehash: 1a3f479fbbe68c8880cd7fefb3b57c77d4cfbbfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349576"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Termos de pesquisa autosusuest Bing na sua aplicação

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Se disponibilizar uma caixa de pesquisa na qual o utilizador introduz o seu termo de pesquisa, utilize a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API devolve cadeias de consulta sugerida com base em termos de pesquisa parcial à medida que o utilizador escreve.

Depois de o utilizador introduzir um termo de pesquisa, deve ser codificado URL antes do parâmetro de consulta [q](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) ser definido. Por exemplo, se o utilizador introduzir *sailing dinghies*, defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

Se o termo de consulta contiver um erro ortográfico, a resposta de pesquisa inclui um objeto [QueryContext.](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) O objeto mostra a ortografia original e a ortografia corrigida que o Bing utilizou para a pesquisa.

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

## <a name="see-also"></a>Ver também  

* [Bing Web Search API referência](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)