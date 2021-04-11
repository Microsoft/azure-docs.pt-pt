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
ms.date: 04/06/2021
ms.openlocfilehash: 7524644b34a6fd479c08b9ce6418c547c836add5
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554030"
---
# <a name="sentiment-analysis"></a>Análise de sentimentos
Se a análise do sentimento estiver configurada, a resposta do JSON LUIS inclui a análise do sentimento. Saiba mais sobre a análise de sentimento na documentação [text Analytics.](../text-analytics/index.yml)

LUIS usa Text Analytics V2. 

A Análise de Sentimentos está configurada ao publicar a sua aplicação. Veja [como publicar uma aplicação](./luis-how-to-publish-app.md) para mais informações.

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
