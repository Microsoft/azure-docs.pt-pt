---
title: Procurar nos blobs de índice que contém vários documentos de índice do indexador de Blobs do Azure para pesquisa em texto completo - Azure Search
description: Pesquise blobs do Azure para o conteúdo de texto com o indexador Blob do Azure Search. Cada blob pode conter um ou mais documentos de índice da Azure Search.
ms.date: 02/12/2019
author: arv100kri
manager: briansmi
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: e95eff015340659b642dff800a03f615e22c1577
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57220572"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Indexar blobs produzir vários documentos de pesquisa
Por predefinição, um indexador blob irão tratar o conteúdo de um blob como um documento única pesquisa. Determinadas **parsingMode** valores oferecer suporte a cenários em que um blob individual pode resultar em vários documentos de pesquisa. Os diferentes tipos de **parsingMode** que permitem que um indexador para extrair mais do que um pesquisar no documento de um blob é:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

> [!IMPORTANT]
> `jsonLines` modo de análise está em pré-visualização pública e não deve ser usado em ambientes de produção. Para obter mais informações, consulte [REST api-version = 2017-11-11-pré-visualização](search-api-2017-11-11-preview.md). 

## <a name="one-to-many-document-key"></a>Chave do documento de um-para-muitos
Cada documento que aparece num índice da Azure Search é identificado exclusivamente por uma chave de documento. 

Quando não é especificado nenhum modo de análise e se não houver nenhum explícito de mapeamento para o campo de chave no índice do Azure Search automaticamente [mapeia](search-indexer-field-mappings.md) o `metadata_storage_path` propriedade como a chave. Este mapeamento garante que cada blob é apresentado como um documento de pesquisa distintos.

Ao utilizar qualquer um dos modos de análise listados acima, um blob é mapeado para "vários" de pesquisa de documentos, fazendo uma chave do documento exclusivamente com base nos metadados do blob não são adequados. Para superar essa restrição, o Azure Search é capaz de gerar uma chave de documento de "um-para-muitos" para cada entidade individual, extraída de um blob. Esta propriedade é denominada `AzureSearch_DocumentKey` e é adicionado a cada entidade individual extraída a partir do blob. O valor desta propriedade é a garantia de ser exclusivo para cada entidade individual _em blobs_ e as entidades aparecerão como documentos de pesquisa separadas.

Por predefinição, quando não existem mapeamentos de campo explícita para o campo de índice de chaves são especificados, o `AzureSearch_DocumentKey` é mapeado para ele, usando o `base64Encode` função de mapeamento de campos.

## <a name="example"></a>Exemplo
Suponha que tem uma definição de índice com os seguintes campos:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E o contentor de BLOBs tem blobs com a seguinte estrutura:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Quando cria um indexador e defina a **parsingMode** para `jsonLines` - sem especificar qualquer campo explícito mapeamentos para o campo de chave, o seguinte mapeamento serão aplicados implicitamente
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Esta configuração irá resultar no índice da Azure Search que contém as seguintes informações (id de codificada em base64 baixou para fins de brevidade)

| ID | temperatura | pressure | carimbo de data/hora |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapeamento de campo personalizado para o campo de chave de índice

Supondo que a mesma definição de índice do exemplo anterior, digamos que o contentor de BLOBs tem blobs com a seguinte estrutura:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Quando cria um indexador com `delimitedText` **parsingMode**, pareça natural para configurar uma função de mapeamento de campos para o campo de chave da seguinte forma:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

No entanto, esse mapeamento será _não_ resultar em documentos de 4 a aparecer no índice, porque a `recordid` campo não é exclusivo _em blobs_. Por este motivo, recomendamos que faça utilizar do mapeamento de campo implícito aplicado a partir do `AzureSearch_DocumentKey` propriedade para o campo de índice de chaves para os modos de análise de "um-para-muitos".

Se pretender configurar um mapeamento de campo explícita, certifique-se de que o _sourceField_ é distinta para cada entidade individual **em todos os blobs**.

> [!NOTE]
> A abordagem usada pelo `AzureSearch_DocumentKey` de garantir a exclusividade por entidade extraída está sujeitas a alterações e, portanto, não deverá confiar no seu valor para as necessidades da sua aplicação.

## <a name="see-also"></a>Consulte também

+ [Indexadores na Azure Search](search-indexer-overview.md)
+ [Indexação do armazenamento de Blobs do Azure com o Azure Search](search-howto-index-json-blobs.md)
+ [Indexar blobs CSV com o indexador de Blobs do Azure Search](search-howto-index-csv-blobs.md)
+ [Indexar blobs JSON com o indexador de Blobs do Azure Search](search-howto-index-csv-blobs.md)

## <a name="NextSteps"></a>Passos seguintes
* Para saber mais sobre o Azure Search, consulte a [página do serviço de pesquisa](https://azure.microsoft.com/services/search/).