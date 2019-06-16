---
title: Habilidade de pesquisa cognitiva de reconhecimento de entidade - Azure Search
description: Extrair a diferentes tipos de entidades de texto num pipeline do Azure Search pesquisa cognitiva.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f05161dbbfd9293cd7b1cbf447bb7ca1c313250c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65023451"
---
#    <a name="entity-recognition-cognitive-skill"></a>Competências cognitivas de reconhecimento de entidades

O **reconhecimento de entidades** habilidade extrai entidades de diferentes tipos de texto. Essa habilidade usa os modelos de machine learning fornecidos pela [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) nos serviços cognitivos.

> [!NOTE]
> Como expandir âmbito ao aumento da frequência de processamento, adicionar mais documentos ou adicionar mais algoritmos de IA, precisará [anexar um recurso dos serviços cognitivos cobrar](cognitive-search-attach-cognitive-services.md). Os encargos acumulam ao chamar APIs serviços cognitivos e para extração de imagem como parte da fase de aberturas de documentos no Azure Search. Não existem custos para extração de texto de documentos.
>
> Execução de habilidades internas é cobrada existente [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services/). Preços de extração de imagem está descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registo deve ser 50.000 carateres conforme medido pela `String.Length`. Se tiver de dividir os dados antes de os enviar para o extrator de expressões-chave, considere utilizar o [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidades

Parâmetros diferenciam maiúsculas de minúsculas e são todos opcionais.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| categories    | Matriz de categorias que deve ser extraído.  Tipos de categoria possíveis: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Se não for fornecida nenhum categoria, todos os tipos são devolvidos.|
|defaultLanguageCode |  Código de idioma do texto de entrada. São suportados os seguintes idiomas: `de, en, es, fr, it`|
|minimumPrecision | Não utilizado. Reservado para utilização futura. |
|includeTypelessEntities | Quando definido como VERDADEIRO se o texto contém uma entidade bem conhecida, mas não pode ser categorizado em uma das categorias suportadas, ele será retornado como parte do `"entities"` campo de saída complexa. 
Estes são entidades que são bem conhecidas, mas não classificadas como parte das atuais suportadas "categorias". Por exemplo "Windows 10" é uma entidade bem conhecida (um produto), mas "Produtos" não estão nas categorias atualmente suportadas. A predefinição é `false` |


## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| languageCode  | Opcional. A predefinição é `"en"`.  |
| texto          | O texto a analisar.          |

## <a name="skill-outputs"></a>Saídas de habilidades

> [!NOTE]
> Nem todas as categorias de entidade são suportadas para todos os idiomas. Apenas _en_, _es_ suporta a extração de `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"` tipos.

| Nome de saída     | Descrição                   |
|---------------|-------------------------------|
| pessoas      | Uma matriz de cadeias de caracteres em que cada cadeia de caracteres representa o nome de uma pessoa. |
| locations  | Uma matriz de cadeias de caracteres em que cada cadeia de caracteres representa uma localização. |
| organizações  | Uma matriz de cadeias de caracteres em que cada cadeia de caracteres representa uma organização. |
| quantidades  | Uma matriz de cadeias de caracteres em que cada cadeia de caracteres representa uma quantidade. |
| dateTimes  | Uma matriz de cadeias de caracteres em que cada cadeia de caracteres representa um DateTime (tal como aparece no texto) valor. |
| urls | Uma matriz de cadeias de caracteres em que cada cadeia de caracteres representa um URL |
| mensagens de correio eletrónico | Uma matriz de cadeias de caracteres em que cada cadeia de caracteres representa uma mensagem de e-mail |
| namedEntities | Uma matriz de tipos complexos que contém os seguintes campos: <ul><li>category</li> <li>valor (o nome de entidade real)</li><li>deslocamento (localização onde foi encontrado no texto)</li><li>confiança (não utilizados por agora. Será definido como um valor de -1)</li></ul> |
| Entidades | Uma matriz de tipos complexos que contém informações detalhadas sobre as entidades extraídos de texto, com os seguintes campos <ul><li> nome (o nome de entidade real. Isso representa um formulário de "normalizado")</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (uma ligação para a página da Wikipédia da entidade)</li><li>bingId</li><li>tipo (a categoria da entidade reconhecida)</li><li>Subtipo (disponível apenas para determinadas categorias, isso dá uma vista mais granular do tipo de entidade)</li><li> corresponde a (uma coleção complexa que contém)<ul><li>texto (o texto não processado para a entidade)</li><li>deslocamento (localização onde foi encontrado)</li><li>comprimento (o comprimento do texto de entidade não processados)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Definição de exemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
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
##  <a name="sample-input"></a>Entrada de exemplo

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

##  <a name="sample-output"></a>Resultado da amostra

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
            "confidence": -1
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


## <a name="error-cases"></a>Casos de erro
Se o código de idioma para o documento não é suportado, é devolvido um erro e não existem entidades são extraídas.

## <a name="see-also"></a>Consulte também

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
