---
title: Extração de dados - LUIS
description: Extrair dados do texto de expressão com intenções e entidades. Saiba que tipo de dados podem ser extraídos da Compreensão da Língua (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: dd7d113b1c23a0afec82a346e0f7baa1254ebbed
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500146"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrair dados do texto de expressão com intenções e entidades
O LUIS dá-lhe a capacidade de obter informações a partir das expressões de linguagem natural de um utilizador. A informação é extraída de forma a que possa ser usada por um programa, aplicação ou chat bot para tomar medidas. Nas secções seguintes, saiba quais os dados devolvidos de intenções e entidades com exemplos de JSON.

Os dados mais difíceis de extrair são os dados de aprendizagem automática porque não é uma correspondência de texto exata. A extração de [dados](luis-concept-entity-types.md) das entidades de aprendizagem automática tem de fazer parte do ciclo de [autoria](luis-concept-app-iteration.md) até estar confiante de que recebe os dados que espera.

## <a name="data-location-and-key-usage"></a>Localização de dados e utilização de chaves
A LUIS extrai dados da expressão do utilizador no [ponto final](luis-glossary.md#endpoint)publicado. O **pedido HTTPS** (POST ou GET) contém a expressão, bem como algumas configurações opcionais, tais como ambientes de encenação ou produção.

**Pedido de ponto final de previsão V2**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**Pedido de ponto final de previsão V3**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

A `appID` página de **Definições** da sua app LUIS, bem como parte do URL (depois `/apps/` ) quando está a editar a aplicação LUIS. Esta `subscription-key` é a chave de ponto final utilizada para consultar a sua aplicação. Embora possa utilizar a sua tecla de autoria/arranque gratuita enquanto está a aprender LUIS, é importante alterar a chave de ponta para uma chave que suporte a sua [utilização luis esperada](luis-limits.md#key-limits). A `timezoneOffset` unidade é de minutos.

A **resposta HTTPS** contém todas as informações de intenção e entidade que a LUIS pode determinar com base no modelo atual publicado, quer da encenação quer do ponto final de produção. O URL de ponto final encontra-se no site da [LUIS,](luis-reference-regions.md) na secção **Manage,** na página **Chaves e pontos finais.**

## <a name="data-from-intents"></a>Dados de intenções
Os dados primários são o **nome de intenção** de pontuação superior. A resposta do ponto final é:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta do ponto final da previsão V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Resposta do ponto final de previsão V3](#tab/V3)

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

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

* * *

|Objeto de dados|Tipo de Dados|Localização de Dados|Valor|
|--|--|--|--|
|Intenção|String|topScoringIntent.intent|"GetStoreInfo"|

Se o seu chatbot ou app LUIS-call tomar uma decisão com base em mais de uma pontuação de intenção, devolva todas as pontuações das intenções.


#### <a name="v2-prediction-endpoint-response"></a>[Resposta do ponto final da previsão V2](#tab/V2)

Desa estaladem o parâmetro de `verbose=true` consulta, . A resposta do ponto final é:

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

#### <a name="v3-prediction-endpoint-response"></a>[Resposta do ponto final de previsão V3](#tab/V3)

Desa estaladem o parâmetro de `show-all-intents=true` consulta, . A resposta do ponto final é:

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

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

* * *

As intenções são ordenadas da pontuação mais alta para a menor.

|Objeto de dados|Tipo de Dados|Localização de Dados|Valor|Resultado|
|--|--|--|--|:--|
|Intenção|String|intenções[0].intenção|"GetStoreInfo"|0.984749258|
|Intenção|String|intenções[1].intenção|"Nenhum"|0.0168218873|

Se adicionar domínios pré-construídos, o nome de intenção indica o domínio, tais como `Utilties` ou bem como a `Communication` intenção:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta do ponto final da previsão V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Resposta do ponto final de previsão V3](#tab/V3)

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

Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).

* * *

|Domínio|Objeto de dados|Tipo de Dados|Localização de Dados|Valor|
|--|--|--|--|--|
|Utilitários|Intenção|String|intenções[0].intenção|"<b>Utilities</b>. ShowNext"|
|Comunicação|Intenção|String|intenções[1].intenção|<b>Comunicação</b>. StartOver"|
||Intenção|String|intenções[2].intenção|"Nenhum"|


## <a name="data-from-entities"></a>Dados de entidades
A maioria dos bots de chat e aplicações precisam mais do que o nome de intenção. Estes dados adicionais e opcionais provêm de entidades descobertas na expressão. Cada tipo de entidade devolve informações diferentes sobre a partida.

Uma única palavra ou frase numa expressão pode corresponder a mais do que uma entidade. Nesse caso, cada entidade correspondente é devolvida com a sua pontuação.

Todas as entidades são devolvidas no conjunto de **entidades** da resposta a partir do ponto final

## <a name="tokenized-entity-returned"></a>Entidade tokenized devolvida

Reveja o [suporte simbólico](luis-language-support.md#tokenization) no LUIS.


## <a name="prebuilt-entity-data"></a>Dados de entidades pré-construídas
As entidades [pré-construídas](luis-concept-entity-types.md) são descobertas com base numa correspondência de expressão regular utilizando o projeto [Reconhecedores-Texto de](https://github.com/Microsoft/Recognizers-Text) código aberto. As entidades pré-construídas são devolvidas no conjunto de entidades e utilizam o nome-tipo pré-fixado com `builtin::` .

## <a name="list-entity-data"></a>Listar dados de entidades

[As entidades da lista](reference-entity-list.md) representam um conjunto fixo e fechado de palavras relacionadas juntamente com os seus sinónimos. A LUIS não descobre valores adicionais para as entidades de lista. Utilize a função **Recomendação** para ver sugestões de novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade é devolvida na consulta de ponto final.

## <a name="regular-expression-entity-data"></a>Dados regulares da entidade de expressão

Uma [entidade de expressão regular](reference-entity-regular-expression.md) extrai uma entidade com base numa expressão regular que fornece.

## <a name="extracting-names"></a>Extrair nomes
Obter nomes de uma expressão é difícil porque um nome pode ser quase qualquer combinação de letras e palavras. Dependendo do tipo de nome que está a extrair, tem várias opções. As seguintes sugestões não são regras, mas mais orientações.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Adicionar entidades pré-construídas personName e GeografiaV2

[As entidades PersonName](luis-reference-prebuilt-person.md) e [GeografiaV2](luis-reference-prebuilt-geographyV2.md) estão disponíveis em [algumas culturas linguísticas.](luis-reference-prebuilt-entities.md)

### <a name="names-of-people"></a>Nomes de pessoas

O nome das pessoas pode ter um pequeno formato dependendo da linguagem e da cultura. Utilize uma pessoa pré-construída **[Entidade nome](luis-reference-prebuilt-person.md)** ou uma **[entidade simples](luis-concept-entity-types.md)** com funções de primeiro e último nome.

Se utilizar a entidade simples, certifique-se de dar exemplos que usam o primeiro e o último nome em diferentes partes da expressão, em expressões de diferentes comprimentos, e expressões em todas as intenções, incluindo a intenção de Zero. [Reveja](./luis-how-to-review-endpoint-utterances.md) as declarações de ponto final regularmente para rotular quaisquer nomes que não foram previstos corretamente.

### <a name="names-of-places"></a>Nomes de lugares

Os nomes de localização são definidos e conhecidos como cidades, condados, estados, províncias e países/regiões. Utilize a entidade pré-construída **[geografiaV2](luis-reference-prebuilt-geographyv2.md)** para extrair informações de localização.

### <a name="new-and-emerging-names"></a>Nomes novos e emergentes

Algumas aplicações precisam de ser capazes de encontrar nomes novos e emergentes, como produtos ou empresas. Este tipo de nomes são o tipo mais difícil de extração de dados. Comece com uma **[entidade simples](luis-concept-entity-types.md)** e adicione uma lista [de frases](luis-concept-feature.md). [Reveja](./luis-how-to-review-endpoint-utterances.md) as declarações de ponto final regularmente para rotular quaisquer nomes que não foram previstos corretamente.

## <a name="patternany-entity-data"></a>Padrão.quaisquer dados de entidade

[Padrão.qualquer](reference-entity-pattern-any.md) um é um espaço reservado de comprimento variável usado apenas na expressão do modelo de um padrão para marcar onde a entidade começa e termina. A entidade utilizada no padrão deve ser encontrada para que o padrão seja aplicado.

## <a name="sentiment-analysis"></a>Análise de sentimentos
Se a análise do sentimento estiver configurada durante [a publicação, a](luis-how-to-publish-app.md#sentiment-analysis)resposta do JSON LUIS inclui a análise do sentimento. Saiba mais sobre a análise de sentimento na documentação [text Analytics.](../text-analytics/index.yml)

## <a name="key-phrase-extraction-entity-data"></a>Dados da entidade de extração de frases-chave
A [entidade de extração de frases-chave](luis-reference-prebuilt-keyphrase.md) devolve frases-chave na expressão, fornecidas pela [Text Analytics](../text-analytics/index.yml).

## <a name="data-matching-multiple-entities"></a>Dados que combinam com várias entidades

LUIS devolve todas as entidades descobertas na expressão. Como resultado, o seu chat bot pode precisar de tomar uma decisão com base nos resultados.

## <a name="data-matching-multiple-list-entities"></a>Dados que combinam várias entidades de lista

Se uma palavra ou frase corresponder a mais de uma entidade de lista, a consulta de ponto final devolve cada entidade lista.

Para a consulta `when is the best time to go to red rock?` , e a app tem a palavra em mais de uma `red` lista, a LUIS reconhece todas as entidades e devolve um conjunto de entidades como parte da resposta do ponto final da JSON.

## <a name="next-steps"></a>Passos seguintes

Consulte [entidades Add](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua app LUIS.
