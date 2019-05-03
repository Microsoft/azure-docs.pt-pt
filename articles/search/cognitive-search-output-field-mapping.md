---
title: Pesquisa cognitiva do mapa enriquecida campos de entrada para campos de saída - Azure Search
description: Extrair e enriquecer os campos de dados de origem para mapear para campos de saída num índice da Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 506acee6cd9cd3c50e10f1c45768230564eeaaf1
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65022080"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Como mapear campos plena para um índice pesquisável

Neste artigo, irá aprender a mapear campos de entrada plena para campos de saída num índice pesquisável. Assim que tiver [definido um conjunto de capacidades](cognitive-search-defining-skillset.md), tem de mapear os campos de saída de qualquer habilidade que contribui diretamente os valores para um determinado campo no seu índice de pesquisa. Mapeamentos de campo são necessários para mover conteúdo de documentos plena para o índice.


## <a name="use-outputfieldmappings"></a>Utilizar outputFieldMappings
Para mapear campos, adicionar `outputFieldMappings` para a definição de indexador, conforme mostrado abaixo:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
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
