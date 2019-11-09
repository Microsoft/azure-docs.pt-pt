---
title: Entidades predefinidas do DatetimeV2 – LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo tem informações de entidade datetimeV2 predefinidas em Reconhecimento vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 75d1f2b6facd438e329555d8595fe159565dbb74
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837357"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>Entidade predefinida DatetimeV2 para um aplicativo LUIS

A entidade predefinida **datetimeV2** extrai valores de data e hora. Esses valores são resolvidos em um formato padronizado para consumo dos programas cliente. Quando um expressão tem uma data ou hora que não está concluída, o LUIS inclui _valores passados e futuros_ na resposta do ponto de extremidade. Como essa entidade já está treinada, você não precisa adicionar o exemplo declarações que contém datetimeV2 às tentativas do aplicativo. 

## <a name="types-of-datetimev2"></a>Tipos de datetimeV2
DatetimeV2 é gerenciado no repositório GitHub de [texto de reconhecedores](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) .

## <a name="example-json"></a>Exemplo de JSON 

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
|Entidade|**String** -texto extraído do expressão com o tipo de data, hora, intervalo de datas ou intervalo de tempo.|
|tipo|**String** -um dos [subtipos de datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int** -o índice no expressão em que a entidade começa.|
|endIndex|**int** -o índice no expressão em que a entidade termina.|
|resolução|Tem uma matriz `values` que tem um, dois ou quatro [valores de resolução](#values-of-resolution).|
|completo|O valor final de um intervalo de tempo ou de data, no mesmo formato que `value`. Usado somente se `type` for `daterange`, `timerange`ou `datetimerange`|

* * * 

## <a name="subtypes-of-datetimev2"></a>Subtipos de datetimeV2

A entidade predefinida **datetimeV2** tem os seguintes subtipos e exemplos de cada um são fornecidos na tabela a seguir:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Valores de resolução
* A matriz terá um elemento se a data ou hora em expressão for totalmente especificada e não ambígua.
* A matriz terá dois elementos se o valor de datetimeV2 for ambíguo. A ambiguidade inclui a falta de ano, hora ou intervalo de tempo específico. Consulte [datas ambíguas](#ambiguous-dates) para obter exemplos. Quando o tempo é ambíguo para A.M. ou P.M., ambos os valores são incluídos.
* A matriz terá quatro elementos se o expressão tiver dois elementos com ambigüidade. Essa ambiguidade inclui elementos que têm:
  * Um intervalo de data ou data ambíguo como ano
  * Um intervalo de tempo ou hora ambíguo como A.M. ou P.M. Por exemplo, 3:00 a 3 de abril.

Cada elemento da matriz de `values` pode ter os seguintes campos: 

|Nome da propriedade|Descrição da propriedade|
|--|--|
|Timex|hora, data ou intervalo de datas expresso no formato TIMEX que segue os atributos [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e TIMEX3 para anotação usando o idioma TimeML. Essa anotação é descrita nas [diretrizes do Timex](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|tipo|O subtipo, que pode ser um dos seguintes itens: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|valor|**Adicional.** Um objeto DateTime no formato AAAA-MM-DD (Date), HH: mm: SS (time) aaaa-MM-DD HH: mm: SS (DateTime). Se `type` for `duration`, o valor será o número de segundos (duração) <br/> Usado somente se `type` for `datetime` ou `date`, `time`ou ' duração '.|

## <a name="valid-date-values"></a>Valores de data válidos

O **datetimeV2** dá suporte a datas entre os seguintes intervalos:

| Mín. | Máx. |
|----------|-------------|
| 1º de janeiro de 1900   | 31 de dezembro de 2099 |

## <a name="ambiguous-dates"></a>Datas ambíguas

Se a data puder estar no passado ou no futuro, o LUIS fornecerá ambos os valores. Um exemplo é um expressão que inclui o mês e a data sem o ano.  

Por exemplo, considerando o seguinte expressão:

`May 2nd`

* Se a data de hoje for de 3 a 2017, o LUIS fornecerá "2017-05-02" e "2018-05-02" como valores. 
* Quando a data de hoje é de 1º 2017, LUIS fornece "2016-05-02" e "2017-05-02" como valores.

O exemplo a seguir mostra a resolução da entidade "maio de 2a". Essa resolução pressupõe que a data de hoje é uma data entre 2 de maio de 2017 e 1º de maio de 2018.
Os campos com `X` no campo `timex` são partes da data que não são explicitamente especificadas no expressão.

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

## <a name="date-range-resolution-examples-for-numeric-date"></a>Exemplos de resolução de intervalo de datas para data numérica

A entidade `datetimeV2` extrai intervalos de data e hora. Os campos `start` e `end` especificam o início e o fim do intervalo. Para o `May 2nd to May 5th`expressão, LUIS fornece valores **DateRange** para o ano atual e o próximo ano. No campo `timex`, os valores de `XXXX` indicam a ambiguidade do ano. `P3D` indica que o período de tempo é de três dias.

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

O exemplo a seguir mostra como o LUIS usa **datetimeV2** para resolver o expressão `Tuesday to Thursday`. Neste exemplo, a data atual é 19 de junho. LUIS inclui valores de **DateRange** para ambos os intervalos de datas que precedem e seguem a data atual.

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

## <a name="ambiguous-time"></a>Hora ambígua
A matriz de valores tem dois elementos de tempo se a hora ou o intervalo de tempo é ambíguo. Quando há um tempo ambíguo, os valores têm as duas horas e P.M. maior.

## <a name="time-range-resolution-example"></a>Exemplo de resolução de intervalo de tempo

A resposta JSON DatetimeV2 foi alterada na API v3. O exemplo a seguir mostra como LUIS usa **datetimeV2** para resolver o expressão que tem um intervalo de tempo.

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

## <a name="deprecated-prebuilt-datetime"></a>DateTime predefinido preterido

A entidade predefinida `datetime` é preterida e substituída por **datetimeV2**. 

Para substituir `datetime` por `datetimeV2` em seu aplicativo LUIS, conclua as seguintes etapas:

1. Abra o painel **entidades** da interface da Web Luis. 
2. Exclua a entidade de **DateTime** predefinida.
3. Clique em **Adicionar entidade predefinida**
4. Selecione **datetimeV2** e clique em **salvar**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).

Saiba mais sobre a [dimensão](luis-reference-prebuilt-dimension.md), as entidades de [email](luis-reference-prebuilt-email.md) e o [número](luis-reference-prebuilt-number.md). 

