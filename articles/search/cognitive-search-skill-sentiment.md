---
title: Habilidade cognitiva de sentimento
titleSuffix: Azure Cognitive Search
description: Extrair uma pontuação de sentimento positiva-negativa do texto num oleoduto de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3aab703b9c5ffcb5f3280060417ce32fcec2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791902"
---
# <a name="sentiment-cognitive-skill"></a>Habilidade cognitiva de sentimento

A habilidade **sentimentais** avalia o texto não estruturado ao longo de um contínuo positivo-negativo, e para cada registo, devolve uma pontuação numérica entre 0 e 1. Pontuações próximas de 1 indicam sentimento positivo, e pontuações próximas de 0 indicam sentimento negativo. Esta habilidade utiliza os modelos de machine learning fornecidos pela [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) em Serviços Cognitivos.

> [!NOTE]
> À medida que expande o âmbito aumentando a frequência do processamento, adicionando mais documentos, ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As acusações acumulam-se quando se ligam para apis em Serviços Cognitivos, e para extração de imagem como parte da fase de quebra de documentos na Pesquisa Cognitiva Azure. Não há encargos para a extração de texto de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na página de preços da [Pesquisa Cognitiva Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)de 5000 caracteres medido por . Se precisar de separar os seus dados antes de enviá-los para o analisador de sentimentos, utilize a [habilidade Text Split](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do Parâmetro |                      |
|----------------|----------------------|
| código de idioma padrão | (opcional) O código linguístico a aplicar a documentos que não especificam explicitamente a linguagem. <br/> Ver [lista completa de línguas suportadas](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Inputs de habilidade 

| Nome de entrada | Descrição |
|--------------------|-------------|
| texto | O texto a ser analisado.|
| languageCode  |  (Opcional) Uma corda indicando a linguagem dos registos. Se este parâmetro não for especificado, o valor predefinido é "en". <br/>Consulte [a lista completa de línguas suportadas](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída | Descrição |
|--------------------|-------------|
| classificação | Um valor entre 0 e 1 que representa o sentimento do texto analisado. Os valores próximos a 0 têm sentimento negativo, perto de 0,5 têm sentimento neutro, e os valores perto de 1 têm sentimento positivo.|


##  <a name="sample-definition"></a>Definição de amostra

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Resultado da amostra

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Notas
Se estiver vazio, uma pontuação de sentimento não é devolvida para esses registos.

## <a name="error-cases"></a>Casos de erro
Se uma linguagem não for suportada, gera-se um erro e não é devolvida nenhuma pontuação de sentimento.

## <a name="see-also"></a>Consulte também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
