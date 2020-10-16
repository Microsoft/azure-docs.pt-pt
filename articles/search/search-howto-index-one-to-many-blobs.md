---
title: Bolhas de índice que contêm vários documentos
titleSuffix: Azure Cognitive Search
description: Crawl Azure blobs para conteúdo de texto usando o indexante Azure Cognitive Search Blob, onde cada blob pode produzir um ou mais documentos de índice de pesquisa.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: e5a69525c4bd0717c0561bc61ee3c52aa68e1c9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533966"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexação de bolhas para produzir vários documentos de pesquisa
Por predefinição, um indexante blob tratará o conteúdo de uma bolha como um único documento de pesquisa. Certos **valores de parsingMode** suportam cenários onde uma bolha individual pode resultar em vários documentos de pesquisa. Os diferentes tipos de **parsingMode** que permitem a um indexante extrair mais de um documento de pesquisa de uma bolha são:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Chave de documentos de um para muitos
Cada documento que aparece num índice de Pesquisa Cognitiva Azure é identificado exclusivamente por uma chave de documento. 

Quando não é especificado nenhum modo de análise, e se não houver um mapeamento explícito para o campo chave no índice Azure Cognitive Search [mapeia](search-indexer-field-mappings.md) automaticamente a `metadata_storage_path` propriedade como a chave. Este mapeamento garante que cada bolha aparece como um documento de pesquisa distinto.

Ao utilizar qualquer um dos modos de análise listados acima, um blob mapeia para "muitos" documentos de pesquisa, tornando uma chave de documento apenas baseada em metadados blob inadequados. Para ultrapassar este constrangimento, a Azure Cognitive Search é capaz de gerar uma chave de documento "um a muitos" para cada entidade extraída de uma bolha. Esta propriedade é nomeada `AzureSearch_DocumentKey` e é adicionada a cada entidade extraída da bolha. O valor deste imóvel é garantido ser único para cada entidade individual _através de blobs_ e as entidades aparecerão como documentos de pesquisa separados.

Por predefinição, quando não são especificados mapeamentos de campo explícitos para o campo de índice de chave, o `AzureSearch_DocumentKey` mapa é mapeado para ele, utilizando a `base64Encode` função de mapeamento de campo.

## <a name="example"></a>Exemplo
Assuma que tem uma definição de índice com os seguintes campos:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E o seu recipiente blob tem bolhas com a seguinte estrutura:

_Blob1.js_

```json
    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }
```

_Blob2.js_

```json
    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }
```

Quando criar um indexador e definir o **ParsingMode** `jsonLines` para - sem especificar quaisquer mapeamentos de campo explícitos para o campo chave, o seguinte mapeamento será aplicado implicitamente

```http
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }
```

Esta configuração resultará no índice de Pesquisa Cognitiva Azure contendo as seguintes informações (ID codificado base64 encurtado para brevidade)

| ID | temperatura | pressão | carimbo de data/hora |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapeamento de campo personalizado para campo de chave de índice

Assumindo a mesma definição de índice que o exemplo anterior, diga que o seu recipiente blob tem bolhas com a seguinte estrutura:

_Blob1.js_

```json
    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.js_

```json
    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 
```

Quando cria um indexador com `delimitedText` **parsingMode,** pode parecer natural configurar uma função de mapeamento de campo para o campo chave da seguinte forma:

```http
    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }
```

No entanto, este mapeamento _não_ resultará em 4 documentos que aparecem no índice, porque o `recordid` campo não é único entre _bolhas._ Por isso, recomendamos que faça uso do mapeamento de campo implícito aplicado da `AzureSearch_DocumentKey` propriedade para o campo de índice chave para modos de análise "um a muitos".

Se pretender configurar um mapeamento de campo explícito, certifique-se de que o _sourceField_ é distinto para cada entidade em **todas as bolhas**.

> [!NOTE]
> A abordagem utilizada para `AzureSearch_DocumentKey` garantir a singularidade por entidade extraída está sujeita a alterações e, por isso, não deve confiar no seu valor para as necessidades da sua aplicação.

## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a melhorar a pesquisa cognitiva do Azure
Se tiver pedidos de funcionalidades ou ideias para melhorias, forneça a sua entrada no [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Se precisar de ajuda para utilizar a função existente, publique a sua pergunta no [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="next-steps"></a>Passos seguintes

Se ainda não está familiarizado com a estrutura básica e o fluxo de trabalho da indexação de bolhas, deve rever o [Indexing Azure Blob Storage com Azure Cognitive Search](search-howto-index-json-blobs.md) em primeiro lugar. Para obter mais informações sobre modos de análise para diferentes tipos de conteúdo de bolhas, reveja os seguintes artigos.

> [!div class="nextstepaction"]
> [Indexação de bolhas](search-howto-index-csv-blobs.md) 
>  de CSV [Indexação de bolhas JSON](search-howto-index-json-blobs.md)
