---
title: Competência cognitiva de reconhecimento de entidades
titleSuffix: Azure Cognitive Search
description: Extrair diferentes tipos de entidades a partir de texto num oleoduto de enriquecimento na Pesquisa Cognitiva do Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 3300d68c6356cb7cdd56a9ad79eb0ac011942eb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547905"
---
#    <a name="entity-recognition-cognitive-skill"></a>Competência cognitiva de reconhecimento de entidades

A **competência de Reconhecimento de Entidade** extrai entidades de diferentes tipos de texto. Esta habilidade utiliza os modelos de aprendizagem automática fornecidos pela [Text Analytics](../cognitive-services/text-analytics/overview.md) em Serviços Cognitivos.

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência do processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As taxas acumulam-se ao chamar APIs em Serviços Cognitivos, e para a extração de imagem como parte da fase de cracking de documentos em Azure Cognitive Search. Não há encargos para a extração de texto a partir de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços de Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de 50.000 caracteres medido por [`String.Length`](/dotnet/api/system.string.length) . Se precisar de separar os seus dados antes de os enviar para o extrator de frases-chave, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis a casos e são todos opcionais.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `categories`    | Conjunto de categorias que devem ser extraídas.  Possíveis tipos de categorias: `"Person"` , , , , , , , . `"Location"` `"Organization"` `"Quantity"` `"Datetime"` `"URL"` `"Email"` . Se não for fornecida nenhuma categoria, todos os tipos são devolvidos.|
| `defaultLanguageCode` |    Código linguístico do texto de entrada. São apoiadas as seguintes línguas: `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans` . Nem todas as categorias de entidades são apoiadas para todas as línguas; ver nota abaixo.|
| `minimumPrecision` | Um valor entre 0 e 1. Se a pontuação de confiança (na saída) for inferior a `namedEntities` este valor, a entidade não é devolvida. A predefinição é 0. |
| `includeTypelessEntities` | De definido para `true` se quiser reconhecer entidades conhecidas que não se enquadram nas categorias atuais. Entidades reconhecidas são devolvidas no `entities` complexo campo de saída. Por exemplo, o "Windows 10" é uma entidade conhecida (um produto), mas como "Produtos" não é uma categoria suportada, esta entidade seria incluída no campo de saída das entidades. O padrão é `false` |


## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | Opcional. A predefinição é `"en"`.  |
| `text`          | O texto para analisar.          |

## <a name="skill-outputs"></a>Saídas de competências

> [!NOTE]
> Nem todas as categorias de entidades são suportadas para todas as línguas. Os `"Person"` `"Location"` tipos de categorias , e `"Organization"` entidades são suportados para a lista completa de idiomas acima. Apenas _de,_ _en_, _es_, _fr_, e _zh-hans_ apoiam a extração `"Quantity"` `"Datetime"` de, `"URL"` e `"Email"` tipos. Para mais informações, consulte [o suporte de Língua e região para a API text Analytics](../cognitive-services/text-analytics/language-support.md).  

| Nome de saída      | Description                   |
|---------------|-------------------------------|
| `persons`       | Uma variedade de cordas onde cada corda representa o nome de uma pessoa. |
| `locations`  | Uma matriz de cordas onde cada corda representa uma localização. |
| `organizations`  | Uma variedade de cordas onde cada corda representa uma organização. |
| `quantities`  | Uma matriz de cordas onde cada corda representa uma quantidade. |
| `dateTimes`  | Uma matriz de cordas onde cada string representa um valor DateTime (como aparece no texto). |
| `urls` | Uma matriz de cordas onde cada string representa um URL |
| `emails` | Uma matriz de cordas onde cada string representa um e-mail |
| `namedEntities` | Uma variedade de tipos complexos que contém os seguintes campos: <ul><li>categoria</li> <li>valor (O nome da entidade real)</li><li>offset (O local onde foi encontrado no texto)</li><li>confiança (Maior valor significa que é mais para ser uma entidade real)</li></ul> |
| `entities` | Uma variedade de tipos complexos que contém informações ricas sobre as entidades extraídas do texto, com os seguintes campos <ul><li> nome (o nome da entidade real. Isto representa uma forma "normalizada")</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (um link para a página da Wikipédia para a entidade)</li><li>bingId</li><li>tipo (categoria da entidade reconhecida)</li><li>subtiType (disponível apenas para determinadas categorias, isto dá uma visão mais granular do tipo de entidade)</li><li> fósforos (uma coleção complexa que contém)<ul><li>texto (o texto em bruto para a entidade)</li><li>offset (o local onde foi encontrado)</li><li>comprimento (comprimento do texto da entidade bruta)</li></ul></li></ul> |

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
##    <a name="sample-input"></a>Entrada de exemplo

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

##    <a name="sample-output"></a>Saída de exemplo

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

Note que as compensações devolvidas às entidades na saída desta habilidade são diretamente devolvidas da [Text Analytics API](../cognitive-services/text-analytics/overview.md), o que significa que se estiver a usá-las para indexar na cadeia original, deverá utilizar a classe [StringInfo](/dotnet/api/system.globalization.stringinfo) em .NET para extrair o conteúdo correto.  [Mais detalhes podem ser encontrados aqui.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="warning-cases"></a>Casos de aviso
Se o código linguístico do documento não for suportado, um aviso é devolvido e nenhuma entidade é extraída.

## <a name="see-also"></a>Ver também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)