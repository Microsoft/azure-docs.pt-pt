---
title: Análise de sentimentos-LUIS
titleSuffix: Azure Cognitive Services
description: Se a análise de sentimentos estiver configurada, a resposta JSON LUIS incluirá a análise de sentimentos.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: a91246e4a4b3dc98bf11d2b014478fd40252f14b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508025"
---
# <a name="sentiment-analysis"></a>Análise de sentimentos
Se a análise de sentimentos estiver configurada, a resposta JSON LUIS incluirá a análise de sentimentos. Saiba mais sobre a análise de sentimentos na documentação do [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) .


## <a name="resolution-for-sentiment"></a>Resolução de sentimentos

Os dados de sentimentos são uma pontuação entre 1 e 0 que indica a diferenciação positiva (mais de 1) ou negativa (mais de 0) dos dados.

#### <a name="english-languagetabenglish"></a>[Idioma inglês](#tab/english)

Quando a cultura é `en-us`, a resposta é:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languagestabother-languages"></a>[Outras linguagens](#tab/other-languages)

Para todas as outras culturas, a resposta é:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * * 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).

