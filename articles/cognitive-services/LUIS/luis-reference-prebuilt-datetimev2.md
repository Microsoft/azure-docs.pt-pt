---
title: Entidades predefinidas do DatetimeV2 – LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo tem datetimeV2 informações da entidade pré-criados na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: diberry
ms.openlocfilehash: 8c29ebd675bb6af66203c13824dacbe9ea2421a2
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732800"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>Entidade predefinida DatetimeV2 para um aplicativo LUIS

O **datetimeV2** entidade pré-criados extrai valores de data e hora. Resolva estes valores num formato padronizado para programas de cliente consumir. Quando uma expressão tem uma data ou hora que não esteja concluída, o LUIS inclui _passado e valores futuras_ na resposta do ponto final. Uma vez que já está preparada esta entidade, não é necessário adicionar expressões de exemplo que contém datetimeV2 para os objetivos do aplicativo.

## <a name="types-of-datetimev2"></a>Tipos de datetimeV2
DatetimeV2 é gerenciado no repositório GitHub de [texto de reconhecedores](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) .

## <a name="example-json"></a>JSON de exemplo

O expressão a seguir e sua resposta JSON parcial são mostrados abaixo.

`8am on may 2nd 2019`

#### <a name="v3-responsetab1-1"></a>[Resposta v3](#tab/1-1)

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

#### <a name="v3-verbose-responsetab1-2"></a>[V3, resposta detalhada](#tab/1-2)

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

#### <a name="v2-responsetab1-3"></a>[Resposta v2](#tab/1-3)

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
|Entidade|**cadeia de caracteres** -textos extraídos de expressão com o tipo de data, hora, intervalo de datas ou intervalo de tempo.|
|tipo|**cadeia de caracteres** - um do [subtipos dos datetimeV2](#subtypes-of-datetimev2)
|startIndex|**Int** -o índice na expressão em que começa a entidade.|
|endIndex|**Int** -o índice na expressão em que termina a entidade.|
|resolução|Tem um `values` matriz que tenha um, dois ou quatro [valores da resolução](#values-of-resolution).|
|end|O valor final de uma hora ou intervalo de datas, o mesmo formato que `value`. Utilizado apenas se `type` é `daterange`, `timerange`, ou `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Subtipos dos datetimeV2

O **datetimeV2** pré-criados de entidade tem os seguintes subtipos e são fornecidos exemplos de cada tabela que se segue:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Valores da resolução
* A matriz tem um elemento se a data ou hora em que a expressão é totalmente especificado e inequívoca.
* A matriz tem dois elementos, se o valor de datetimeV2 é ambíguo. Ambiguidade inclui a falta de um ano específico, hora ou intervalo de tempo. Ver [datas ambíguas](#ambiguous-dates) para obter exemplos. Quando o tempo é ambíguo para horas ou PM, ambos os valores estão incluídos.
* A matriz tem quatro elementos, se a expressão tiver dois elementos com a ambiguidade. Esta ambiguidade inclui elementos com:
  * Uma data ou um intervalo de datas é ambíguo como ano
  * Um tempo ou intervalo de tempo é ambíguo quanto às horas ou tarde Por exemplo, 3:00, 3 de Abril.

Cada elemento do `values` matriz pode ter os seguintes campos:

|Nome da propriedade|Descrição de propriedade|
|--|--|
|Timex|hora, data ou intervalo de datas expressado no formato TIMEX que se segue a [norma ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e os atributos de TIMEX3 usando a linguagem de TimeML de anotação. Esta anotação está descrita com o [diretrizes TIMEX](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|resto|termo usado para descrever como usar o valor, como `before`, `after`.|
|tipo|O subtipo, que pode ser um dos seguintes itens: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|valor|**Opcional.** Um objeto DateTime no formato AAAA-MM-DD (Date), HH: mm: SS (time) aaaa-MM-DD HH: mm: SS (DateTime). Se `type` é `duration`, o valor é o número de segundos (duração) <br/> Utilizado apenas se `type` é `datetime` ou `date`, `time`, ou "duração.|

## <a name="valid-date-values"></a>Valores de data válido

O **datetimeV2** suporta datas entre os seguintes intervalos:

| Mín. | Máx |
|----------|-------------|
| 1º de Janeiro de 1900   | 31 de Dezembro de 2099 |

## <a name="ambiguous-dates"></a>Datas ambíguas

Se a data pode ser no passado ou futuras, LUIS fornece os dois valores. Um exemplo é uma expressão que inclui o mês e data sem o ano.

Por exemplo, considerando o seguinte expressão:

`May 2nd`

* Se a data de hoje é 3 de Maio de 2017, o LUIS fornece "2017-05-02" e "2018-05-02" como valores.
* Quando a data de hoje é de 1 de Maio de 2017, o LUIS fornece "2016-05-02" e "2017-05-02" como valores.

O exemplo seguinte mostra a resolução da entidade "2nd de Maio". Esta resolução parte do princípio de que a data de hoje é uma data entre 2 de Maio de 2017 e 1 de Maio de 2018.
Campos com `X` no `timex` campo são partes da data que não estão especificados explicitamente na expressão.

## <a name="date-resolution-example"></a>Exemplo de resolução de data


O expressão a seguir e sua resposta JSON parcial são mostrados abaixo.

`May 2nd`

#### <a name="v3-responsetab2-1"></a>[Resposta v3](#tab/2-1)

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

#### <a name="v3-verbose-responsetab2-2"></a>[V3, resposta detalhada](#tab/2-2)

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

#### <a name="v2-responsetab2-3"></a>[Resposta v2](#tab/2-3)

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

O `datetimeV2` entidade extrai os intervalos de data e hora. O `start` e `end` fields, especifique o início e no fim do intervalo. Para o `May 2nd to May 5th`expressão, LUIS fornece valores **DateRange** para o ano atual e o próximo ano. Na `timex` campo, o `XXXX` valores indicam a ambiguidade do ano. `P3D` indica o período de tempo é longo de três dias.

O expressão a seguir e sua resposta JSON parcial são mostrados abaixo.

`May 2nd to May 5th`

#### <a name="v3-responsetab3-1"></a>[Resposta v3](#tab/3-1)

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


#### <a name="v3-verbose-responsetab3-2"></a>[V3, resposta detalhada](#tab/3-2)

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

#### <a name="v2-responsetab3-3"></a>[Resposta v2](#tab/3-3)

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

O exemplo a seguir mostra como o LUIS usa **datetimeV2** para resolver o expressão `Tuesday to Thursday`. Neste exemplo, a data atual for 19 de Junho. LUIS inclui **daterange** valores para os dois intervalos de datas que são precedidas e siga a data atual.

O expressão a seguir e sua resposta JSON parcial são mostrados abaixo.

`Tuesday to Thursday`

#### <a name="v3-responsetab4-1"></a>[Resposta v3](#tab/4-1)

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

#### <a name="v3-verbose-responsetab4-2"></a>[V3, resposta detalhada](#tab/4-2)

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

#### <a name="v2-responsetab4-3"></a>[Resposta v2](#tab/4-3)

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

A resposta JSON DatetimeV2 foi alterada na API v3. O exemplo seguinte mostra como utiliza o LUIS **datetimeV2** para resolver a expressão que tem um intervalo de tempo.

Alterações da API v2:
* `datetimeV2.timex.type` propriedade não é mais retornada porque é retornada no nível de pai, `datetimev2.type`.
* A propriedade `datetimeV2.value` foi renomeada para `datetimeV2.timex`.

O expressão a seguir e sua resposta JSON parcial são mostrados abaixo.

`from 6pm to 7pm`

#### <a name="v3-responsetab5-1"></a>[Resposta v3](#tab/5-1)

O JSON a seguir é com o parâmetro `verbose` definido como `false`:

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
#### <a name="v3-verbose-responsetab5-2"></a>[V3, resposta detalhada](#tab/5-2)

O JSON a seguir é com o parâmetro `verbose` definido como `true`:

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
#### <a name="v2-responsetab5-3"></a>[Resposta v2](#tab/5-3)

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

O expressão a seguir e sua resposta JSON parcial são mostrados abaixo.

`8am`

#### <a name="v3-responsetab6-1"></a>[Resposta v3](#tab/6-1)

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
#### <a name="v3-verbose-responsetab6-2"></a>[V3, resposta detalhada](#tab/6-2)

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
#### <a name="v2-responsetab6-3"></a>[Resposta v2](#tab/6-3)

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

O `datetime` criados previamente entidade é preterida e substituída por **datetimeV2**.

Para substituir `datetime` com `datetimeV2` na sua aplicação LUIS, conclua os seguintes passos:

1. Abra o **entidades** painel da interface da web do LUIS.
2. Eliminar a **datetime** entidade pré-criados.
3. Clique em **adicionar entidade pré-criados**
4. Selecione **datetimeV2** e clique em **guardar**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).

Saiba mais sobre o [dimensão](luis-reference-prebuilt-dimension.md), [e-mail](luis-reference-prebuilt-email.md) entidades, e [número](luis-reference-prebuilt-number.md).

