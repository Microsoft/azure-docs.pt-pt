---
title: Capacidade cognitiva do sentimento
titleSuffix: Azure Cognitive Search
description: Extrair uma pontuação positiva-negativa do texto num oleoduto de enriquecimento de IA na Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 895e22d94122539ee68bcac8ee4debce2d824ff0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935284"
---
# <a name="sentiment-cognitive-skill"></a>Capacidade cognitiva do sentimento

A habilidade **sentimentais** avalia o texto não estruturado ao longo de um contínuo positivo-negativo, e para cada registo, devolve uma pontuação numérica entre 0 e 1. Pontuações próximas de 1 indicam sentimento positivo, e pontuações perto de 0 indicam sentimento negativo. Esta habilidade utiliza os modelos de aprendizagem automática fornecidos pela [Text Analytics](../cognitive-services/text-analytics/overview.md) em Serviços Cognitivos.

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência do processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As taxas acumulam-se ao chamar APIs em Serviços Cognitivos, e para a extração de imagem como parte da fase de cracking de documentos em Azure Cognitive Search. Não há encargos para a extração de texto a partir de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços de Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de 5000 caracteres medido por [`String.Length`](/dotnet/api/system.string.length) . Se precisar de separar os seus dados antes de os enviar para o analisador de sentimentos, utilize a [habilidade Text Split](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do Parâmetro | Descrição |
|----------------|----------------------|
| `defaultLanguageCode` | (opcional) O código linguístico a aplicar a documentos que não especificam explicitamente a língua. <br/> Ver [lista completa de línguas apoiadas](../cognitive-services/text-analytics/language-support.md) |

## <a name="skill-inputs"></a>Entradas de habilidades 

| Nome de entrada | Descrição |
|--------------------|-------------|
| `text` | O texto a ser analisado.|
| `languageCode`    |  (Opcional) Uma corda que indica a linguagem dos registos. Se este parâmetro não for especificado, o valor predefinido é "en". <br/>Consulte [a lista completa de línguas suportadas.](../cognitive-services/text-analytics/language-support.md)|

## <a name="skill-outputs"></a>Saídas de competências

| Nome de saída | Descrição |
|--------------------|-------------|
| `score` | Um valor entre 0 e 1 que representa o sentimento do texto analisado. Valores próximos de 0 têm sentimento negativo, perto de 0,5 têm sentimento neutro, e valores próximos de 1 têm sentimento positivo.|


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

##  <a name="sample-input"></a>Entrada de exemplo

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


##  <a name="sample-output"></a>Saída de exemplo

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
Se uma língua não for suportada, um erro é gerado e nenhuma pontuação de sentimento é devolvida.

## <a name="see-also"></a>Consulte também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)