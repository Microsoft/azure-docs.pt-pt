---
title: Extração de dados-LUIS
titleSuffix: Azure Cognitive Services
description: Extraia dados de texto expressão com intenções e entidades. Saiba que tipo de dados pode ser extraído de Reconhecimento vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 055cd25f534de5d3cc3ccbe44df88e7111e101a3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560762"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrair dados de texto expressão com intenções e entidades
LUIS dá-lhe a capacidade de obter informações de expressões de linguagem natural com um utilizador. As informações são extraídas de uma forma que pode ser utilizada por um programa, aplicação ou bot de bate-papo para tomar medidas. As secções seguintes, saiba quais dados são retornados de objetivos e entidades com exemplos de JSON.

Os dados mais difíceis de extrair são os dados aprendidos por computador porque não é uma correspondência exata de texto. A extração de dados das [entidades](luis-concept-entity-types.md) aprendidas por computador precisa fazer parte do [ciclo de criação](luis-concept-app-iteration.md) até que você tenha certeza de que você receberá os dados esperados.

## <a name="data-location-and-key-usage"></a>Utilização de localização e a chave de dados
LUIS fornece os dados a partir do publicados [ponto final](luis-glossary.md#endpoint). O **pedido HTTPS** (POST ou GET) contém a expressão, bem como algumas configurações opcionais, como ambientes de teste ou produção.

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

O `appID` está disponível na página **configurações** do aplicativo Luis, bem como parte da URL (depois `/apps/`) quando você está editando o aplicativo Luis. O `subscription-key` é a chave de ponto final utilizada para consultar a sua aplicação. Embora você possa usar sua chave de criação/início gratuita enquanto estiver aprendendo LUIS, é importante alterar a chave do ponto de extremidade para uma chave que dê suporte ao [uso esperado de Luis](luis-boundaries.md#key-limits). O `timezoneOffset` unidade é minutos.

O **resposta HTTPS** contém todas as informações de intenção e entidade LUIS pode determinar com base no modelo publicado atual do ponto final de transição ou produção. O ponto final do URL encontra-se no [LUIS](luis-reference-regions.md) Web site, na **gerir** na secção o **chaves e os pontos finais** página.

## <a name="data-from-intents"></a>Dados desde intenções
Os dados primários são a parte superior de classificação **intenção nome**. Utilizar o `MyStore` [guia de introdução](luis-quickstart-intents-only.md), a resposta do ponto final é:

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

|Objeto de dados|Tipo de Dados|Localização dos Dados|Valor|
|--|--|--|--|
|Intenção|Cadeia|topScoringIntent.intent|"GetStoreInfo"|

Se a aplicação de chamada de LUIS ou chatbot fizer uma decisão com base na mais do que uma classificação de intenção, devolver pontuações dos objetivos ao definir o parâmetro de cadeia de consulta, `verbose=true`. A resposta do ponto final é:

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

Os objetivos são ordenados da mais alta pontuação mais baixa.

|Objeto de dados|Tipo de Dados|Localização dos Dados|Valor|Classificação|
|--|--|--|--|:--|
|Intenção|Cadeia|.intent intenções [0]|"GetStoreInfo"|0.984749258|
|Intenção|Cadeia|.intent intenções [1]|"None"|0.0168218873|

Se adicionar domínios pré-concebidos, o nome de intenção indica o domínio, tal como `Utilties` ou `Communication` , bem como a intenção:

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

|Domain|Objeto de dados|Tipo de Dados|Localização dos Dados|Valor|
|--|--|--|--|--|
|Serviços Públicos|Intenção|Cadeia|.intent intenções [0]|"<b>Utilitários</b>. ShowNext"|
|Comunicação|Intenção|Cadeia|.intent intenções [1]|<b>Comunicação</b>. StartOver"|
||Intenção|Cadeia|.intent intenções [2]|"None"|


## <a name="data-from-entities"></a>Dados de entidades
A maioria dos chatbots e as aplicações necessitam de mais do que o nome de intenção. Adicionais e opcionais vêm os dados detetadas na expressão de entidades. Cada tipo de entidade devolve diferentes informações sobre a correspondência.

Uma única palavra ou frase numa expressão pode corresponder a mais de uma entidade. Nesse caso, cada entidade correspondente é devolvida com sua pontuação.

Todas as entidades são retornadas no **entidades** matriz da resposta do ponto final:

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

## <a name="tokenized-entity-returned"></a>entidade com token devolvida
Várias [culturas](luis-language-support.md#tokenization) devolve o objeto de entidade com o `entity` valor [com token](luis-glossary.md#token). O startIndex e endIndex devolvido pelo LUIS no objeto de entidade não mapeiam para o valor novo, com token, mas sim à consulta original para que extrair a entidade não processada por meio de programação. 

Por exemplo, em alemão, a palavra `das Bauernbrot` é com token em `das bauern brot`. O valor de tokenized `das bauern brot`, é devolvido e o valor original pode ser determinado através de programação do startIndex e endIndex da consulta original, dando-lhe `das Bauernbrot`.

## <a name="simple-entity-data"></a>Dados de entidades simples

R [entidade simple](reference-entity-simple.md) é um valor aprendidas por máquina. Pode ser uma palavra ou frase.

## <a name="composite-entity-data"></a>Dados de entidades compostos

Uma [entidade composta](reference-entity-composite.md) é composta por outras entidades, como entidades predefinidas, expressão simples, regular e entidades de lista. As entidades separadas formam uma entidade inteira. 

## <a name="list-entity-data"></a>Dados de entidades de lista

As [entidades de lista](reference-entity-list.md) representam um conjunto fixo e fechado de palavras relacionadas junto com seus sinônimos. LUIS não Deteta valores adicionais para entidades de lista. Utilize o **Recomendamos** funcionalidade para ver sugestões para novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade é devolvida na consulta de ponto final. 

## <a name="prebuilt-entity-data"></a>Dados de entidade predefinidos
[Pré-criados](luis-concept-entity-types.md) entidades são detetadas com base numa correspondência de expressão regular com o código-fonte aberto [reconhecedores texto](https://github.com/Microsoft/Recognizers-Text) projeto. Entidades pré-concebidas são devolvidas da matriz de entidades e utilizar o nome do tipo com o prefixo `builtin::`. O texto seguinte é uma expressão de exemplo com as entidades retornadas de pré-criados:

`Dec 5th send to +1 360-555-1212`

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

## <a name="regular-expression-entity-data"></a>Dados de entidades de expressão regular

Uma [entidade de expressão regular](reference-entity-regular-expression.md) extrai uma entidade com base em um padrão de expressão regular fornecido por você.

## <a name="extracting-names"></a>Extrair nomes
Obtendo nomes de uma expressão é difícil porque um nome pode ser praticamente qualquer combinação de letras e palavras. Dependendo do tipo de nome que você está extraindo, você tem várias opções. As sugestões a seguir não são regras, mas mais diretrizes.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Adicionar entidades PersonName e GeographyV2 predefinidas

[](luis-reference-prebuilt-person.md) As entidades PersonName e [GeographyV2](luis-reference-prebuilt-geographyV2.md) estão disponíveis em algumas [culturas de idioma](luis-reference-prebuilt-entities.md). 

### <a name="names-of-people"></a>Nomes de pessoas

Nome das pessoas pode ter algum formato ligeiro dependendo do idioma e cultura. Use uma entidade PersonName **[](luis-reference-prebuilt-person.md)** predefinida ou uma **[entidade simples](luis-concept-entity-types.md#simple-entity)** com [funções](luis-concept-roles.md) de First e Last Name. 

Se você usar a entidade simples, certifique-se de fornecer exemplos que usam o nome e o sobrenome em diferentes partes do expressão, em declarações de comprimentos diferentes e declarações em todas as intenções, incluindo a intenção de nenhum. [Revisão](luis-how-to-review-endoint-utt.md) expressões de ponto final em intervalos regulares para rotular todos os nomes que não foram previstos corretamente.

### <a name="names-of-places"></a>Nomes de locais

Os nomes de local são definidos e conhecidos como cidades, municípios, Estados, províncias e países/regiões. Use a entidade predefinida **[geographyV2](luis-reference-prebuilt-geographyv2.md)** para extrair informações de localização.

### <a name="new-and-emerging-names"></a>Nomes de novos e emergentes

Algumas aplicações tem de conseguir encontrar os nomes de novos e emergentes, como produtos ou as empresas. Esses tipos de nomes são o tipo mais difícil de extração de dados. Comece com uma **[entidade simples](luis-concept-entity-types.md#simple-entity)** e adicione uma [lista de frases](luis-concept-feature.md). [Revisão](luis-how-to-review-endoint-utt.md) expressões de ponto final em intervalos regulares para rotular todos os nomes que não foram previstos corretamente.

## <a name="pattern-roles-data"></a>Dados de funções padrão
As funções são as diferenças contextuais das entidades.

```JSON
{
  "query": "move bob jones from seattle to redmond",
  "topScoringIntent": {
    "intent": "MoveAssetsOrPeople",
    "score": 0.9999998
  },
  "intents": [
    {
      "intent": "MoveAssetsOrPeople",
      "score": 0.9999998
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 6.12244548E-07
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 6.12244548E-07
    },
    {
      "intent": "FindForm",
      "score": 1.1E-09
    }
  ],
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
}
```

## <a name="patternany-entity-data"></a>Dados de entidades de Pattern.any

[Padrão. any](reference-entity-pattern-any.md) é um espaço reservado de comprimento variável usado somente no modelo de um padrão expressão para marcar onde a entidade começa e termina.  

## <a name="sentiment-analysis"></a>Análise de sentimentos
Se a análise de sentimentos está configurada, a resposta de json de LUIS inclui a análise de sentimentos. Saiba mais sobre a análise de sentimentos no [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) documentação.

### <a name="sentiment-data"></a>Dados de sentimento
Dados de sentimento são uma pontuação entre 1 e 0 indicando o positivo (mais de perto como 1) nem negativa (mais próximo de 0) sentimentos dos dados.

Quando estiver a cultura `en-us`, a resposta é:

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
A entidade de extração de expressões-chave devolve expressões-chave na expressão, fornecida pela [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

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

## <a name="data-matching-multiple-entities"></a>Correspondência de várias entidades de dados

LUIS devolve todas as entidades detetadas na expressão. Como resultado, o seu chatbot poderá ter de tomar a decisão com base nos resultados. Uma expressão pode ter muitas entidades numa expressão:

`book me 2 adult business tickets to paris tomorrow on air france`

O ponto de extremidade do LUIS pode detetar os mesmos dados em entidades diferentes:

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

## <a name="data-matching-multiple-list-entities"></a>Dados que correspondem a várias entidades de lista

Se uma palavra ou frase corresponder a mais de uma entidade de lista, a consulta de ponto final devolve cada entidade de lista.

Para a consulta `when is the best time to go to red rock?`, e a aplicação com a palavra `red` em mais de uma lista, LUIS reconhece todas as entidades e retorna uma matriz de entidades como parte da resposta do ponto final JSON: 

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

## <a name="next-steps"></a>Passos Seguintes

Ver [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua aplicação LUIS.
