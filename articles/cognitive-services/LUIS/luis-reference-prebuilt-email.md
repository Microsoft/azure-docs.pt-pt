---
title: Referência de email de entidades predefinidas do LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém e-mail informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 542173a8756b868603c606307c5b682c68d7db25
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933556"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Entidade de email predefinida para um aplicativo LUIS
Extração de e-mail inclui o endereço de e-mail completo de uma expressão. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém o e-mail para os objetivos do aplicativo. Entidade de e-mail é suportada no `en-us` apenas a cultura. 

## <a name="resolution-for-prebuilt-email"></a>Resolução de correio eletrónico pré-criados

### <a name="api-version-2x"></a>Versão da API 2. x

O exemplo seguinte mostra a resolução do **builtin.email** entidade.

```json
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55,
      "resolution": {
        "value": "patti.owens@microsoft.com"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Versão prévia da API 3. x

O JSON a seguir é com `verbose` o parâmetro definido `false`como:

```json
{
    "query": "please send the information to patti.owens@microsoft.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti.owens@microsoft.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti.owens@microsoft.com"
            ]
        }
    }
}
```


O JSON a seguir é com `verbose` o parâmetro definido `true`como:

```json
{
    "query": "please send the information to patti.owens@microsoft.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti.owens@microsoft.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti.owens@microsoft.com"
            ],
            "$instance": {
                "email": [
                    {
                        "type": "builtin.email",
                        "text": "patti.owens@microsoft.com",
                        "startIndex": 31,
                        "length": 25,
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

Saiba mais sobre o [número](luis-reference-prebuilt-number.md), [ordinal](luis-reference-prebuilt-ordinal.md), e [percentagem](luis-reference-prebuilt-percentage.md). 
