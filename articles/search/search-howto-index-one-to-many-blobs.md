---
title: Indexar um blob em muitos documentos de índice de pesquisa do indexador de blob do Azure para pesquisa de texto completo
titleSuffix: Azure Cognitive Search
description: Rastreie BLOBs do Azure para conteúdo de texto usando o indexador de blob do Azure cognitivos Search. Cada blob pode produzir um ou mais documentos de índice de pesquisa.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 127354e55a81e379825b41759f2b6150ba554a12
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818549"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexando BLOBs para produzir vários documentos de pesquisa
Por padrão, um indexador de blob tratará o conteúdo de um blob como um único documento de pesquisa. Determinados valores de **parsingMode** dão suporte a cenários em que um blob individual pode resultar em vários documentos de pesquisa. Os diferentes tipos de **parsingMode** que permitem que um indexador Extraia mais de um documento de pesquisa de um blob são:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Chave de documento de um para muitos
Cada documento que aparece em um índice de Pesquisa Cognitiva do Azure é identificado exclusivamente por uma chave de documento. 

Quando nenhum modo de análise for especificado, e se não houver mapeamento explícito para o campo de chave no índice, o Azure Pesquisa Cognitiva [mapeará](search-indexer-field-mappings.md) automaticamente a propriedade `metadata_storage_path` como a chave. Esse mapeamento garante que cada blob apareça como um documento de pesquisa distinto.

Ao usar qualquer um dos modos de análise listados acima, um blob é mapeado para "muitos" documentos de pesquisa, tornando uma chave de documento exclusivamente baseada em metadados de blob inadequados. Para superar essa restrição, o Azure Pesquisa Cognitiva é capaz de gerar uma chave de documento "um para muitos" para cada entidade individual extraída de um blob. Essa propriedade é denominada `AzureSearch_DocumentKey` e é adicionada a cada entidade individual extraída do blob. É garantido que o valor dessa propriedade seja exclusivo para cada entidade individual _entre os BLOBs_ e as entidades aparecerão como documentos de pesquisa separados.

Por padrão, quando nenhum mapeamento de campo explícito para o campo de índice de chave é especificado, o `AzureSearch_DocumentKey` é mapeado para ele, usando a função de mapeamento de campo `base64Encode`.

## <a name="example"></a>Exemplo
Suponha que você tenha uma definição de índice com os seguintes campos:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E seu contêiner de BLOBs tem BLOBs com a seguinte estrutura:

_Blob1. JSON_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2. JSON_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Quando você cria um indexador e define o **parsingMode** como `jsonLines`-sem especificar nenhum mapeamento de campo explícito para o campo de chave, o mapeamento a seguir será aplicado implicitamente
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Essa configuração resultará no índice de Pesquisa Cognitiva do Azure que contém as seguintes informações (ID codificada em base64 reduzida para fins de brevidade)

| ID | temperatura | pressure | carimbo de data/hora |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapeamento de campo personalizado para campo de chave de índice

Supondo que a mesma definição de índice do exemplo anterior, digamos que o contêiner de blob tenha BLOBs com a seguinte estrutura:

_Blob1. JSON_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2. JSON_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Quando você cria um indexador com `delimitedText` **parsingMode**, pode parecer natural configurar uma função de mapeamento de campo para o campo de chave da seguinte maneira:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

No entanto, esse mapeamento _não_ resultará em quatro documentos exibidos no índice, porque o campo `recordid` não é exclusivo _entre BLOBs_. Portanto, é recomendável que você use o mapeamento de campo implícito aplicado da propriedade `AzureSearch_DocumentKey` para o campo de índice de chave para os modos de análise "um-para-muitos".

Se você quiser configurar um mapeamento de campo explícito, certifique-se de que _SourceField_ seja distinto para cada entidade individual **em todos os BLOBs**.

> [!NOTE]
> A abordagem usada pelo `AzureSearch_DocumentKey` de garantir a exclusividade por entidade extraída está sujeita a alterações e, portanto, você não deve depender de seu valor para as necessidades do seu aplicativo.

## <a name="next-steps"></a>Passos seguintes

Se você ainda não estiver familiarizado com a estrutura básica e o fluxo de trabalho da indexação de BLOBs, examine a [indexação do armazenamento de BLOBs do Azure com o azure pesquisa cognitiva](search-howto-index-json-blobs.md) primeiro. Para obter mais informações sobre os modos de análise para diferentes tipos de conteúdo de BLOB, examine os artigos a seguir.

> [!div class="nextstepaction"]
> [Indexando BLOBs CSV](search-howto-index-csv-blobs.md)
> [indexando BLOBs JSON](search-howto-index-json-blobs.md)
