---
title: Extração de Frases-chave habilidades cognitivas
titleSuffix: Azure Cognitive Search
description: Avalia o texto não estruturado e, para cada registro, retorna uma lista de frases-chave em um pipeline de enriquecimento de ia no Azure Pesquisa Cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ccdd25d82af2b4893260af18dac818816d9e4579
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791984"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Extração de Frases-chave habilidades cognitivas

A **extração de frases-chave** habilidade avalia o texto não estruturado e, para cada registro, retorna uma lista de frases-chave. Essa habilidade usa os modelos de aprendizado de máquina fornecidos pelo [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) em serviços cognitivas.

Esse recurso será útil se você precisar identificar rapidamente os principais pontos de discussão no registro. Por exemplo, dado o texto de entrada "o alimento foi deliciosa e havia uma equipe maravilhosa", o serviço retorna "alimentos" e "equipe maravilhosa".

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Pesquisa Cognitiva. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. O preço de extração de imagem é descrito na [página de preços do Azure pesquisa cognitiva](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres conforme medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar dividir seus dados antes de enviá-los para o extrator de frases-chave, considere usar a [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros diferenciam maiúsculas de minúsculas.

| Entradas                | Descrição |
|---------------------|-------------|
| defaultLanguageCode | Adicional O código de idioma a ser aplicado a documentos que não especificam o idioma explicitamente.  Se o código de idioma padrão não for especificado, o inglês (EN) será usado como o código de idioma padrão. <br/> Consulte a [lista completa de idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | Adicional O número máximo de frases-chave a serem produzidas. |

## <a name="skill-inputs"></a>Entradas de habilidades

| Entradas     | Descrição |
|--------------------|-------------|
| texto | O texto a ser analisado.|
| languageCode  |  Uma cadeia de caracteres que indica o idioma dos registros. Se esse parâmetro não for especificado, o código de idioma padrão será usado para analisar os registros. <br/>Veja a [lista completa de idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Definição de exemplo

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
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
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
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
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Erros e avisos
Se você fornecer um código de idioma sem suporte, um erro será gerado e as frases-chave não serão extraídas.
Se o texto estiver vazio, um aviso será produzido.
Se o texto tiver mais de 50.000 caracteres, somente os primeiros 50.000 caracteres serão analisados e um aviso será emitido.

## <a name="see-also"></a>Ver também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
