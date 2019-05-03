---
title: Habilidade de pesquisa cognitiva de deteção de idioma - Azure Search
description: Avalia a texto não estruturado e para cada registo, devolve um identificador de idioma com uma pontuação indicando a força da análise num pipeline de enriquecimento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a076eee9818f294a8e5c4b10cebbcb9e5a55d80c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021853"
---
#   <a name="language-detection-cognitive-skill"></a>Habilidade de cognitiva de deteção de idioma

O **deteção de idioma** habilidade Deteta o idioma de texto de entrada e comunica um código de idioma único para cada documento enviado no pedido. O código de idioma é emparelhado com uma pontuação indicando a força da análise. Essa habilidade usa os modelos de machine learning fornecidos pela [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) nos serviços cognitivos.

Esta capacidade é especialmente útil quando tem de indicar o idioma do texto como entrada para outras habilidades (por exemplo, o [habilidade de análise de sentimentos](cognitive-search-skill-sentiment.md) ou [habilidade de divisão de texto](cognitive-search-skill-textsplit.md)).

Deteção de idioma tira partido das bibliotecas de processamento de linguagem natural do Bing, que excede o número de [idiomas e regiões com suporte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) listados para análise de texto. A lista exata de idiomas não está publicada, mas inclui todos os idiomas amplamente falado, além de variantes, dialetos e alguns idiomas regionais e culturais. Se tiver conteúdo expresso numa linguagem utilizada com menos frequência, pode [experimentar a API de deteção de idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) para ver se ele retorna um código. A resposta para idiomas que não puderem ser detetadas está `unknown`.

> [!NOTE]
> Como expandir âmbito ao aumento da frequência de processamento, adicionar mais documentos ou adicionar mais algoritmos de IA, precisará [anexar um recurso dos serviços cognitivos cobrar](cognitive-search-attach-cognitive-services.md). Os encargos acumulam ao chamar APIs serviços cognitivos e para extração de imagem como parte da fase de aberturas de documentos no Azure Search. Não existem custos para extração de texto de documentos.
>
> Execução de habilidades internas é cobrada existente [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services/). Preços de extração de imagem está descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registo deve ser 50.000 carateres conforme medido pela `String.Length`. Se tiver de dividir os dados antes de os enviar para o analisador de sentimentos, pode utilizar o [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Entradas de habilidades

Parâmetros diferenciam maiúsculas de minúsculas.

| Entradas     | Descrição |
|--------------------|-------------|
| texto | O texto a ser analisados.|

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída    | Descrição |
|--------------------|-------------|
| languageCode | O código de idioma ISO 6391 para o idioma identificado. Por exemplo, "pt". |
| languageName | O nome de idioma. Por exemplo, "inglês". |
| pontuação | Um valor entre 0 e 1. A probabilidade de que o idioma é identificado corretamente. A classificação pode ser inferior a 1, se a frase tem misto de idiomas.  |

##  <a name="sample-definition"></a>Definição de exemplo

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
Se o texto é expressa num idioma não suportado, é gerado um erro e não é devolvido nenhum identificador de idioma.

## <a name="see-also"></a>Consulte também

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
