---
title: Blobs de índice que contêm vários documentos de índice de pesquisa do indexador de blob do Azure para pesquisa de texto completo-Azure Search
description: Rastreie BLOBs do Azure para conteúdo de texto usando o indexador de blob Azure Search. Cada blob pode conter um ou mais documentos de índice de Azure Search.
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: 2c2a17d006f65854a89b9fac1818fcec420c07dc
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182327"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Indexando BLOBs que produzem vários documentos de pesquisa
Por padrão, um indexador de blob tratará o conteúdo de um blob como um único documento de pesquisa. Determinados valores de **parsingMode** dão suporte a cenários em que um blob individual pode resultar em vários documentos de pesquisa. Os diferentes tipos de **parsingMode** que permitem que um indexador Extraia mais de um documento de pesquisa de um blob são:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Chave de documento de um para muitos
Cada documento que aparece em um índice de Azure Search é identificado exclusivamente por uma chave de documento. 

Quando nenhum modo de análise for especificado, e se não houver nenhum mapeamento explícito para o campo de chave no índice Azure Search o [](search-indexer-field-mappings.md) mapeará `metadata_storage_path` automaticamente a propriedade como a chave. Esse mapeamento garante que cada blob apareça como um documento de pesquisa distinto.

Ao usar qualquer um dos modos de análise listados acima, um blob é mapeado para "muitos" documentos de pesquisa, tornando uma chave de documento exclusivamente baseada em metadados de blob inadequados. Para superar essa restrição, Azure Search é capaz de gerar uma chave de documento "um para muitos" para cada entidade individual extraída de um blob. Essa propriedade é nomeada `AzureSearch_DocumentKey` e adicionada a cada entidade individual extraída do blob. É garantido que o valor dessa propriedade seja exclusivo para cada entidade individual _entre_ os BLOBs e as entidades aparecerão como documentos de pesquisa separados.

Por padrão, quando nenhum mapeamento de campo explícito para o campo de índice de chave é especificado `AzureSearch_DocumentKey` , o é mapeado para ele, `base64Encode` usando a função de mapeamento de campo.

## <a name="example"></a>Exemplo
Suponha que você tenha uma definição de índice com os seguintes campos:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E seu contêiner de BLOBs tem BLOBs com a seguinte estrutura:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Quando você cria um indexador e define o **parsingMode** como `jsonLines` -sem especificar nenhum mapeamento de campo explícito para o campo de chave, o mapeamento a seguir será aplicado implicitamente
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Essa configuração fará com que o índice de Azure Search que contém as seguintes informações (ID codificada em base64 diminuiu para fins de brevidade)

| id | temperatura | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapeamento de campo personalizado para campo de chave de índice

Supondo que a mesma definição de índice do exemplo anterior, digamos que o contêiner de blob tenha BLOBs com a seguinte estrutura:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Quando você cria um indexador com `delimitedText` **parsingMode**, pode parecer natural configurar uma função de mapeamento de campo para o campo de chave da seguinte maneira:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

No entanto, esse mapeamento _não_ resultará em quatro documentos exibidos no índice, pois o `recordid` campo não é exclusivo _entre BLOBs_. Portanto, é recomendável que você use o mapeamento de campo implícito aplicado da `AzureSearch_DocumentKey` Propriedade ao campo de índice de chave para os modos de análise "um para muitos".

Se você quiser configurar um mapeamento de campo explícito, certifique _-_ se de que SourceField seja distinto para cada entidade individual **em todos os BLOBs**.

> [!NOTE]
> A abordagem usada pelo `AzureSearch_DocumentKey` de assegurar a exclusividade por entidade extraída está sujeita a alterações e, portanto, você não deve depender de seu valor para as necessidades do seu aplicativo.

## <a name="see-also"></a>Consulte também

+ [Indexadores no Azure Search](search-indexer-overview.md)
+ [Indexando o armazenamento de BLOBs do Azure com o Azure Search](search-howto-index-json-blobs.md)
+ [Indexando BLOBs CSV com o indexador de blob Azure Search](search-howto-index-csv-blobs.md)
+ [Indexando BLOBs JSON com o indexador de blob Azure Search](search-howto-index-json-blobs.md)

## <a name="NextSteps"></a>Passos seguintes
* Para saber mais sobre Azure Search, consulte a [página de serviço Search](https://azure.microsoft.com/services/search/).