---
title: Entrada de mapa para campos de saída
titleSuffix: Azure Cognitive Search
description: Extrair e enriquecer os campos de dados de origem, e mapear para campos de saída num índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74280974"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Como mapear campos enriquecidos de IA para um índice pesquisável

Neste artigo, aprende-se a mapear campos de entrada enriquecidos para campos de saída num índice pesquisável. Depois de [definir um skillset,](cognitive-search-defining-skillset.md)deve mapear os campos de saída de qualquer habilidade que contribua diretamente com valores para um determinado campo no seu índice de pesquisa. 

Os mapeamentos do campo de saída são necessários para mover conteúdo de documentos enriquecidos para o índice.  O documento enriquecido é realmente uma árvore de informação, e mesmo que exista suporte para tipos complexos no índice, por vezes pode querer transformar a informação da árvore enriquecida num tipo mais simples (por exemplo, uma variedade de cordas). Os mapeamentos de campo de saída permitem-lhe realizar transformações de forma de dados achatando informações.

## <a name="use-outputfieldmappings"></a>Utilizar os outputFieldMappings
Para mapear campos, adicione `outputFieldMappings` à sua definição de indexante como mostrado abaixo:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

O corpo do pedido é estruturado da seguinte forma:

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

Para cada mapeamento de campo de saída, detete a localização dos dados na árvore de documentoenriquecida (sourceFieldName), e o nome do campo conforme referenciado no índice (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Informação de achatamento de tipos complexos 

O caminho num FieldName de origem pode representar um elemento ou vários elementos. No exemplo acima, ```/document/content/sentiment``` representa um único valor ```/document/content/organizations/*/description``` numérico, enquanto representa várias descrições da organização. 

Nos casos em que existem vários elementos, são "achatados" numa matriz que contém cada um dos elementos. 

De forma mais ```/document/content/organizations/*/description``` concreta, por exemplo, os dados no campo de *descrições* pareceriam uma série plana antes de ser indexado:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Trata-se de um princípio importante, pelo que daremos outro exemplo. Imagine que tem uma série de tipos complexos como parte da árvore de enriquecimento. Digamos que há um membro chamado CustomEntities que tem uma variedade de tipos complexos como o descrito abaixo.

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

Vamos supor que o seu índice tem um campo chamado 'doenças' do tipo Collection (Edm.String), onde gostaria de armazenar cada um dos nomes das entidades. 

Isto pode ser feito facilmente usando o símbolo "\*" " como se segue:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Esta operação irá simplesmente "achatar" cada um dos nomes dos elementos personalizados Entidades numa única gama de cordas como esta:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Passos seguintes
Depois de mapear os seus campos enriquecidos para campos pesquisáveis, pode definir os atributos de campo para cada um dos campos pesquisáveis [como parte da definição de índice](search-what-is-an-index.md).

Para obter mais informações sobre mapeamento de campo, consulte [mapeamentos de campo em indexadores](search-indexer-field-mappings.md)de pesquisa cognitiva Azure .
