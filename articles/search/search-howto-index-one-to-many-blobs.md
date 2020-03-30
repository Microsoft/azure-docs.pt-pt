---
title: Bolhas de índice contendo vários documentos
titleSuffix: Azure Cognitive Search
description: Crawl Azure blobs para conteúdo de texto usando o indexante De pesquisa congitive Azure, onde cada blob pode produzir um ou mais documentos de índice de pesquisa.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112276"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Bolhas de indexação para produzir múltiplos documentos de pesquisa
Por padrão, um indexante blob tratará o conteúdo de uma bolha como um único documento de pesquisa. Certos **valores de parsingMode** suportam cenários em que uma bolha individual pode resultar em vários documentos de pesquisa. Os diferentes tipos de **parsingMode** que permitem a um indexante extrair mais do que um documento de pesquisa de uma bolha são:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Chave de documento sinuoso
Cada documento que aparece num índice de Pesquisa Cognitiva Azure é identificado de forma única por uma chave de documentos. 

Quando nenhum modo de análise é especificado, e se não houver mapeamento explícito para o `metadata_storage_path` campo chave no índice Azure Cognitive Search [automaticamente mapeia](search-indexer-field-mappings.md) a propriedade como a chave. Este mapeamento garante que cada bolha aparece como um documento de pesquisa distinto.

Ao utilizar qualquer um dos modos de análise acima listados, um blob mapeia para "muitos" documentos de pesquisa, tornando uma chave de documento apenas com base em metadados blob inadequados. Para ultrapassar esta restrição, a Azure Cognitive Search é capaz de gerar uma chave de documento "um a muitos" para cada entidade extraída de uma bolha. Esta propriedade `AzureSearch_DocumentKey` é nomeada e é adicionada a cada entidade extraída da bolha. O valor deste imóvel é garantido ser único para cada entidade individual através de _blobs_ e as entidades aparecerão como documentos de pesquisa separados.

Por padrão, quando não forem especificados mapeamentos `AzureSearch_DocumentKey` explícitos de campo para `base64Encode` o campo de índice chave, o é mapeado para o mesmo, utilizando a função de mapeamento de campo.

## <a name="example"></a>Exemplo
Assuma que tem uma definição de índice com os seguintes campos:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E o seu recipiente de bolhas tem bolhas com a seguinte estrutura:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Quando criar um indexador e definir `jsonLines` o **parsingMode** para - sem especificar quaisquer mapeamentos explícitos de campo para o campo chave, o seguinte mapeamento será aplicado implicitamente
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Esta configuração resultará no índice de pesquisa cognitiva Azure contendo as seguintes informações (id codificado base64 encurtado para brevidade)

| ID | temperatura | pressure | carimbo de data/hora |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13t00:00:00 |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14t00:00:00 |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12t00:00:00 |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11t00:00:00 |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapeamento de campo personalizado para campo chave de índice

Assumindo a mesma definição de índice que o exemplo anterior, digamos que o seu recipiente de bolhas tem bolhas com a seguinte estrutura:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Quando se cria um `delimitedText` indexante com **parsingMode,** pode parecer natural configurar uma função de mapeamento de campo para o campo-chave da seguinte forma:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

No entanto, este mapeamento _não_ resultará em `recordid` 4 documentos que aparecem no índice, porque o campo não é único _em bolhas._ Por isso, recomendamos que faça uso do `AzureSearch_DocumentKey` mapeamento implícito de campo aplicado da propriedade para o campo de índice chave para modos de análise "um a muitos".

Se quiser configurar um mapeamento de campo explícito, certifique-se de que o _Campo de origem_ é distinto para cada entidade individual em todas as **bolhas**.

> [!NOTE]
> A abordagem `AzureSearch_DocumentKey` utilizada para garantir a singularidade por entidade extraída está sujeita a alterações e, por isso, não deve confiar no seu valor para as necessidades da sua aplicação.

## <a name="next-steps"></a>Passos seguintes

Se ainda não está familiarizado com a estrutura básica e fluxo de trabalho da indexação de blob, deve rever primeiro o [Indexing Azure Blob Storage com a Azure Cognitive Search.](search-howto-index-json-blobs.md) Para obter mais informações sobre modos de análise para diferentes tipos de conteúdo blob, reveja os seguintes artigos.

> [!div class="nextstepaction"]
> [Indexação cSV blobs](search-howto-index-csv-blobs.md)
> [Indexing JSON blobs](search-howto-index-json-blobs.md)
