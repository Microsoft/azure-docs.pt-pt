---
title: Mapear os campos de entrada aprimorados da pesquisa cognitiva para campos de saída-Azure Search
description: Extrair e enriquecer campos de dados de origem e mapear para campos de saída em um índice de Azure Search.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 0e422d2453fe12280da9e9b0b5dc7aa391f97b9f
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186398"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Como mapear campos aprimorados para um índice pesquisável

Neste artigo, você aprenderá a mapear campos de entrada aprimorados para campos de saída em um índice pesquisável. Depois de [definir um qualificable](cognitive-search-defining-skillset.md), você deve mapear os campos de saída de qualquer habilidade que contribui diretamente com valores para um determinado campo em seu índice de pesquisa. Os mapeamentos de campo são necessários para mover o conteúdo de documentos aprimorados para o índice.


## <a name="use-outputfieldmappings"></a>Usar outputFieldMappings
Para mapear campos, `outputFieldMappings` adicione à definição do indexador, conforme mostrado abaixo:

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

O caminho em um sourceFieldName pode representar um elemento ou vários elementos. No exemplo acima, ```/document/content/sentiment``` representa um único valor numérico, enquanto ```/document/content/organizations/*/description``` representa várias descrições da organização. Nos casos em que há vários elementos, eles são "achatados" em uma matriz que contém cada um dos elementos. Mais concretamente, por ```/document/content/organizations/*/description``` exemplo, os dados no campo *descrições* pareceriam uma matriz simples de descrições antes de serem indexados:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Passos Seguintes
Depois de mapear seus campos aprimorados para campos pesquisáveis, você pode definir os atributos de campo para cada um dos campos pesquisáveis [como parte da definição do índice](search-what-is-an-index.md).

Para obter mais informações sobre mapeamento de campo, consulte [mapeamentos de campo em indexadores de Azure Search](search-indexer-field-mappings.md).
