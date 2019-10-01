---
title: Tipo de entidade de lista-LUIS
titleSuffix: Azure Cognitive Services
description: As entidades de lista representam um conjunto fixo e fechado de palavras relacionadas junto com seus sinônimos. LUIS não Deteta valores adicionais para entidades de lista. Use o recurso recomendado para ver sugestões para novas palavras com base na lista atual.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 1757faf8ab2be0b62956b6939ee068929f9275a4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695244"
---
# <a name="list-entity"></a>Entidade de lista 

As entidades de lista representam um conjunto fixo e fechado de palavras relacionadas junto com seus sinônimos. LUIS não Deteta valores adicionais para entidades de lista. Utilize o **Recomendamos** funcionalidade para ver sugestões para novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade é devolvida na consulta de ponto final. 

Uma entidade de lista não é aprendida por máquina. É uma correspondência exata de texto. LUIS marca qualquer correspondência com um item em qualquer lista como uma entidade na resposta. 

**A entidade é uma boa opção quando os dados de texto:**

* É um conjunto conhecido.
* Não é alterado com frequência. Se você precisar alterar a lista com frequência ou desejar que a lista se expanda automaticamente, uma entidade simples aumentada com uma lista de frases é uma opção melhor. 
* O conjunto não excede os [limites](luis-boundaries.md) máximos do LUIS para este tipo de entidade.
* O texto na expressão é uma correspondência exata com um sinónimo ou o nome canónico. LUIS não usa a lista além de correspondências de texto exatas. Correspondência difusa, não diferencia maiúsculas de minúsculas, lematização, plurals e outras variações não são resolvidas com uma entidade de lista. Para gerenciar variações, considere usar um [padrão](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional.

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

|Objeto de dados|Nome da entidade|Value|
|--|--|--|
|Listar entidade|`Cities`|`paris`|


## <a name="next-steps"></a>Passos seguintes

Neste [tutorial](luis-quickstart-intent-and-list-entity.md), saiba como usar uma entidade de **lista** para extrair correspondências exatas de texto de uma lista de itens conhecidos. 
