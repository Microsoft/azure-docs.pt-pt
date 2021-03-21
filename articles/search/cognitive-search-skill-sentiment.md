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
ms.openlocfilehash: 6519cd952bd1265b4daad3b77b29aabd47ea4cc5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547480"
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

| Nome do Parâmetro | Description |
|----------------|----------------------|
| `defaultLanguageCode` | (opcional) O código linguístico a aplicar a documentos que não especificam explicitamente a língua. <br/> Ver [lista completa de línguas apoiadas](../cognitive-services/text-analytics/language-support.md) |

## <a name="skill-inputs"></a>Entradas de habilidades 

| Nome de entrada | Description |
|--------------------|-------------|
| `text` | O texto a ser analisado.|
| `languageCode`    |  (Opcional) Uma corda que indica a linguagem dos registos. Se este parâmetro não for especificado, o valor predefinido é "en". <br/>Consulte [a lista completa de línguas suportadas.](../cognitive-services/text-analytics/language-support.md)|

## <a name="skill-outputs"></a>Saídas de competências

| Nome de saída | Description |
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

## <a name="warning-cases"></a>Casos de aviso
Se o seu texto estiver vazio, é gerado um aviso e não é devolvida nenhuma pontuação de sentimento.
Se uma língua não for suportada, um aviso é gerado e nenhuma pontuação de sentimento é devolvida.

## <a name="see-also"></a>Ver também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)