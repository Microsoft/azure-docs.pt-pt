---
title: Tipo de entidade aprendida por máquina-LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 2289e8ac7744a7b4cbee300e77efda89d29ee2f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74017221"
---
# <a name="machine-learned-entity"></a>Entidade aprendida por máquina 



**A entidade é uma boa opção quando os dados de texto:**


![entidade de lista](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>JSON de exemplo

Suponha que a aplicação tem uma lista, com o nome `Cities`, o que possibilita para variações de nomes de cidade, incluindo a cidade do aeroporto (mar-tac), código do aeroporto (SEA), código postal de zip (98101) e o código de área de telefone (206).

|Item da lista|Sinónimos de item|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Na expressão anterior, a palavra `paris` é mapeado para o item de paris como parte do `Cities` lista entidade. A entidade de lista corresponde ao nome do item de normalizado os e os item de sinónimos.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Resposta de ponto de extremidade de previsão v2](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Resposta de ponto de extremidade de previsão v3](#tab/V3)


Esse é o JSON se `verbose=false` for definido na cadeia de caracteres de consulta:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Esse é o JSON se `verbose=true` for definido na cadeia de caracteres de consulta:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|Objeto de dados|Nome da entidade|Valor|
|--|--|--|
|Listar entidade|`Cities`|`paris`|


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a entidade de [lista](reference-entity-list.md) e a entidade de [expressão regular](reference-entity-regular-expression.md) .