---
title: Habilidade cognitiva de deteção de linguagem
titleSuffix: Azure Cognitive Search
description: Avalia o texto não estruturado e, para cada registo, devolve um identificador linguístico com uma pontuação indicando a força da análise num pipeline de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8439788c63ec1b9feaea148ab52aba498791dc12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76045028"
---
#   <a name="language-detection-cognitive-skill"></a>Habilidade cognitiva de deteção de linguagem

A habilidade de **Deteção de Idiomas** deteta a linguagem do texto de entrada e reporta um único código linguístico para cada documento submetido a pedido. O código linguístico é emparelhado com uma pontuação indicando a força da análise. Esta habilidade utiliza os modelos de machine learning fornecidos pela [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) em Serviços Cognitivos.

Esta capacidade é especialmente útil quando é necessário fornecer a linguagem do texto como entrada para outras competências (por exemplo, a habilidade de Análise de [Sentimentos](cognitive-search-skill-sentiment.md) ou habilidade de [Divisão de Texto).](cognitive-search-skill-textsplit.md)

A deteção de linguagens aproveita as bibliotecas de processamento de linguagem natural de Bing, que excede o número de [línguas e regiões apoiadas](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) listadas para o Text Analytics. A lista exata de línguas não é publicada, mas inclui todas as línguas amplamente faladas, além de variantes, dialetos e algumas línguas regionais e culturais. Se tiver conteúdo expresso num idioma menos utilizado, pode [experimentar a API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) de Deteção de Idiomas para ver se devolve um código. A resposta para línguas que `unknown`não podem ser detetadas é .

> [!NOTE]
> À medida que expande o âmbito aumentando a frequência do processamento, adicionando mais documentos, ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As acusações acumulam-se quando se ligam para apis em Serviços Cognitivos, e para extração de imagem como parte da fase de quebra de documentos na Pesquisa Cognitiva Azure. Não há encargos para a extração de texto de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na página de preços da [Pesquisa Cognitiva Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)50.000 caracteres medido por . Se precisar de separar os seus dados antes de enviá-los para a habilidade de deteção de idiomas, poderá utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Inputs de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Entradas     | Descrição |
|--------------------|-------------|
| texto | O texto a ser analisado.|

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída    | Descrição |
|--------------------|-------------|
| languageCode | O código linguístico ISO 6391 para a língua identificada. Por exemplo, "en". |
| nome de idioma | O nome da linguagem. Por exemplo, "Inglês". |
| classificação | Um valor entre 0 e 1. A probabilidade de a linguagem estar corretamente identificada. A pontuação pode ser inferior a 1 se a frase tiver línguas mistas.  |

##  <a name="sample-definition"></a>Definição de amostra

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Entrada da amostra

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>Resultado da amostra

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Casos de erro
Se o texto for expresso numa linguagem não suportada, gera-se um erro e nenhum identificador de linguagem é devolvido.

## <a name="see-also"></a>Consulte também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
