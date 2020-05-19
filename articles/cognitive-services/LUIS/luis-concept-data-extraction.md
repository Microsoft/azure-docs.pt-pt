---
title: Extração de dados - LUIS
description: Extrair dados de texto de expressão com intenções e entidades. Saiba que tipo de dados podem ser extraídos da Compreensão da Linguagem (LUIS).
author: diberry
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 3b6b09fd1066a9caa745cddf30d76e2843c3f56c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589725"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrair dados de texto de expressão com intenções e entidades
O LUIS dá-lhe a capacidade de obter informações a partir das expressões de linguagem natural de um utilizador. A informação é extraída de uma forma que pode ser usada por um programa, aplicação ou chat bot para tomar medidas. Nas seguintes secções, saiba quais os dados devolvidos de intenções e entidades com exemplos de JSON.

Os dados mais difíceis de extrair são os dados aprendidos pela máquina porque não é uma correspondência exata de texto. A extração de dados das entidades aprendidas por [máquinas](luis-concept-entity-types.md) tem de fazer parte do ciclo de [autor](luis-concept-app-iteration.md) até estar confiante de que recebe os dados que espera.

## <a name="data-location-and-key-usage"></a>Localização de dados e utilização da chave
Luis extrai dados da expressão do utilizador no [ponto final](luis-glossary.md#endpoint)publicado . O **pedido HTTPS** (POST ou GET) contém a expressão, bem como algumas configurações opcionais, tais como ambientes de encenação ou produção.

**Pedido de ponto final de previsão V2**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**Pedido de ponto final de previsão V3**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

A página de `appID` **Definições** da sua aplicação LUIS, bem como parte do URL `/apps/` (depois) quando estiver a editar aquela aplicação LUIS. A `subscription-key` chave final é usada para consultar a sua aplicação. Embora possa utilizar a sua chave de autor/arranque gratuita enquanto está a aprender LUIS, é importante alterar a chave de ponto final para uma chave que suporta o uso esperado do [LUIS.](luis-limits.md#key-limits) A `timezoneOffset` unidade tem minutos.

A **resposta HTTPS** contém todas as informações de intenção e entidade que a LUIS pode determinar com base no modelo publicado atual, quer na encenação quer no ponto final de produção. O URL de ponto final encontra-se no site da [LUIS,](luis-reference-regions.md) na secção **Gerir,** na página **Keys e endpoints.**

## <a name="data-from-intents"></a>Dados de intenções
Os dados primários são o nome de **intenção**de pontuação superior. A resposta final é:

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

|Objeto de dados|Tipo de Dados|Localização de Dados|Valor|
|--|--|--|--|
|Intenção|String|topScoringIntent.intent|"GetStoreInfo"|

Se o seu chatbot ou app de chamada LUIS tomar uma decisão com base em mais de uma pontuação de intenção, devolva todas as pontuações das intenções.


#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

Defina o parâmetro de corda de consulta, `verbose=true` . A resposta final é:

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

Defina o parâmetro de corda de consulta, `show-all-intents=true` . A resposta final é:

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

As intenções são ordenadas da pontuação mais alta para a mais baixa.

|Objeto de dados|Tipo de Dados|Localização de Dados|Valor|Classificação|
|--|--|--|--|:--|
|Intenção|String|intenções[0].intenção|"GetStoreInfo"|0.984749258|
|Intenção|String|intenções[1].intenção|"Nenhum"|0.0168218873|

Se adicionar domínios pré-construídos, o nome de intenção indica o domínio, tal como `Utilties` ou `Communication` a intenção:

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

|Domain|Objeto de dados|Tipo de Dados|Localização de Dados|Valor|
|--|--|--|--|--|
|Utilitários|Intenção|String|intenções[0].intenção|"<b>Serviços públicos</b>. ShowNext"|
|Comunicação|Intenção|String|intenções[1].intenção|<b>Comunicação.</b> Início"|
||Intenção|String|intenção[2].intenção|"Nenhum"|


## <a name="data-from-entities"></a>Dados de entidades
A maioria dos bots de chat e aplicações precisam de mais do que o nome da intenção. Estes dados adicionais e opcionais provêm de entidades descobertas na expressão. Cada tipo de entidade devolve informações diferentes sobre a partida.

Uma única palavra ou frase numa expressão pode corresponder a mais do que uma entidade. Nesse caso, cada entidade correspondente é devolvida com a sua pontuação.

Todas as entidades são devolvidas na matriz de **entidades** da resposta a partir do ponto final

## <a name="tokenized-entity-returned"></a>Entidade tokenizada devolvida

Reveja o [suporte simbólico](luis-language-support.md#tokenization) no LUIS.


## <a name="prebuilt-entity-data"></a>Dados de entidades pré-construídas
[Entidades pré-construídas](luis-concept-entity-types.md) são descobertas com base numa correspondência de expressão regular usando o projeto [Recognisers-Text](https://github.com/Microsoft/Recognizers-Text) de código aberto. As entidades pré-construídas são devolvidas na matriz de entidades e usam o nome tipo pré-fixado com `builtin::` .

## <a name="list-entity-data"></a>Dados da entidade da lista

[As entidades da lista](reference-entity-list.md) representam um conjunto fixo e fechado de palavras relacionadas juntamente com os seus sinónimos. A LUIS não descobre valores adicionais para entidades de lista. Utilize a função **Recomendar** para ver sugestões de novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade é devolvida na consulta final.

## <a name="regular-expression-entity-data"></a>Dados da entidade de expressão regular

Uma [entidade de expressão regular](reference-entity-regular-expression.md) extrai uma entidade com base numa expressão regular que fornece.

## <a name="extracting-names"></a>Extrair nomes
Obter nomes de uma expressão é difícil porque um nome pode ser quase qualquer combinação de letras e palavras. Dependendo do tipo de nome que está a extrair, tem várias opções. As seguintes sugestões não são regras, mas mais orientações.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Adicionar personname pré-construído e geografiaV2 entidades

As entidades [PersonName](luis-reference-prebuilt-person.md) e [GeographyV2](luis-reference-prebuilt-geographyV2.md) estão disponíveis em [algumas culturas linguísticas.](luis-reference-prebuilt-entities.md)

### <a name="names-of-people"></a>Nomes de pessoas

O nome das pessoas pode ter algum formato leve dependendo da linguagem e da cultura. Utilize uma entidade pré-construída **[nome ou](luis-reference-prebuilt-person.md)** uma **[entidade simples](luis-concept-entity-types.md#simple-entity)** com [papéis](luis-concept-roles.md) de primeiro e último nome.

Se utilizar a entidade simples, certifique-se de dar exemplos que usam o primeiro e o último nome em diferentes partes da expressão, em expressões de diferentes comprimentos, e expressões em todas as intenções, incluindo a intenção De None. [Rever](luis-how-to-review-endoint-utt.md) as declarações finais regularmente para rotular quaisquer nomes que não tenham sido corretamente previstos.

### <a name="names-of-places"></a>Nomes de lugares

Os nomes de localização são definidos e conhecidos como cidades, condados, estados, províncias e países/regiões. Utilize a entidade pré-construída **[geografiaV2](luis-reference-prebuilt-geographyv2.md)** para extrair informações de localização.

### <a name="new-and-emerging-names"></a>Nomes novos e emergentes

Algumas aplicações precisam de ser capazes de encontrar novos e emergentes nomes como produtos ou empresas. Estes tipos de nomes são o tipo mais difícil de extração de dados. Comece com uma **[entidade simples](luis-concept-entity-types.md#simple-entity)** e adicione uma lista de [frases.](luis-concept-feature.md) [Rever](luis-how-to-review-endoint-utt.md) as declarações finais regularmente para rotular quaisquer nomes que não tenham sido corretamente previstos.

## <a name="patternany-entity-data"></a>Padrão.qualquer dados de entidade

[Pattern.any](reference-entity-pattern-any.md) é um espaço reservado de comprimento variável usado apenas na expressão do modelo de um padrão para marcar onde a entidade começa e termina. A entidade utilizada no padrão deve ser encontrada para que o padrão seja aplicado. 

## <a name="sentiment-analysis"></a>Análise de sentimentos
Se a análise do Sentimento for configurada durante [a publicação,](luis-how-to-publish-app.md#sentiment-analysis)a resposta do JSON inclui análise de sentimento. Saiba mais sobre a análise de sentimentos na documentação do [Text Analytics.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)

## <a name="key-phrase-extraction-entity-data"></a>Dados da entidade de extração de frases-chave
A entidade de extração de [frases-chave](luis-reference-prebuilt-keyphrase.md) devolve frases-chave na expressão, fornecida por [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

## <a name="data-matching-multiple-entities"></a>Dados que correspondem a várias entidades

Luis devolve todas as entidades descobertas na expressão. Como resultado, o seu chat bot pode ter de tomar uma decisão com base nos resultados.

## <a name="data-matching-multiple-list-entities"></a>Dados que correspondem a várias entidades da lista

Se uma palavra ou frase corresponder a mais do que uma entidade da lista, a consulta de ponto final devolve cada entidade da Lista.

Para a consulta `when is the best time to go to red rock?` , e a app tem a palavra em mais de uma `red` lista, a LUIS reconhece todas as entidades e devolve um conjunto de entidades como parte da resposta do ponto final da JSON.

## <a name="next-steps"></a>Passos seguintes

Consulte [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua app LUIS.
