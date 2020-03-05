---
title: Análise de sentimento - LUIS
titleSuffix: Azure Cognitive Services
description: Se a análise de sentimentos está configurada, a resposta de json de LUIS inclui a análise de sentimentos.
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270412"
---
# <a name="sentiment-analysis"></a>Análise de sentimentos
Se a análise de sentimentos está configurada, a resposta de json de LUIS inclui a análise de sentimentos. Saiba mais sobre a análise de sentimentos na documentação do [Text Analytics.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)


## <a name="resolution-for-sentiment"></a>Resolução de sentimento

Dados de sentimento são uma pontuação entre 1 e 0 indicando o positivo (mais de perto como 1) nem negativa (mais próximo de 0) sentimentos dos dados.

#### <a name="english-language"></a>[Língua inglesa](#tab/english)

Quando a cultura é `en-us`, a resposta é:

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

