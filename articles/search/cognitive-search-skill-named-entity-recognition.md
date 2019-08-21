---
title: Azure Search de habilidades de pesquisa cognitiva de reconhecimento de entidade nomeada
description: Extraia entidades nomeadas para pessoa, local e organização do texto em um pipeline de pesquisa Azure Search cognitiva.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: b689b577ed940dad03cdf25570187bde98222ee3
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69635882"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Habilidade cognitiva de reconhecimento de entidade nomeada

A habilidade de **reconhecimento de entidade nomeada** extrai entidades nomeadas do texto. As entidades disponíveis incluem os `person`tipos `location` e `organization`.

> [!IMPORTANT]
> A habilidade de reconhecimento de entidade nomeada agora foi descontinuada substituída por [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). O suporte parou em 15 de fevereiro de 2019 e a API foi removida do produto em 2 de maio de 2019. Siga as recomendações em [habilidades de pesquisa cognitiva](cognitive-search-skill-deprecated.md) preteridas para migrar para uma habilidade com suporte.

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Search. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. O preço de extração de imagem é descrito na [página de preços de Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres conforme medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar dividir seus dados antes de enviá-los para o extrator de frases-chave, considere usar a [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| categorias    | Matriz de categorias que devem ser extraídas.  Tipos de categoria possíveis `"Person"`: `"Location"`, `"Organization"`,. Se nenhuma categoria for fornecida, todos os tipos serão retornados.|
|defaultLanguageCode |  Código de idioma do texto de entrada. Há suporte para os seguintes idiomas:`de, en, es, fr, it`|
| minimumPrecision  | Um número entre 0 e 1. Se a precisão for menor que esse valor, a entidade não será retornada. O padrão é 0.|

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| languageCode  | Opcional. A predefinição é `"en"`.  |
| text          | O texto a ser analisado.          |

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome da saída     | Descrição                   |
|---------------|-------------------------------|
| pessoas      | Uma matriz de cadeias de caracteres em que cada cadeia representa o nome de uma pessoa. |
| locations  | Uma matriz de cadeias de caracteres em que cada cadeia representa um local. |
| organizações  | Uma matriz de cadeias de caracteres em que cada cadeia representa uma organização. |
| entidades | Uma matriz de tipos complexos. Cada tipo complexo inclui os seguintes campos: <ul><li>Categoria (`"person"`, `"organization"`, ou `"location"`)</li> <li>valor (o nome real da entidade)</li><li>deslocamento (o local onde ele foi encontrado no texto)</li><li>confiança (um valor entre 0 e 1 que representa a confiança de que o valor é uma entidade real)</li></ul> |

##  <a name="sample-definition"></a>Definição de exemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
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
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
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
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Casos de erro
Se não houver suporte para o código de idioma do documento, um erro será retornado e nenhuma entidade será extraída.

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Habilidade de reconhecimento de entidade](cognitive-search-skill-entity-recognition.md)
