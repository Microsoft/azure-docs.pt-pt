---
title: Competência cognitiva de reconhecimento de entidades nomeada
titleSuffix: Azure Cognitive Search
description: Extrair entidades nomeadas para pessoa, localização e organização a partir de texto num oleoduto de enriquecimento de IA em Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e598f16c6b441cf986c7ac82d67c037f75be8982
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547446"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Competência cognitiva de reconhecimento de entidades nomeada

A **competência de Reconhecimento de Entidades Nomeadas** extrai entidades nomeadas a partir de texto. As entidades disponíveis incluem os `person` tipos, `location` e `organization` .

> [!IMPORTANT]
> A competência de reconhecimento de entidades nomeadas é agora descontinuada substituída por [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). O apoio parou no dia 15 de fevereiro de 2019 e a API foi removida do produto no dia 2 de maio de 2019. Siga as recomendações em [competências de pesquisa cognitiva deprecadas](cognitive-search-skill-deprecated.md) para migrar para uma habilidade apoiada.

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência do processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As taxas acumulam-se ao chamar APIs em Serviços Cognitivos, e para a extração de imagem como parte da fase de cracking de documentos em Azure Cognitive Search. Não há encargos para a extração de texto a partir de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços de Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.text.namedEntityRecognitionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de 50.000 caracteres medido por [`String.Length`](/dotnet/api/system.string.length) . Se precisar de separar os seus dados antes de os enviar para o extrator de frases-chave, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| categories    | Conjunto de categorias que devem ser extraídas.  Possíveis tipos de categorias: `"Person"` `"Location"` . . . `"Organization"` . Se não for fornecida nenhuma categoria, todos os tipos são devolvidos.|
|Código de padrãoLanguage |  Código linguístico do texto de entrada. São apoiadas as seguintes línguas: `de, en, es, fr, it`|
| mínimaPrecision  | Um número entre 0 e 1. Se a precisão for inferior a este valor, a entidade não é devolvida. A predefinição é 0.|

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Description                   |
|---------------|-------------------------------|
| languageCode  | Opcional. A predefinição é `"en"`.  |
| texto          | O texto para analisar.          |

## <a name="skill-outputs"></a>Saídas de competências

| Nome de saída     | Description                   |
|---------------|-------------------------------|
| pessoas      | Uma variedade de cordas onde cada corda representa o nome de uma pessoa. |
| Locais  | Uma matriz de cordas onde cada corda representa uma localização. |
| organizações  | Uma variedade de cordas onde cada corda representa uma organização. |
| entidades | Uma variedade de tipos complexos. Cada tipo complexo inclui os seguintes campos: <ul><li>categoria `"person"` `"organization"` (, , ou `"location"` )</li> <li>valor (o nome da entidade real)</li><li>offset (O local onde foi encontrado no texto)</li><li>confiança (Um valor entre 0 e 1 que representa essa confiança de que o valor é uma entidade real)</li></ul> |

##  <a name="sample-definition"></a>Definição de amostra

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

##  <a name="sample-output"></a>Saída de exemplo

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


## <a name="warning-cases"></a>Casos de aviso
Se o código linguístico do documento não for suportado, um aviso é devolvido e nenhuma entidade é extraída.

## <a name="see-also"></a>Ver também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Competência de Reconhecimento de Entidades](cognitive-search-skill-entity-recognition.md)