---
title: Análise de sentimento - LUIS
titleSuffix: Azure Cognitive Services
description: Se a análise do sentimento estiver configurada, a resposta do JSON inclui a análise de sentimentos.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: dee07859da97f76d9a2db3ccbb71ae74d8ff4846
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270412"
---
# <a name="sentiment-analysis"></a>Análise de sentimentos
Se a análise do sentimento estiver configurada, a resposta do JSON inclui a análise de sentimentos. Saiba mais sobre a análise de sentimentos na documentação do [Text Analytics.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)


## <a name="resolution-for-sentiment"></a>Resolução de sentimento

Os dados de sentimento são uma pontuação entre 1 e 0 indicando o sentimento positivo (mais próximo de 1) ou negativo (mais próximo de 0) dos dados.

#### <a name="english-language"></a>[Língua inglesa](#tab/english)

Quando a `en-us`cultura é, a resposta é:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Outras línguas](#tab/other-languages)

Para todas as outras culturas, a resposta é:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

