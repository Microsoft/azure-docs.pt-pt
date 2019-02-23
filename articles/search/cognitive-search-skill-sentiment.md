---
title: Habilidade de pesquisa cognitiva sentimentos - Azure Search
description: Extrair uma pontuação de sentimento positivo negativo de texto num pipeline de enriquecimento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 03e6cd89280adff508f7c30ba7567664cc8ac9bd
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737370"
---
#   <a name="sentiment-cognitive-skill"></a>Competências cognitivas de sentimentos

O **sentimentos** habilidade avalia o texto não estruturado ao longo de um continuum positivo negativo e para cada registo, devolve uma pontuação numérica entre 0 e 1. As pontuações próximas de 1 indicam um sentimento positivo, enquanto as pontuações próximas de 0 indicam um sentimento negativo. Essa habilidade usa os modelos de machine learning fornecidos pela [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) nos serviços cognitivos.

> [!NOTE]
> A partir de 21 de Dezembro de 2018, pode [anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md) com um conjunto de capacidades do Azure Search. Isso permite que iniciemos a cobrança de consumo para a execução do conjunto de capacidades. Esta data, também Começámos a cobrar para extração de imagem como parte da fase de aberturas de documentos. Extração de texto de documentos continua a ser disponibilizado sem custos adicionais.
>
> [Competências cognitivas incorporada](cognitive-search-predefined-skills.md) execução é cobrada a [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services), em para classificar o mesmo como se realizados diretamente a tarefa. Extração de imagem é um valor de Azure Search, atualmente oferecido a preços de pré-visualização. Para obter detalhes, consulte a [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) ou [como funciona a faturação](search-sku-tier.md#how-billing-works).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registo deve ser 5000 carateres conforme medido pela `String.Length`. Se tiver de dividir os dados antes de os enviar para o analisador de sentimentos, utilize o [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parâmetros de habilidades

Parâmetros diferenciam maiúsculas de minúsculas.

| Nome do Parâmetro |                      |
|----------------|----------------------|
| defaultLanguageCode | (opcional) O código de idioma para aplicar a documentos que não especificar explicitamente o idioma. <br/> Consulte [lista completa das linguagens suportadas](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Entradas de habilidades 

| Nome de entrada | Descrição |
|--------------------|-------------|
| texto | O texto a ser analisados.|
| languageCode  |  (Opcional) Uma cadeia que indica o idioma dos registos. Se este parâmetro não for especificado, o valor predefinido é "en". <br/>Ver [uma lista completa das linguagens suportadas](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída | Descrição |
|--------------------|-------------|
| pontuação | Um valor entre 0 e 1 que representa o sentimento do texto analisado. Valores próximas de 0 têm um sentimento negativo, próximo 0,5 têm neutro sentimentos e valores próximas de 1 tem um sentimento positivo.|


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
Se estiver vazio, uma pontuação de sentimento não é devolvida para esses registos.

## <a name="error-cases"></a>Casos de erro
Se um idioma não for suportado, é gerado um erro e nenhuma classificação de sentimento é retornada.

## <a name="see-also"></a>Consulte também

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
