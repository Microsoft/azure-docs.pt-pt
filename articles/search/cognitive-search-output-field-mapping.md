---
title: Entrada de mapa para campos de saída
titleSuffix: Azure Cognitive Search
description: Extrair e enriquecer campos de dados de origem, e mapear para campos de produção num índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 58bb87d5af785d3cffd96f3bd02477f97ed967a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935369"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Como mapear campos enriquecidos com IA para um índice pesmável

![Estágios indexantes](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "estágios indexantes")

Neste artigo, você aprende a mapear campos de entrada enriquecidos para campos de produção em um índice pesmável. Uma vez [definido um skillset,](cognitive-search-defining-skillset.md)deve mapear os campos de saída de qualquer habilidade que contribua diretamente para um determinado campo no seu índice de pesquisa.

Os Mapeamentos de Campo de Saída são necessários para mover o conteúdo de documentos enriquecidos para o índice.  O documento enriquecido é realmente uma árvore de informação, e mesmo que exista suporte para tipos complexos no índice, por vezes você pode querer transformar a informação da árvore enriquecida em um tipo mais simples (por exemplo, uma variedade de cordas). Os mapeamentos de campo de saída permitem-lhe realizar transformações de formas de dados achatando informações. Os mapeamentos de campo de saída ocorrem sempre após a execução do skillset, embora seja possível que esta fase seja executada mesmo que não seja definida nenhuma habilidade.

Exemplos de mapeamentos de campo de saída:

* Como parte da sua habilidade, extraiu os nomes das organizações mencionadas em cada uma das páginas do seu documento. Agora quer mapear cada um desses nomes de organização num campo no seu índice de tipo Edm.Collection (Edm.String).

* Como parte da sua habilidade, produziu um novo nó chamado "document/translated_text". Você gostaria de mapear a informação deste nó para um campo específico no seu índice.

* Não tens uma habilidade, mas estás a indexar um tipo complexo a partir de uma base de dados do Cosmos DB. Você gostaria de chegar a um nó nesse tipo complexo e mapeá-lo em um campo no seu índice.

> [!NOTE]
> Recentemente, ativamos a funcionalidade de funções de mapeamento em mapeamentos de campo de saída. Para obter mais detalhes sobre as funções de mapeamento, consulte [as funções de mapeamento de campo](./search-indexer-field-mappings.md#field-mapping-functions)

## <a name="use-outputfieldmappings"></a>Utilizar outputFieldMappings

Para mapear os campos, adicione `outputFieldMappings` à definição do indexante como mostrado abaixo:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
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
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
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

Para cada mapeamento de campo de saída, desaponte a localização dos dados na árvore de documento enriquecida (sourceFieldName), e o nome do campo como referenciado no índice (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Achatamento de informações de tipos complexos 

O caminho numa fonteFieldName pode representar um elemento ou vários elementos. No exemplo acima, ```/document/content/sentiment``` representa um único valor numérico, enquanto representa ```/document/content/organizations/*/description``` várias descrições da organização. 

Nos casos em que existem vários elementos, são "achatados" numa matriz que contém cada um dos elementos. 

Mais concretamente, por ```/document/content/organizations/*/description``` exemplo, os dados no campo de *descrições* seriam como uma matriz plana de descrições antes de ser indexado:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Trata-se de um princípio importante, pelo que daremos outro exemplo. Imagine que tem uma variedade de tipos complexos como parte da árvore do enriquecimento. Digamos que há um membro chamado customEntities que tem uma variedade de tipos complexos como o descrito abaixo.

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

Vamos assumir que o seu índice tem um campo chamado 'doenças' do tipo Collection (Edm.String), onde gostaria de armazenar cada um dos nomes das entidades. 

Isto pode ser feito facilmente usando o símbolo " \* " " da seguinte forma:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Esta operação irá simplesmente "achatar" cada um dos nomes dos elementos personalizados numa única variedade de cordas como esta:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Passos seguintes
Uma vez mapeado os seus campos enriquecidos para campos pescandáveis, pode definir os atributos de campo para cada um dos campos pescandáveis [como parte da definição de índice](search-what-is-an-index.md).

Para obter mais informações sobre o mapeamento de campo, consulte [mapeamentos de campo em indexadores de Pesquisa Cognitiva Azure](search-indexer-field-mappings.md).