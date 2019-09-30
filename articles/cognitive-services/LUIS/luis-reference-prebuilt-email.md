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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4a1bc9ae7ccf48b9dc8b47b57ea43b9259786d01
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677670"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Entidade de email predefinida para um aplicativo LUIS
Extração de e-mail inclui o endereço de e-mail completo de uma expressão. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém o e-mail para os objetivos do aplicativo. Entidade de e-mail é suportada no `en-us` apenas a cultura. 

## <a name="resolution-for-prebuilt-email"></a>Resolução de correio eletrónico pré-criados

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Resposta de ponto de extremidade de previsão v2](#tab/V2)

O exemplo seguinte mostra a resolução do **builtin.email** entidade.

```json
{
  "query": "please send the information to patti@contoso.com",
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
      "entity": "patti@contoso.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55,
      "resolution": {
        "value": "patti@contoso.com"
      }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Resposta de ponto de extremidade de previsão v3](#tab/V3)

O JSON a seguir é com o parâmetro `verbose` definido como `false`:

```json
{
    "query": "please send the information to patti@contoso.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti@contoso.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti@contoso.com"
            ]
        }
    }
}
```


O JSON a seguir é com o parâmetro `verbose` definido como `true`:

```json
{
    "query": "please send the information to patti@contoso.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti@contoso.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti@contoso.com"
            ],
            "$instance": {
                "email": [
                    {
                        "type": "builtin.email",
                        "text": "patti@contoso.com",
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

* * * 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).

Saiba mais sobre o [número](luis-reference-prebuilt-number.md), [ordinal](luis-reference-prebuilt-ordinal.md), e [percentagem](luis-reference-prebuilt-percentage.md). 
