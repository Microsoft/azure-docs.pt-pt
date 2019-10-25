---
title: Habilidade cognitiva de detecção de linguagem
titleSuffix: Azure Cognitive Search
description: Avalia o texto não estruturado e, para cada registro, retorna um identificador de idioma com uma pontuação indicando a força da análise em um pipeline de enriquecimento de ia no Azure Pesquisa Cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e3ec9ea9cfbae314297c5b59f6a07bcebaef6a5c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791964"
---
#   <a name="language-detection-cognitive-skill"></a>Habilidade cognitiva de detecção de linguagem

A **detecção de idioma** habilidade detecta o idioma do texto de entrada e relata um único código de idioma para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a força da análise. Essa habilidade usa os modelos de aprendizado de máquina fornecidos pelo [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) em serviços cognitivas.

Esse recurso é especialmente útil quando você precisa fornecer o idioma do texto como entrada para outras habilidades (por exemplo, a habilidade de [análise de sentimento](cognitive-search-skill-sentiment.md) ou [habilidade de divisão de texto](cognitive-search-skill-textsplit.md)).

A detecção de idioma aproveita as bibliotecas de processamento de idioma natural do Bing, que excedem o número de [idiomas e regiões com suporte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) listados para análise de texto. A lista exata de idiomas não é publicada, mas inclui todas as linguagens amplamente faladas, além de variantes, dialetos e algumas linguagens regionais e culturais. Se você tiver conteúdo expresso em uma linguagem usada com menos frequência, poderá [tentar a API detecção de idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) para ver se ela retorna um código. A resposta para idiomas que não podem ser detectados é `unknown`.

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Pesquisa Cognitiva. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. O preço de extração de imagem é descrito na [página de preços do Azure pesquisa cognitiva](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. LanguageDetectionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres conforme medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se precisar dividir seus dados antes de enviá-los para o analisador de sentimentos, você poderá usar a [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Entradas de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Entradas     | Descrição |
|--------------------|-------------|
| texto | O texto a ser analisado.|

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome da saída    | Descrição |
|--------------------|-------------|
| languageCode | O código de idioma ISO 6391 para o idioma identificado. Por exemplo, "en". |
| LanguageName | O nome do idioma. Por exemplo, "inglês". |
| placar | Um valor entre 0 e 1. A probabilidade de o idioma ser identificado corretamente. A pontuação pode ser inferior a 1 se a frase tiver idiomas mistos.  |

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
Se o texto for expresso em um idioma sem suporte, um erro será gerado e nenhum identificador de idioma será retornado.

## <a name="see-also"></a>Ver também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
