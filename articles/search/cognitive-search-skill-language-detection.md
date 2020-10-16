---
title: Habilidade cognitiva de deteção de linguagem
titleSuffix: Azure Cognitive Search
description: Avalia o texto não estruturado e, para cada registo, devolve um identificador de linguagem com uma pontuação indicando a força da análise num pipeline de enriquecimento de IA em Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 087989638193bb59001ed33c4ee253d61682d8bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935998"
---
#   <a name="language-detection-cognitive-skill"></a>Habilidade cognitiva de deteção de linguagem

A habilidade **de Deteção de Idiomas** deteta a linguagem do texto de entrada e reporta um único código linguístico para cada documento submetido no pedido. O código linguístico é emparelhado com uma pontuação que indica a força da análise. Esta habilidade utiliza os modelos de aprendizagem automática fornecidos pela [Text Analytics](../cognitive-services/text-analytics/overview.md) em Serviços Cognitivos.

Esta capacidade é especialmente útil quando precisa de fornecer a linguagem do texto como entrada para outras habilidades (por exemplo, a [habilidade de Análise](cognitive-search-skill-sentiment.md) de Sentimento ou Divisão de [Texto).](cognitive-search-skill-textsplit.md)

A deteção de linguagens aproveita as bibliotecas de processamento de linguagem natural de Bing, que excede o número de [línguas e regiões apoiadas listadas](../cognitive-services/text-analytics/language-support.md) para a Text Analytics. A lista exata de línguas não é publicada, mas inclui todas as línguas amplamente faladas, além de variantes, dialetos e algumas línguas regionais e culturais. Se tiver conteúdo expresso num idioma menos utilizado, pode [experimentar a API de Deteção de Idiomas](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) para ver se devolve um código. A resposta para línguas que não podem ser detetadas é `unknown` .

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência do processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As taxas acumulam-se ao chamar APIs em Serviços Cognitivos, e para a extração de imagem como parte da fase de cracking de documentos em Azure Cognitive Search. Não há encargos para a extração de texto a partir de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços de Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionskill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de 50.000 caracteres medido por [`String.Length`](/dotnet/api/system.string.length) . Se precisar de separar os seus dados antes de os enviar para a habilidade de deteção de idiomas, poderá utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Entradas de habilidades

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Entradas     | Descrição |
|--------------------|-------------|
| `text` | O texto a ser analisado.|

## <a name="skill-outputs"></a>Saídas de competências

| Nome de saída    | Descrição |
|--------------------|-------------|
| `languageCode` | O código linguístico ISO 6391 para a língua identificada. Por exemplo, "en". |
| `languageName` | O nome da linguagem. Por exemplo, "Inglês". |
| `score` | Um valor entre 0 e 1. A probabilidade de que a linguagem esteja corretamente identificada. A pontuação pode ser inferior a 1 se a frase tiver línguas mistas.  |

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

##  <a name="sample-input"></a>Entrada de exemplo

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


##  <a name="sample-output"></a>Saída de exemplo

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
Se o texto for expresso numa língua não apoiada, é gerado um erro e nenhum identificador de idiomas é devolvido.

## <a name="see-also"></a>Consulte também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)