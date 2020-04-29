---
title: Competência cognitiva de reconhecimento de entidades
titleSuffix: Azure Cognitive Search
description: Extrair diferentes tipos de entidades do texto num oleoduto de enriquecimento em Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6ef5952b6413563b2c2e16ff2218f709b414fb84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80297818"
---
#    <a name="entity-recognition-cognitive-skill"></a>Competência cognitiva de reconhecimento de entidades

A habilidade de Reconhecimento de **Entidadeextra** entidades de diferentes tipos de texto. Esta habilidade utiliza os modelos de machine learning fornecidos pela [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) em Serviços Cognitivos.

> [!NOTE]
> À medida que expande o âmbito aumentando a frequência do processamento, adicionando mais documentos, ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As acusações acumulam-se quando se ligam para apis em Serviços Cognitivos, e para extração de imagem como parte da fase de quebra de documentos na Pesquisa Cognitiva Azure. Não há encargos para a extração de texto de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na página de preços da [Pesquisa Cognitiva Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)50.000 caracteres medido por . Se precisar de separar os seus dados antes de enviá-los para o extrator de frases-chave, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis aos casos e são todos opcionais.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| categories    | Conjunto de categorias que devem ser extraídas.  Tipos de `"Person"`categorias possíveis: `"Email"`, `"Location"` `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`. Se não for fornecida nenhuma categoria, todos os tipos são devolvidos.|
|código de idioma padrão |    Código linguístico do texto de entrada. Apoiam-se as seguintes `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans`línguas: . Nem todas as categorias de entidades são suportadas para todas as línguas; ver nota abaixo.|
|mínimoPrecisão | Um valor entre 0 e 1. Se a pontuação `namedEntities` de confiança (na saída) for inferior a este valor, a entidade não é devolvida. A predefinição é 0. |
|incluirDigitas Sem Tipo | Definir `true` para se quiser reconhecer entidades bem conhecidas que não se enquadram nas categorias atuais. Entidades reconhecidas são `entities` devolvidas no complexo campo de saída. Por exemplo, o "Windows 10" é uma entidade conhecida (um produto), mas como "Products" não é uma categoria suportada, esta entidade seria incluída na área de saída de entidades. Padrão é`false` |


## <a name="skill-inputs"></a>Inputs de habilidade

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| languageCode    | Opcional. A predefinição é `"en"`.  |
| texto          | O texto para analisar.          |

## <a name="skill-outputs"></a>Saídas de habilidades

> [!NOTE]
> Nem todas as categorias de entidades são suportadas para todas as línguas. Os `"Person"` `"Location"`tipos `"Organization"` de categoria seleções e entidades são suportados para a lista completa de línguas acima. Apenas _de,_ _en,_ _es,_ _fr_, `"Quantity"`e `"Datetime"` `"URL"` _zh-hans_ support extração de, , , e `"Email"` tipos. Para mais informações, consulte o suporte de [Linguagem e região para a API textanalytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support).  

| Nome de saída      | Descrição                   |
|---------------|-------------------------------|
| pessoas       | Uma variedade de cordas onde cada corda representa o nome de uma pessoa. |
| locais  | Uma variedade de cordas onde cada corda representa uma localização. |
| organizações  | Uma variedade de cordas onde cada corda representa uma organização. |
| quantidades  | Uma variedade de cordas onde cada corda representa uma quantidade. |
| dataTimes  | Uma série de cordas onde cada corda representa um valor DateTime (como aparece no texto). |
| urls | Uma variedade de cordas onde cada corda representa um URL |
| e-mails | Uma variedade de cordas onde cada corda representa um e-mail |
| entidades nomeadas | Uma variedade de tipos complexos que contém os seguintes campos: <ul><li>categoria</li> <li>valor (O nome da entidade real)</li><li>offset (A localização onde foi encontrado no texto)</li><li>confiança (valor mais elevado significa que é mais para ser uma entidade real)</li></ul> |
| entidades | Uma variedade de tipos complexos que contém informações ricas sobre as entidades extraídas de texto, com os seguintes campos <ul><li> nome (o nome da entidade real. Isto representa uma forma "normalizada")</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (um link para a página wikipédia para a entidade)</li><li>bingId</li><li>tipo (a categoria da entidade reconhecida)</li><li>subTipo (disponível apenas para determinadas categorias, isto dá uma visão mais granular do tipo de entidade)</li><li> fósforos (uma coleção complexa que contém)<ul><li>texto (texto bruto para a entidade)</li><li>compensação (o local onde foi encontrado)</li><li>comprimento (o comprimento do texto da entidade bruta)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Definição de amostra

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
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
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##    <a name="sample-input"></a>Entrada da amostra

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Resultado da amostra

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```

Note que as compensações devolvidas para entidades na saída desta habilidade são devolvidas diretamente da API text [analytics,](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)o que significa que se estiver a usá-las para indexar na cadeia original, deve utilizar a classe [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) em .NET para extrair o conteúdo correto.  [Mais detalhes podem ser encontrados aqui.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-cases"></a>Casos de erro
Se o código linguístico do documento não for suportado, um erro é devolvido e nenhuma entidade é extraída.

## <a name="see-also"></a>Consulte também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
