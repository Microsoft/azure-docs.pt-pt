---
title: Extração de dados - LUIS
description: Extrair dados de texto de expressão com intenções e entidades. Saiba que tipo de dados podem ser extraídos da Compreensão da Linguagem (LUIS).
author: diberry
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 1c1a744c06e5347625fb96518bd809481ee797e5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361204"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrair dados de texto de expressão com intenções e entidades
LUIS dá-lhe a capacidade de obter informações de expressões de linguagem natural com um utilizador. As informações são extraídas de uma forma que pode ser utilizada por um programa, aplicação ou bot de bate-papo para tomar medidas. As secções seguintes, saiba quais dados são retornados de objetivos e entidades com exemplos de JSON.

Os dados mais difíceis de extrair são os dados aprendidos pela máquina porque não é uma correspondência exata de texto. A extração de dados das entidades aprendidas por [máquinas](luis-concept-entity-types.md) tem de fazer parte do ciclo de [autor](luis-concept-app-iteration.md) até estar confiante de que recebe os dados que espera.

## <a name="data-location-and-key-usage"></a>Utilização de localização e a chave de dados
A LUIS fornece os dados do [ponto final](luis-glossary.md#endpoint)publicado. O **pedido HTTPS** (POST ou GET) contém a expressão, bem como algumas configurações opcionais, tais como ambientes de encenação ou produção.

#### <a name="v2-prediction-endpoint-request"></a>[Pedido de ponto final de previsão V2](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-request"></a>[Pedido de ponto final de previsão V3](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

* * *

O `appID` está disponível na página de **Definições** da sua aplicação LUIS, bem como parte do URL (depois de `/apps/`) quando estiver a editar aquela aplicação LUIS. O `subscription-key` é a chave final utilizada para consultar a sua aplicação. Embora possa utilizar a sua chave de autor/arranque gratuita enquanto está a aprender LUIS, é importante alterar a chave de ponto final para uma chave que suporta o uso esperado do [LUIS.](luis-boundaries.md#key-limits) A unidade `timezoneOffset` tem minutos.

A **resposta HTTPS** contém todas as informações de intenção e entidade que a LUIS pode determinar com base no modelo publicado atual, quer na encenação quer no ponto final de produção. O URL de ponto final encontra-se no site da [LUIS,](luis-reference-regions.md) na secção **Gerir,** na página **Keys e endpoints.**

## <a name="data-from-intents"></a>Dados desde intenções
Os dados primários são o nome de **intenção**de pontuação superior. A resposta do ponto final é:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

* * *

|Objeto de dados|Tipo de Dados|Localização dos Dados|Valor|
|--|--|--|--|
|Intenção|String|topScoringIntent.intent|"GetStoreInfo"|

Se o seu chatbot ou app de chamada LUIS tomar uma decisão com base em mais de uma pontuação de intenção, devolva todas as pontuações das intenções.


#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

Defina o parâmetro de corda de consulta, `verbose=true`. A resposta do ponto final é:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

Defina o parâmetro de corda de consulta, `show-all-intents=true`. A resposta do ponto final é:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

* * *

Os objetivos são ordenados da mais alta pontuação mais baixa.

|Objeto de dados|Tipo de Dados|Localização dos Dados|Valor|Classificação|
|--|--|--|--|:--|
|Intenção|String|.intent intenções [0]|"GetStoreInfo"|0.984749258|
|Intenção|String|.intent intenções [1]|"None"|0.0168218873|

Se adicionar domínios pré-construídos, o nome de intenção indica o domínio, como `Utilties` ou `Communication`, bem como a intenção:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

* * *

|Domain|Objeto de dados|Tipo de Dados|Localização dos Dados|Valor|
|--|--|--|--|--|
|Serviços Públicos|Intenção|String|.intent intenções [0]|"<b>Serviços públicos</b>. ShowNext"|
|Comunicação|Intenção|String|.intent intenções [1]|<b>Comunicação.</b> Início"|
||Intenção|String|.intent intenções [2]|"None"|


## <a name="data-from-entities"></a>Dados de entidades
A maioria dos chatbots e as aplicações necessitam de mais do que o nome de intenção. Adicionais e opcionais vêm os dados detetadas na expressão de entidades. Cada tipo de entidade devolve diferentes informações sobre a correspondência.

Uma única palavra ou frase numa expressão pode corresponder a mais de uma entidade. Nesse caso, cada entidade correspondente é devolvida com sua pontuação.

Todas as entidades são devolvidas na variedade de **entidades** da resposta a partir do ponto final:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

* * *

## <a name="tokenized-entity-returned"></a>entidade com token devolvida

Reveja o [suporte simbólico](luis-language-support.md#tokenization) no LUIS.

## <a name="simple-entity-data"></a>Dados de entidades simples

Uma [entidade simples](reference-entity-simple.md) é um valor aprendido por máquinas. Pode ser uma palavra ou frase.

## <a name="composite-entity-data"></a>Dados de entidades compostos

Uma [entidade composta](reference-entity-composite.md) é constituída por outras entidades, como entidades pré-construídas, expressão simples, regular e entidades de lista. As entidades separadas formam uma entidade inteira.

## <a name="list-entity-data"></a>Dados de entidades de lista

[As entidades da lista](reference-entity-list.md) representam um conjunto fixo e fechado de palavras relacionadas juntamente com os seus sinónimos. LUIS não Deteta valores adicionais para entidades de lista. Utilize a função **Recomendar** para ver sugestões de novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade é devolvida na consulta de ponto final.

## <a name="prebuilt-entity-data"></a>Dados de entidade predefinidos
[Entidades pré-construídas](luis-concept-entity-types.md) são descobertas com base numa correspondência de expressão regular usando o projeto [Recognisers-Text](https://github.com/Microsoft/Recognizers-Text) de código aberto. As entidades pré-construídas são devolvidas na matriz de entidades e usam o nome tipo pré-fixado com `builtin::`. O texto seguinte é uma expressão de exemplo com as entidades retornadas de pré-criados:

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

Sem o parâmetro de corda de consulta, `verbose=true`:

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

Com o parâmetro de corda de consulta, `verbose=true`:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
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

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

* * *
## <a name="regular-expression-entity-data"></a>Dados de entidades de expressão regular

Uma [entidade de expressão regular](reference-entity-regular-expression.md) extrai uma entidade com base num padrão de expressão regular que fornece.

## <a name="extracting-names"></a>Extrair nomes
Obtendo nomes de uma expressão é difícil porque um nome pode ser praticamente qualquer combinação de letras e palavras. Dependendo do tipo de nome que está a extrair, tem várias opções. As seguintes sugestões não são regras, mas mais orientações.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Adicionar personname pré-construído e geografiaV2 entidades

As entidades [PersonName](luis-reference-prebuilt-person.md) e [GeographyV2](luis-reference-prebuilt-geographyV2.md) estão disponíveis em [algumas culturas linguísticas.](luis-reference-prebuilt-entities.md)

### <a name="names-of-people"></a>Nomes de pessoas

Nome das pessoas pode ter algum formato ligeiro dependendo do idioma e cultura. Utilize uma entidade pré-construída **[nome ou](luis-reference-prebuilt-person.md)** uma **[entidade simples](luis-concept-entity-types.md#simple-entity)** com [papéis](luis-concept-roles.md) de primeiro e último nome.

Se utilizar a entidade simples, certifique-se de dar exemplos que usam o primeiro e o último nome em diferentes partes da expressão, em expressões de diferentes comprimentos, e expressões em todas as intenções, incluindo a intenção De None. [Rever](luis-how-to-review-endoint-utt.md) as declarações finais regularmente para rotular quaisquer nomes que não tenham sido corretamente previstos.

### <a name="names-of-places"></a>Nomes de locais

Os nomes de localização são definidos e conhecidos como cidades, condados, estados, províncias e países/regiões. Utilize a entidade pré-construída **[geografiaV2](luis-reference-prebuilt-geographyv2.md)** para extrair informações de localização.

### <a name="new-and-emerging-names"></a>Nomes de novos e emergentes

Algumas aplicações tem de conseguir encontrar os nomes de novos e emergentes, como produtos ou as empresas. Estes tipos de nomes são o tipo mais difícil de extração de dados. Comece com uma **[entidade simples](luis-concept-entity-types.md#simple-entity)** e adicione uma lista de [frases.](luis-concept-feature.md) [Rever](luis-how-to-review-endoint-utt.md) as declarações finais regularmente para rotular quaisquer nomes que não tenham sido corretamente previstos.

## <a name="pattern-roles-data"></a>Dados de funções padrão
As funções são as diferenças contextuais das entidades.


#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

O nome da entidade é `Location`, com duas funções, `Origin` e `Destination`.

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Employee",
    "startIndex": 5,
    "endIndex": 13,
    "score": 0.922820568,
    "role": ""
  },
  {
    "entity": "seattle",
    "type": "Location",
    "startIndex": 20,
    "endIndex": 26,
    "score": 0.948008537,
    "role": "Origin"
  },
  {
    "entity": "redmond",
    "type": "Location",
    "startIndex": 31,
    "endIndex": 37,
    "score": 0.7047979,
    "role": "Destination"
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

Em V3, o **nome** principal é o nome principal do objeto.

O nome da entidade é `Location`, com duas funções, `Origin` e `Destination`.

Sem o parâmetro de corda de consulta, `verbose=true`:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

Com o parâmetro de corda de consulta, `verbose=true`:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

* * *

## <a name="patternany-entity-data"></a>Dados de entidades de Pattern.any

[Pattern.any](reference-entity-pattern-any.md) é um espaço reservado de comprimento variável usado apenas na expressão do modelo de um padrão para marcar onde a entidade começa e termina.

## <a name="sentiment-analysis"></a>Análise de sentimentos
Se a análise de sentimentos está configurada, a resposta de json de LUIS inclui a análise de sentimentos. Saiba mais sobre a análise de sentimentos na documentação do [Text Analytics.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)

### <a name="sentiment-data"></a>Dados de sentimento
Dados de sentimento são uma pontuação entre 1 e 0 indicando o positivo (mais de perto como 1) nem negativa (mais próximo de 0) sentimentos dos dados.

Quando a cultura é `en-us`, a resposta é:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Para todas as outras culturas, a resposta é:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Dados de entidades de extração de expressões-chave
A entidade de extração de frases-chave devolve frases-chave na expressão, fornecida por [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).


#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

Sem o parâmetro de corda de consulta, `verbose=true`:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

Com o parâmetro de corda de consulta, `verbose=true`:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
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

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

* * *


## <a name="data-matching-multiple-entities"></a>Correspondência de várias entidades de dados

LUIS devolve todas as entidades detetadas na expressão. Como resultado, o seu chatbot poderá ter de tomar a decisão com base nos resultados. Uma expressão pode ter muitas entidades numa expressão:

`book me 2 adult business tickets to paris tomorrow on air france`

O ponto final do LUIS pode descobrir os mesmos dados em diferentes entidades.

#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

Sem `verbose=true` como parâmetro de corda de consulta.

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

Com `verbose=true` como parâmetro de corda de consulta.


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

* * *

## <a name="data-matching-multiple-list-entities"></a>Dados que correspondem a várias entidades da lista

Se uma palavra ou frase corresponder a mais de uma entidade de lista, a consulta de ponto final devolve cada entidade de lista.

Para a consulta `when is the best time to go to red rock?`, e a app tem a palavra `red` em mais de uma lista, a LUIS reconhece todas as entidades e devolve um conjunto de entidades como parte da resposta do ponto final da JSON:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

Sem `verbose=true` na corda de consulta:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


Com `verbose=true` na corda de consulta:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

* * *

## <a name="next-steps"></a>Passos seguintes

Consulte [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua app LUIS.
