---
title: Mapear entrada para campos de saída
titleSuffix: Azure Cognitive Search
description: Extrair e enriquecer campos de dados de origem e mapear para campos de saída em um índice de Pesquisa Cognitiva do Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280974"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Como mapear campos de ia-ricos para um índice pesquisável

Neste artigo, você aprenderá a mapear campos de entrada aprimorados para campos de saída em um índice pesquisável. Depois de [definir um qualificable](cognitive-search-defining-skillset.md), você deve mapear os campos de saída de qualquer habilidade que contribui diretamente com valores para um determinado campo em seu índice de pesquisa. 

Os mapeamentos de campo de saída são necessários para mover o conteúdo de documentos aprimorados para o índice.  O documento aprimorado é realmente uma árvore de informações e, embora haja suporte para tipos complexos no índice, às vezes você pode querer transformar as informações da árvore aprimorada em um tipo mais simples (por exemplo, uma matriz de cadeias de caracteres). Os mapeamentos de campo de saída permitem que você execute transformações de forma de dados por meio do nivelamento de informações.

## <a name="use-outputfieldmappings"></a>Usar outputFieldMappings
Para mapear campos, adicione `outputFieldMappings` à definição do indexador, conforme mostrado abaixo:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

O corpo da solicitação é estruturado da seguinte maneira:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

Para cada mapeamento de campo de saída, defina o local dos dados na árvore de documentos enriquecedo (sourceFieldName) e o nome do campo como referenciado no índice (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Nivelamento de informações de tipos complexos 

O caminho em um sourceFieldName pode representar um elemento ou vários elementos. No exemplo acima, ```/document/content/sentiment``` representa um único valor numérico, enquanto ```/document/content/organizations/*/description``` representa várias descrições da organização. 

Nos casos em que há vários elementos, eles são "achatados" em uma matriz que contém cada um dos elementos. 

Mais concretamente, para o exemplo de ```/document/content/organizations/*/description```, os dados no campo *descrições* pareceriam uma matriz simples de descrições antes de serem indexados:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Esse é um princípio importante, então forneceremos outro exemplo. Imagine que você tenha uma matriz de tipos complexos como parte da árvore de enriquecimento. Digamos que haja um membro chamado customEntities que tenha uma matriz de tipos complexos, como o descrito abaixo.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Vamos supor que o índice tenha um campo chamado ' doenças ' do tipo Collection (EDM. String), em que você gostaria de armazenar cada um dos nomes das entidades. 

Isso pode ser feito facilmente usando o símbolo "\*", da seguinte maneira:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Essa operação simplesmente "achata" cada um dos nomes dos elementos customEntities em uma única matriz de cadeias de caracteres como esta:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Passos seguintes
Depois de mapear seus campos aprimorados para campos pesquisáveis, você pode definir os atributos de campo para cada um dos campos pesquisáveis [como parte da definição do índice](search-what-is-an-index.md).

Para obter mais informações sobre mapeamento de campo, consulte [mapeamentos de campo no Azure pesquisa cognitiva indexadores](search-indexer-field-mappings.md).
