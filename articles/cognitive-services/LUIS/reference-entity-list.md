---
title: Tipo de entidade de lista - LUIS
description: As entidades da lista representam um conjunto fixo e fechado de palavras relacionadas juntamente com os seus sinónimos. A LUIS não descobre valores adicionais para as entidades de lista. Utilize a função Recomendação para ver sugestões de novas palavras com base na lista atual.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 410b33b5c6078d096fa4b2acaa7b49bc14c95e31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97608277"
---
# <a name="list-entity"></a>Entidade de lista

As entidades da lista representam um conjunto fixo e fechado de palavras relacionadas juntamente com os seus sinónimos. A LUIS não descobre valores adicionais para as entidades de lista. Utilize a função **Recomendação** para ver sugestões de novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade é devolvida na consulta de ponto final.

Uma entidade de lista não é aprendida com máquinas. É uma correspondência exata. Luis marca qualquer correspondência com um item em qualquer lista como entidade na resposta.

**A entidade é um bom ajuste quando os dados de texto:**

* São um conjunto conhecido.
* Não muda muitas vezes. Se precisa de alterar a lista com frequência ou quiser que a lista se auto-expanda, uma entidade simples reforçada com uma lista de frases é uma escolha melhor.
* O conjunto não excede os [limites](luis-limits.md) máximos do LUIS para este tipo de entidade.
* O texto na expressão é uma combinação caso-insensível com um sinónimo ou o nome canónico. Luis não usa a lista para além da partida. A correspondência fuzzy, stemming, plurals e outras variações não são resolvidas com uma entidade de lista. Para gerir variações, considere usar um [padrão](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional.

![entidade lista](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Exemplo .json para importar para entidade de lista

  Pode importar valores numa entidade de lista existente utilizando o seguinte formato .json:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>Exemplo JSON resposta

Suponha que a aplicação tenha uma lista, `Cities` nomeada, permitindo variações de nomes da cidade, incluindo cidade do aeroporto (Sea-tac), código do aeroporto (SEA), código postal postal (98101) e código de área telefónica (206).

|Artigo de lista|Sinónimos de artigo|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Na anterior expressão, a palavra `paris` é mapeada para o item de Paris como parte da entidade da `Cities` lista. A entidade da lista corresponde tanto ao nome normalizado do item como aos sinónimos de item.

#### <a name="v2-prediction-endpoint-response"></a>[Resposta do ponto final da previsão V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Resposta do ponto final de previsão V3](#tab/V3)

Este é o JSON se `verbose=false` for definido na cadeia de consulta:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Este é o JSON se `verbose=true` for definido na cadeia de consulta:

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
|Entidade de Lista|`Cities`|`paris`|

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as entidades:

* [Conceitos](luis-concept-entity-types.md)
* [Como criar](luis-how-to-add-entities.md)
