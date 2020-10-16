---
title: Análise de sentimento - LUIS
titleSuffix: Azure Cognitive Services
description: Se a análise do sentimento estiver configurada, a resposta do JSON LUIS inclui a análise do sentimento.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 7c3c0f603b9e7d83e40f43ab00d31f4ca63f2b4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535377"
---
# <a name="sentiment-analysis"></a>Análise de sentimentos
Se a análise do sentimento estiver configurada, a resposta do JSON LUIS inclui a análise do sentimento. Saiba mais sobre a análise de sentimento na documentação [text Analytics.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)

LUIS usa Text Analytics V2. 

## <a name="resolution-for-sentiment"></a>Resolução para o sentimento

Os dados do sentimento são uma pontuação entre 1 e 0 indicando o sentimento positivo (mais próximo de 1) ou negativo (mais próximo de 0) dos dados.

#### <a name="english-language"></a>[Língua inglesa](#tab/english)

Quando a cultura `en-us` é, a resposta é:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Outras linguagens](#tab/other-languages)

Para todas as outras culturas, a resposta é:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

