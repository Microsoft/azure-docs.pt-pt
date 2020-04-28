---
title: Habilidade cognitiva de reconhecimento de entidades
titleSuffix: Azure Cognitive Search
description: Extrair entidades nomeadas para pessoa, localização e organização a partir de texto num oleoduto de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 127155e492b556ce1ce02b67cf0b0846b99ebcd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72791940"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Habilidade cognitiva de reconhecimento de entidades

A habilidade **de reconhecimento de entidades nomeada** extrai entidades nomeadas a partir de texto. As entidades disponíveis `person` `location` incluem os tipos, e `organization`.

> [!IMPORTANT]
> A habilidade de reconhecimento de entidades nomeada é agora descontinuada substituída por [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). O apoio parou no dia 15 de fevereiro de 2019 e a API foi removida do produto no dia 2 de maio de 2019. Siga as recomendações em habilidades de [pesquisa cognitiva deprecatadas](cognitive-search-skill-deprecated.md) para migrar para uma habilidade apoiada.

> [!NOTE]
> À medida que expande o âmbito aumentando a frequência do processamento, adicionando mais documentos, ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As acusações acumulam-se quando se ligam para apis em Serviços Cognitivos, e para extração de imagem como parte da fase de quebra de documentos na Pesquisa Cognitiva Azure. Não há encargos para a extração de texto de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na página de preços da [Pesquisa Cognitiva Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)50.000 caracteres medido por . Se precisar de separar os seus dados antes de enviá-los para o extrator de frases-chave, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| categories    | Conjunto de categorias que devem ser extraídas.  Possíveis tipos `"Person"` `"Location"`de `"Organization"`categoria: . . Se não for fornecida nenhuma categoria, todos os tipos são devolvidos.|
|código de idioma padrão |  Código linguístico do texto de entrada. As seguintes línguas são suportadas:`de, en, es, fr, it`|
| mínimoPrecisão  | Um número entre 0 e 1. Se a precisão for inferior a este valor, a entidade não é devolvida. A predefinição é 0.|

## <a name="skill-inputs"></a>Inputs de habilidade

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| languageCode  | Opcional. A predefinição é `"en"`.  |
| texto          | O texto para analisar.          |

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída     | Descrição                   |
|---------------|-------------------------------|
| pessoas      | Uma variedade de cordas onde cada corda representa o nome de uma pessoa. |
| locais  | Uma variedade de cordas onde cada corda representa uma localização. |
| organizações  | Uma variedade de cordas onde cada corda representa uma organização. |
| entidades | Uma variedade de tipos complexos. Cada tipo complexo inclui os seguintes campos: <ul><li>categoria`"person"`( `"organization"` `"location"`ou)</li> <li>valor (o nome da entidade real)</li><li>offset (A localização onde foi encontrado no texto)</li><li>confiança (Um valor entre 0 e 1 que representa a confiança de que o valor é uma entidade real)</li></ul> |

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
##  <a name="sample-input"></a>Entrada da amostra

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
Se o código linguístico do documento não for suportado, um erro é devolvido e nenhuma entidade é extraída.

## <a name="see-also"></a>Consulte também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Habilidade de Reconhecimento de Entidades](cognitive-search-skill-entity-recognition.md)
