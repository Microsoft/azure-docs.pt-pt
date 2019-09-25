---
title: Habilidades de pesquisa cognitiva de reconhecimento de entidade-Azure Search
description: Extrair diferentes tipos de entidades do texto em um pipeline de pesquisa Azure Search cognitiva.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: ad2fef96491c2d1a15ad9ff5f57d2911dfecaa36
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265794"
---
#    <a name="entity-recognition-cognitive-skill"></a>Habilidade cognitiva de reconhecimento de entidade

A habilidade de **reconhecimento de entidade** extrai entidades de tipos diferentes do texto. Essa habilidade usa os modelos de aprendizado de máquina fornecidos pelo [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) em serviços cognitivas.

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Search. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. O preço de extração de imagem é descrito na [página de preços de Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres conforme medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar dividir seus dados antes de enviá-los para o extrator de frases-chave, considere usar a [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros diferenciam maiúsculas de minúsculas e são todos opcionais.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| categorias    | Matriz de categorias que devem ser extraídas.  Tipos de categoria possíveis `"Person"`: `"Location"` `"Organization"`, ,,`"Quantity"`,, ,.`"Email"` `"URL"` `"Datetime"` Se nenhuma categoria for fornecida, todos os tipos serão retornados.|
|defaultLanguageCode |  Código de idioma do texto de entrada. Há suporte para os seguintes idiomas:`de, en, es, fr, it`|
|minimumPrecision | Não utilizado. Reservado para uso futuro. |
|includeTypelessEntities | Quando definido como true se o texto contiver uma entidade bem conhecida, mas não puder ser Categorizado em uma das categorias com suporte, ele será retornado como parte do `"entities"` campo de saída complexo. 
Essas são entidades que são bem conhecidas, mas não classificadas como parte das "categorias" com suporte atual. Por exemplo, "Windows 10" é uma entidade bem conhecida (um produto), mas "produtos" não estão nas categorias com suporte atualmente. O padrão é`false` |


## <a name="skill-inputs"></a>Entradas de habilidades

| Introduzir nome      | Descrição                   |
|---------------|-------------------------------|
| languageCode  | Opcional. A predefinição é `"en"`.  |
| text          | O texto a ser analisado.          |

## <a name="skill-outputs"></a>Saídas de habilidades

> [!NOTE]
> Nem todas as categorias de entidade têm suporte para todos os idiomas. Somente _en_, _es_ dão suporte à `"Quantity"`extração `"URL"`de `"Email"` tipos, `"Datetime"`,,.

| Nome da saída     | Descrição                   |
|---------------|-------------------------------|
| pessoas      | Uma matriz de cadeias de caracteres em que cada cadeia representa o nome de uma pessoa. |
| locations  | Uma matriz de cadeias de caracteres em que cada cadeia representa um local. |
| organizações  | Uma matriz de cadeias de caracteres em que cada cadeia representa uma organização. |
| volumes  | Uma matriz de cadeias de caracteres em que cada cadeia representa uma quantidade. |
| dateTimes  | Uma matriz de cadeias de caracteres em que cada cadeia representa um DateTime (como aparece no valor de texto). |
| URLs | Uma matriz de cadeias de caracteres em que cada cadeia representa uma URL |
| e-mails | Uma matriz de cadeias de caracteres em que cada cadeia representa um email |
| namedEntities | Uma matriz de tipos complexos que contém os seguintes campos: <ul><li>category</li> <li>valor (o nome real da entidade)</li><li>deslocamento (o local onde ele foi encontrado no texto)</li><li>confiança (não usada por enquanto. Será definido como um valor de-1)</li></ul> |
| Contabilidade | Uma matriz de tipos complexos que contém informações detalhadas sobre as entidades extraídas do texto, com os campos a seguir <ul><li> nome (o nome da entidade real. Isso representa um formulário "normalizado")</li><li> wikipédiaid</li><li>wikipediaLanguage</li><li>wikipediaUrl (um link para a página da Wikipédia da entidade)</li><li>bingId</li><li>tipo (a categoria da entidade reconhecida)</li><li>subtipo (disponível somente para determinadas categorias, isso fornece uma exibição mais granular do tipo de entidade)</li><li> corresponde (uma coleção complexa que contém)<ul><li>texto (o texto bruto para a entidade)</li><li>deslocamento (o local onde ele foi encontrado)</li><li>comprimento (o comprimento do texto bruto da entidade)</li></ul></li></ul> |

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
Se não houver suporte para o código de idioma do documento, um erro será retornado e nenhuma entidade será extraída.

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
