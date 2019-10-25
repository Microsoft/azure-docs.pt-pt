---
title: Mapear campos de entrada aprimorados do ia para campos de saída
titleSuffix: Azure Cognitive Search
description: Extrair e enriquecer campos de dados de origem e mapear para campos de saída em um índice de Pesquisa Cognitiva do Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 77f378f4e0c4a8e4827523e244f7b18c2a9ba336
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792113"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Como mapear campos de ia-ricos para um índice pesquisável

Neste artigo, você aprenderá a mapear campos de entrada aprimorados para campos de saída em um índice pesquisável. Depois de [definir um qualificable](cognitive-search-defining-skillset.md), você deve mapear os campos de saída de qualquer habilidade que contribui diretamente com valores para um determinado campo em seu índice de pesquisa. Os mapeamentos de campo são necessários para mover o conteúdo de documentos aprimorados para o índice.


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
Para cada mapeamento de campo de saída, defina o nome do campo enriquecedo (sourceFieldName) e o nome do campo como referenciado no índice (targetFieldName).

O caminho em um sourceFieldName pode representar um elemento ou vários elementos. No exemplo acima, ```/document/content/sentiment``` representa um único valor numérico, enquanto ```/document/content/organizations/*/description``` representa várias descrições da organização. Nos casos em que há vários elementos, eles são "achatados" em uma matriz que contém cada um dos elementos. Mais concretamente, para o exemplo de ```/document/content/organizations/*/description```, os dados no campo *descrições* pareceriam uma matriz simples de descrições antes de serem indexados:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Passos seguintes
Depois de mapear seus campos aprimorados para campos pesquisáveis, você pode definir os atributos de campo para cada um dos campos pesquisáveis [como parte da definição do índice](search-what-is-an-index.md).

Para obter mais informações sobre mapeamento de campo, consulte [mapeamentos de campo no Azure pesquisa cognitiva indexadores](search-indexer-field-mappings.md).
