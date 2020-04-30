---
title: Tipo de entidade de lista - LUIS
description: As entidades da lista representam um conjunto fixo e fechado de palavras relacionadas juntamente com os seus sinónimos. A LUIS não descobre valores adicionais para entidades de lista. Utilize a função Recomendar para ver sugestões de novas palavras com base na lista atual.
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 273fabae38f6682cfaaffcdcc19e62adc41b7a47
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097579"
---
# <a name="list-entity"></a>Entidade de lista

As entidades da lista representam um conjunto fixo e fechado de palavras relacionadas juntamente com os seus sinónimos. A LUIS não descobre valores adicionais para entidades de lista. Utilize a função **Recomendar** para ver sugestões de novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade é devolvida na consulta final.

Uma entidade da lista não é aprendida com máquinas. É uma correspondência exata de texto. Luis marca qualquer correspondência com um item em qualquer lista como entidade na resposta.

**A entidade é um bom ajuste quando os dados de texto:**

* São um conjunto conhecido.
* Não muda muitas vezes. Se precisa de alterar a lista com frequência ou se quiser que a lista se auto-expanda, uma entidade simples reforçada com uma lista de frases é uma escolha melhor.
* O conjunto não excede os [limites](luis-limits.md) máximos do LUIS para este tipo de entidade.
* O texto na expressão é um jogo insensível com um sinónimo ou o nome canónico. Luis não usa a lista para além da partida. A correspondência fuzzy, stemming, plural, e outras variações não são resolvidas com uma entidade da lista. Para gerir variações, considere usar um [padrão](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional.

![entidade lista](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Exemplo .json para importar em entidade de lista

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

## <a name="example-json-response"></a>Exemplo resposta JSON

Suponha que a `Cities`app tem uma lista, nomeada , permitindo variações de nomes da cidade, incluindo cidade do aeroporto (Sea-tac), código do aeroporto (SEA), código postal postal postal (98101) e código de área telefónica (206).

|Listar item|Sinónimos de artigo|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Na expressão anterior, a `paris` palavra é mapeada para o `Cities` item de Paris como parte da entidade da lista. A entidade da lista corresponde tanto ao nome normalizado do artigo como aos sinónimos de artigo.

#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)


Este é o JSON se `verbose=false` estiver definido na corda de consulta:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Este é o JSON se `verbose=true` estiver definido na corda de consulta:

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

Neste [tutorial,](tutorial-list-entity.md)aprenda a usar uma **entidade da lista** para extrair correspondências exatas de texto de uma lista de itens conhecidos.
