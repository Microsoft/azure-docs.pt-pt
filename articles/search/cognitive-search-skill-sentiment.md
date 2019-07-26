---
title: Habilidades de pesquisa cognitiva de opiniões-Azure Search
description: Extraia uma pontuação de sentimentos positiva negativa do texto em um pipeline de enriquecimento Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f950bea4ea32ecc95b1721c6930903c3afaee848
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347669"
---
#   <a name="sentiment-cognitive-skill"></a>Habilidade cognitiva de sentimentos

A habilidade de **sentimentos** avalia o texto não estruturado ao longo de uma sequência positiva negativa e, para cada registro, retorna uma pontuação numérica entre 0 e 1. As pontuações próximas de 1 indicam um sentimento positivo, enquanto as pontuações próximas de 0 indicam um sentimento negativo. Essa habilidade usa os modelos de aprendizado de máquina fornecidos pelo [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) em serviços cognitivas.

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Search. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. O preço de extração de imagem é descrito na [página de preços de Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 5000 caracteres conforme medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar dividir seus dados antes de enviá-los para o analisador de sentimentos, use a [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro |                      |
|----------------|----------------------|
| defaultLanguageCode | adicional O código de idioma a ser aplicado a documentos que não especificam o idioma explicitamente. <br/> Veja a [lista completa de idiomas com suporte](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Entradas de habilidades 

| Nome de entrada | Descrição |
|--------------------|-------------|
| text | O texto a ser analisado.|
| languageCode  |  Adicional Uma cadeia de caracteres que indica o idioma dos registros. Se esse parâmetro não for especificado, o valor padrão será "en". <br/>Consulte a [lista completa de idiomas com suporte](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome da saída | Descrição |
|--------------------|-------------|
| pontuação | Um valor entre 0 e 1 que representa a suopinião do texto analisado. Os valores próximos a 0 têm uma emoção negativa, perto de 0,5 têm uma opinião neutra e os valores próximos a 1 têm uma opinião positiva.|


##  <a name="sample-definition"></a>Definição de exemplo

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
Se estiver vazio, uma pontuação de sentimentos não será retornada para esses registros.

## <a name="error-cases"></a>Casos de erro
Se não houver suporte para um idioma, um erro será gerado e nenhuma pontuação de sentimentos será retornada.

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
