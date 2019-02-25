---
title: Pesquisa cognitiva do mapa enriquecida campos de entrada para campos de saída - Azure Search
description: Extrair e enriquecer os campos de dados de origem para mapear para campos de saída num índice da Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: dd62119b01465392a92c7e68231fed8027b04da2
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750536"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Como mapear campos plena para um índice pesquisável

Neste artigo, irá aprender a mapear campos de entrada plena para campos de saída num índice pesquisável. Assim que tiver [definido um conjunto de capacidades](cognitive-search-defining-skillset.md), tem de mapear os campos de saída de qualquer habilidade que contribui diretamente os valores para um determinado campo no seu índice de pesquisa. Mapeamentos de campo são necessários para mover conteúdo de documentos plena para o índice.


## <a name="use-outputfieldmappings"></a>Utilizar outputFieldMappings
Para mapear campos, adicionar `outputFieldMappings` para a definição de indexador, conforme mostrado abaixo:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

O corpo do pedido é estruturado, da seguinte forma:

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
Para cada mapeamento de campo de saída, defina o nome do campo plena (sourceFieldName) e o nome do campo conforme referenciado no índice (targetFieldName).

O caminho num sourceFieldName pode representar um elemento ou em vários elementos. No exemplo acima, ```/document/content/sentiment``` representa um único valor numérico, enquanto ```/document/content/organizations/*/description``` representa vários descrições de organização. Em casos em que existem vários elementos, eles são "nivelados" uma matriz que contém cada um dos elementos. Mais concretamente, para o ```/document/content/organizations/*/description``` exemplo, os dados no *descrições* campo pareceria com uma matriz simples de descrições de antes de serem indexado:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Passos Seguintes
Depois de ter mapeado os seus campos plena aos campos pesquisáveis, pode definir os atributos de campo para cada um dos campos pesquisáveis [como parte da definição do índice](search-what-is-an-index.md).

Para obter mais informações sobre o mapeamento de campos, consulte [mapeamentos de campo em indexadores do Azure Search](search-indexer-field-mappings.md).
