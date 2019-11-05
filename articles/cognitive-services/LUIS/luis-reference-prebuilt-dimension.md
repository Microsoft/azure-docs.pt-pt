---
title: Entidades predefinidas da dimensão – LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidade predefinidas da dimensão no Reconhecimento vocal (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 6699b1617ccd1fef9a507e71fdd73a02b0e98bea
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465030"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Entidade predefinida de dimensão para um aplicativo LUIS
A entidade de dimensão predefinida detecta vários tipos de dimensões, independentemente da cultura do aplicativo LUIS. Como essa entidade já está treinada, você não precisa adicionar o exemplo declarações que contém dimensões às tentativas do aplicativo. A entidade de dimensão tem suporte em [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Tipos de dimensão

A dimensão é gerenciada do repositório GitHub de [texto de reconhecedores](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) .

## <a name="resolution-for-dimension-entity"></a>Resolução para entidade de dimensão

Os seguintes objetos de entidade são retornados para a consulta:

`10 1/2 miles of cable`

#### <a name="v3-responsetabv3"></a>[Resposta v3](#tab/V3)

O JSON a seguir é com o parâmetro `verbose` definido como `false`:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3, resposta detalhada](#tab/V3-verbose)
O JSON a seguir é com o parâmetro `verbose` definido como `true`:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-responsetabv2"></a>[Resposta v2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **Builtin. Dimension** .

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * * 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).

Saiba mais sobre o [email](luis-reference-prebuilt-email.md), [número](luis-reference-prebuilt-number.md)e entidades [ordinais](luis-reference-prebuilt-ordinal.md) . 
