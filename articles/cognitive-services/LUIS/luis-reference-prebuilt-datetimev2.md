---
title: DataTimeV2 Entidades pré-construídas - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo tem datetimeV2 informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/07/2020
ms.author: diberry
ms.openlocfilehash: 30132983f37323e798efd330f5cc8f15c0a9d2b6
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270741"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída DatetimeV2 para uma app LUIS

A entidade pré-construída **dataV2** extrai valores de data e hora. Resolva estes valores num formato padronizado para programas de cliente consumir. Quando uma expressão tem uma data ou hora que não esteja completa, a LUIS inclui _valores passados e futuros_ na resposta final. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém datetimeV2 para os objetivos do aplicativo.

## <a name="types-of-datetimev2"></a>Tipos de datetimeV2
DatetimeV2 é gerido a partir do repositório GitHub [de texto reconheço.](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml)

## <a name="example-json"></a>JSON de exemplo

A seguinte expressão e a sua resposta parcial da JSON são mostradas abaixo.

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
|Entidade|**cadeia** - Texto extraído da expressão com tipo de data, hora, intervalo de data ou intervalo de tempo.|
|tipo|**cadeia** - Um dos [subtipos de datatimeV2](#subtypes-of-datetimev2)
|startIndex|**int** - O índice na expressão em que a entidade começa.|
|endIndex|**int** - O índice na expressão em que a entidade termina.|
|resolução|Tem um conjunto de `values` que tem um, dois ou quatro [valores de resolução.](#values-of-resolution)|
|end|O valor final de uma hora, ou intervalo de data, no mesmo formato que `value`. Só utilizados se `type` for `daterange`, `timerange`ou `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Subtipos dos datetimeV2

A entidade pré-construída **datetimeV2** tem os seguintes subtipos, e exemplos de cada um são fornecidos na tabela que se segue:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Valores da resolução
* A matriz tem um elemento se a data ou hora em que a expressão é totalmente especificado e inequívoca.
* A matriz tem dois elementos, se o valor de datetimeV2 é ambíguo. Ambiguidade inclui a falta de um ano específico, hora ou intervalo de tempo. Consulte [datas ambíguas,](#ambiguous-dates) por exemplo. Quando o tempo é ambíguo para horas ou PM, ambos os valores estão incluídos.
* A matriz tem quatro elementos, se a expressão tiver dois elementos com a ambiguidade. Esta ambiguidade inclui elementos com:
  * Uma data ou um intervalo de datas é ambíguo como ano
  * Um tempo ou intervalo de tempo é ambíguo quanto às horas ou tarde Por exemplo, 3:00, 3 de Abril.

Cada elemento da matriz `values` pode ter os seguintes campos:

|Nome da propriedade|Descrição de propriedade|
|--|--|
|Timex|intervalo de tempo, data ou data expresso no formato TIMEX que segue a [norma ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e os atributos TIMEX3 para anotação utilizando a linguagem TimeML. Esta anotação é descrita nas [diretrizes TIMEX](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|mod|termo usado para descrever como usar o valor como `before`, `after`.|
|tipo|O subtipo, que pode ser um dos seguintes itens: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|valor|**Opcional.** Um objeto de data no Formato yyyy-MM-dd (data), HH:mm:ss (hora) yyyy-MM-dd HH:mm:ss (data). Se `type` for `duration`, o valor é o número de segundos (duração) <br/> Só utilizados se `type` for `datetime` ou `date`, `time`ou "duração"|

## <a name="valid-date-values"></a>Valores de data válido

A **datav2** suporta as datas entre as seguintes gamas:

| Mín. | Máx. |
|----------|-------------|
| 1º de Janeiro de 1900   | 31 de Dezembro de 2099 |

## <a name="ambiguous-dates"></a>Datas ambíguas

Se a data pode ser no passado ou futuras, LUIS fornece os dois valores. Um exemplo é uma expressão que inclui o mês e data sem o ano.

Por exemplo, dada a seguinte expressão:

`May 2nd`

* Se a data de hoje é 3 de Maio de 2017, o LUIS fornece "2017-05-02" e "2018-05-02" como valores.
* Quando a data de hoje é de 1 de Maio de 2017, o LUIS fornece "2016-05-02" e "2017-05-02" como valores.

O exemplo seguinte mostra a resolução da entidade "2nd de Maio". Esta resolução parte do princípio de que a data de hoje é uma data entre 2 de Maio de 2017 e 1 de Maio de 2018.
Campos com `X` no campo `timex` são partes da data que não estão explicitamente especificadas na expressão.

## <a name="date-resolution-example"></a>Exemplo de resolução de datas


A seguinte expressão e a sua resposta parcial da JSON são mostradas abaixo.

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

## <a name="date-range-resolution-examples-for-numeric-date"></a>Exemplos de resolução de intervalo de datas para numéricas de data

A entidade `datetimeV2` extrai intervalos de data e hora. Os campos `start` e `end` especificam o início e o fim do alcance. Para a expressão `May 2nd to May 5th`, a LUIS disponibiliza valores de **datação** tanto para o ano corrente como para o próximo. No campo `timex`, os valores `XXXX` indicam a ambiguidade do ano. `P3D` indica que o período de tempo é de três dias.

A seguinte expressão e a sua resposta parcial da JSON são mostradas abaixo.

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

## <a name="date-range-resolution-examples-for-day-of-week"></a>Exemplos de resolução de intervalo de datas para o dia da semana

O exemplo que se segue mostra como o LUIS utiliza **o datetimeV2** para resolver a expressão `Tuesday to Thursday`. Neste exemplo, a data atual for 19 de Junho. O LUIS inclui valores de **datação** para ambas as datas que precedem e seguem a data atual.

A seguinte expressão e a sua resposta parcial da JSON são mostradas abaixo.

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
A matriz de valores tem dois elementos de tempo se o tempo ou intervalo de tempo é ambíguo. Quando existe um período de tempo ambíguo, valores de ter as duas da manhã e. horas.

## <a name="time-range-resolution-example"></a>Exemplo de resolução de intervalo de tempo

A resposta DatetimeV2 JSON alterou-se no API V3. O exemplo que se segue mostra como o LUIS usa o **datetimeV2** para resolver a expressão que tem um intervalo de tempo.

Alterações da API V2:
* `datetimeV2.timex.type` propriedade já não é devolvida porque é devolvida ao nível dos pais, `datetimev2.type`.
* A propriedade `datetimeV2.value` foi renomeada para `datetimeV2.timex`.

A seguinte expressão e a sua resposta parcial da JSON são mostradas abaixo.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[Resposta V3](#tab/5-1)

O seguinte JSON está com o parâmetro `verbose` definido para `false`:

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

O seguinte JSON está com o parâmetro `verbose` definido para `true`:

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

A seguinte expressão e a sua resposta parcial da JSON são mostradas abaixo.

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

## <a name="deprecated-prebuilt-datetime"></a>Datetime pré-criados preterido

A entidade `datetime` pré-construída é depreciada e substituída por **datav2**.

Para substituir `datetime` por `datetimeV2` na sua aplicação LUIS, complete os seguintes passos:

1. Abra o painel **de Entidades** da interface web do LUIS.
2. Elimine a entidade pré-construída de **data.**
3. Clique em **Adicionar entidade pré-construída**
4. Selecione **datatimeV2** e clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Conheça a [dimensão,](luis-reference-prebuilt-dimension.md)as entidades de [e-mail](luis-reference-prebuilt-email.md) e [o número.](luis-reference-prebuilt-number.md)

