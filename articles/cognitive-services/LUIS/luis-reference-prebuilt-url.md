---
title: Entidades pré-concebidas do URL
titleSuffix: Azure
description: Este artigo contém o url de informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d59020eb45f7dcced5ea8da04b3b908def34fb70
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072231"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>URL de entidade criados previamente para uma aplicação LUIS
Entidade de URL extrai URLs com nomes de domínio ou endereços IP. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém os URLs para a aplicação. Entidade de URL é suportada no `en-us` apenas a cultura. 

## <a name="types-of-urls"></a>Tipos de URLs
URL é gerido a partir da [reconhecedores texto](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) repositório do GitHub

## <a name="resolution-for-prebuilt-url-entity"></a>Resolução de entidade pré-criados do URL

### <a name="api-version-2x"></a>Versão de API 2.x

O exemplo seguinte mostra a resolução do **builtin.url** entidade.

```json
{
  "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "https://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Versão de API de pré-visualização 3.x

O seguinte JSON é com o `verbose` parâmetro definido como `false`:

```json
{
    "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
    "prediction": {
        "normalizedQuery": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.421936184
            }
        },
        "entities": {
            "url": [
                "https://www.luis.ai"
            ]
        }
    }
}
```

O seguinte JSON é com o `verbose` parâmetro definido como `true`:

```json
{
    "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
    "prediction": {
        "normalizedQuery": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.421936184
            }
        },
        "entities": {
            "url": [
                "https://www.luis.ai"
            ],
            "$instance": {
                "url": [
                    {
                        "type": "builtin.url",
                        "text": "https://www.luis.ai",
                        "startIndex": 0,
                        "length": 19,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [ordinal](luis-reference-prebuilt-ordinal.md), [número](luis-reference-prebuilt-number.md), e [temperatura](luis-reference-prebuilt-temperature.md) entidades.
