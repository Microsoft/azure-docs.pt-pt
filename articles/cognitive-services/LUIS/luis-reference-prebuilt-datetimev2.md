---
title: DatatimeV2 Entidades pré-construídas - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo tem datav2 informação de entidade pré-incorporada em Compreensão de Línguas (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2020
ms.openlocfilehash: 83522de9c00056a3808b002b3103f45c72553399
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013076"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída DatetimeV2 para uma aplicação LUIS

A entidade pré-construída **dataV2** extrai valores de data e hora. Estes valores resolvem-se num formato padronizado para os programas do cliente consumirem. Quando uma expressão tem uma data ou hora que não está completa, LUIS inclui _valores passados e futuros_ na resposta do ponto final. Como esta entidade já está treinada, não precisa de adicionar palavras de exemplo que contenham datav2 às intenções da aplicação.

## <a name="types-of-datetimev2"></a>Tipos de datav2
O datetimeV2 é gerido a partir do repositório GitHub [de texto recognisers.](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml)

## <a name="example-json"></a>Exemplo JSON

A seguinte expressão e a sua resposta parcial do JSON são mostradas abaixo.

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[Resposta V3](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-response"></a>[Resposta V2](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|Nome da propriedade |Tipo de propriedade e descrição|
|---|---|
|Entidade|**corda** - Texto extraído da expressão com tipo de data, hora, intervalo de data ou intervalo de tempo.|
|tipo|**corda** - Um dos [subtipos do tempo de dataV2](#subtypes-of-datetimev2)
|startIndex|**int** - O índice na expressão em que a entidade começa.|
|endIndex|**int** - O índice na expressão em que a entidade termina.|
|resolução|Tem uma `values` matriz que tem um, dois ou quatro [valores de resolução.](#values-of-resolution)|
|fim|O valor final de uma faixa de tempo, ou data, no mesmo formato que `value` . . Apenas usado se `type` for `daterange` , `timerange` ou `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Subtipos de dataV2

A entidade pré-construída **dataV2** tem os seguintes subtipos, e exemplos de cada um são fornecidos na tabela que se segue:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Valores de resolução
* A matriz tem um elemento se a data ou hora na expressão for totalmente especificada e inequívoca.
* A matriz tem dois elementos se o valor da dataV2 for ambíguo. A ambiguidade inclui a falta de ano, tempo ou intervalo de tempo específicos. Veja [datas ambíguas,](#ambiguous-dates) por exemplo. Quando o tempo é ambíguo para A.M. ou P.M., ambos os valores estão incluídos.
* A matriz tem quatro elementos se a expressão tiver dois elementos com ambiguidade. Esta ambiguidade inclui elementos que têm:
  * Um intervalo de data ou data que é ambíguo a partir do ano
  * Um intervalo de tempo ou tempo ambíguo quanto a A.M. . Por exemplo, 3 de abril 3 de abril.

Cada elemento da `values` matriz pode ter os seguintes campos:

|Nome da propriedade|Descrição da propriedade|
|--|--|
|timex|intervalo de tempo, data ou data expresso no formato TIMEX que segue a [norma ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e os atributos TIMEX3 para anotação utilizando o idioma TimeML.|
|mod|termo usado para descrever como usar o valor `before` como, `after` . .|
|tipo|O subtipo, que pode ser um dos seguintes itens: `datetime` , , , , , , , , , `date` , `time` `daterange` `timerange` `datetimerange` `duration` `set` .|
|valor|**É opcional.** Um objeto de data no Formato yyyy-MM-dd (data), HH:mm:ss (tempo) yyyy-MM-dd HH:mm:mm (data). Se `type` for , o valor é o número de `duration` segundos (duração) <br/> Só utilizado se `type` for `datetime` `date` `time` ou, ou duração.|

## <a name="valid-date-values"></a>Valores de data válidos

A **dataV2** suporta datas entre as seguintes gamas:

| Mín. | Máx |
|----------|-------------|
| 1 de janeiro de 1900   | 31 de dezembro de 2099 |

## <a name="ambiguous-dates"></a>Datas ambíguas

Se a data pode ser no passado ou no futuro, a LUIS fornece ambos os valores. Um exemplo é uma expressão que inclui o mês e a data sem o ano.

Por exemplo, dada a seguinte expressão:

`May 2nd`

* Se a data de hoje é 3 de maio de 2017, a LUIS fornece tanto "2017-05-02" como "2018-05-02" como valores.
* Quando a data de hoje é 1 de maio de 2017, a LUIS fornece os valores "2016-05-02" e "2017-05-02".

O exemplo a seguir mostra a resolução da entidade "2 de maio". Esta resolução pressupõe que a data de hoje é uma data entre 2 de maio de 2017 e 1 de maio de 2018.
Os campos com `X` no campo são partes da data que não são `timex` explicitamente especificadas na expressão.

## <a name="date-resolution-example"></a>Exemplo de resolução de data


A seguinte expressão e a sua resposta parcial do JSON são mostradas abaixo.

`May 2nd`

#### <a name="v3-response"></a>[Resposta V3](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
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

#### <a name="v2-response"></a>[Resposta V2](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>Exemplos de resolução da gama de datas para data numérica

A `datetimeV2` entidade extrai data e intervalos de tempo. Os `start` campos e os campos `end` especificam o início e o fim do intervalo. Para a `May 2nd to May 5th` expressão, a LUIS fornece valores **de data-limite** tanto para o ano em curso como para o próximo. No `timex` campo, os `XXXX` valores indicam a ambiguidade do ano. `P3D` indica que o período de tempo é de três dias.

A seguinte expressão e a sua resposta parcial do JSON são mostradas abaixo.

`May 2nd to May 5th`

#### <a name="v3-response"></a>[Resposta V3](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
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

#### <a name="v2-response"></a>[Resposta V2](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>Exemplos de resolução de intervalos de data para o dia da semana

O exemplo a seguir mostra como o LUIS utiliza **o horário de 2018** para resolver a expressão `Tuesday to Thursday` . Neste exemplo, a data atual é 19 de junho. O LUIS inclui valores **de gama de datas** para ambos os intervalos de data que precedem e seguem a data atual.

A seguinte expressão e a sua resposta parcial do JSON são mostradas abaixo.

`Tuesday to Thursday`

#### <a name="v3-response"></a>[Resposta V3](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-response"></a>[Resposta V2](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>Tempo ambíguo
A matriz de valores tem dois elementos de tempo se o tempo ou intervalo de tempo for ambíguo. Quando há um tempo ambíguo, os valores têm tanto o .M. e P.M. vezes.

## <a name="time-range-resolution-example"></a>Exemplo de resolução do intervalo de tempo

A resposta JSON do tempo de dataV2 mudou no V3 da API. O exemplo a seguir mostra como o LUIS utiliza **o horário de 2018** para resolver a expressão que tem um intervalo de tempo.

Alterações da API V2:
* `datetimeV2.timex.type` a propriedade já não é devolvida porque é devolvida ao nível dos pais, `datetimev2.type` .
* A `datetimeV2.value` propriedade foi renomeada `datetimeV2.timex` para.

A seguinte expressão e a sua resposta parcial do JSON são mostradas abaixo.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[Resposta V3](#tab/5-1)

O seguinte JSON é com o `verbose` parâmetro definido `false` para:

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/5-2)

O seguinte JSON é com o `verbose` parâmetro definido `true` para:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
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
#### <a name="v2-response"></a>[Resposta V2](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>Exemplo de resolução de tempo

A seguinte expressão e a sua resposta parcial do JSON são mostradas abaixo.

`8am`

#### <a name="v3-response"></a>[Resposta V3](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
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
#### <a name="v2-response"></a>[Resposta V2](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>Tempo de data pré-construído precotado

A `datetime` entidade pré-construída é depreciada e substituída por **datatimeV2**.

Para substituir `datetime` `datetimeV2` na sua aplicação LUIS, complete os seguintes passos:

1. Abra o painel de **Entidades** da interface web LUIS.
2. Eliminar a entidade pré-construída da **data.**
3. Clique **em Adicionar entidade pré-construída**
4. Selecione **a hora de dataV2** e clique **em Guardar**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

Conheça a [dimensão,](luis-reference-prebuilt-dimension.md)as entidades [de email](luis-reference-prebuilt-email.md) e o [número.](luis-reference-prebuilt-number.md)

