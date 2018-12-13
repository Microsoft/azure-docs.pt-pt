---
title: Geografia V2 pré-criados de entidade
titleSuffix: Azure Cognitive Services
description: Este artigo contém geographyV2 informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 07bbb8e17a9f75bd878c384b5b8d90798d043814
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086232"
---
# <a name="geographyv2-entity"></a>Entidade GeographyV2
A entidade de geographyV2 pré-criados Deteta locais. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém GeographyV2 para os objetivos do aplicativo. Entidade de GeographyV2 é suportada em inglês [cultura](luis-reference-prebuilt-entities.md).

## <a name="subtypes"></a>Subtipos
As localizações geográficas tem subtipos:

|Subtipo|Objetivo|
|--|--|
|`poi`|ponto de interesse|
|`city`|nome da cidade|
|`countryRegion`|nome do país ou região|
|`continent`|nome do continente|
|`state`|nome do Estado ou província|


## <a name="resolution-for-geographyv2-entity"></a>Resolução de entidades de GeographyV2
O exemplo seguinte mostra a resolução do **builtin.geographyV2** entidade.

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [e-mail](luis-reference-prebuilt-email.md), [número](luis-reference-prebuilt-number.md), e [ordinal](luis-reference-prebuilt-ordinal.md) entidades. 