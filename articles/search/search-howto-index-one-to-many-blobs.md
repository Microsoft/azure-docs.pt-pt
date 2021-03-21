---
title: Bolhas de índice que contêm vários documentos
titleSuffix: Azure Cognitive Search
description: Crawl Azure blobs para conteúdo de texto usando o indexante Azure Cognitive Search Blob, onde cada blob pode produzir um ou mais documentos de índice de pesquisa.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ea22b3cff8a0303c4e6698db4090df0f5ed2153a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99430985"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexação de bolhas para produzir vários documentos de pesquisa

Por predefinição, um indexante blob tratará o conteúdo de uma bolha como um único documento de pesquisa. Se quiser uma representação mais granular da bolha num índice de pesquisa, pode definir os **valores de ParsingMode** para criar vários documentos de pesquisa a partir de uma bolha. Os **valores de parsingMode** que resultam em muitos documentos de pesquisa incluem `delimitedText` (para [CSV),](search-howto-index-csv-blobs.md)e `jsonArray` `jsonLines` (para [JSON).](search-howto-index-json-blobs.md)

Quando utiliza qualquer um destes modos de análise, os novos documentos de pesquisa que emergem devem ter chaves de documento únicas, e surge um problema na determinação de onde esse valor vem. A bolha-mãe tem pelo menos um valor único na forma `metadata_storage_path property` de, mas se contribui nesse valor para mais de um documento de pesquisa, a chave já não é única no índice.

Para resolver este problema, o indexante blob gera um `AzureSearch_DocumentKey` que identifica de forma única cada documento de pesquisa infantil criado a partir do pai da bolha única. Este artigo explica como funciona esta funcionalidade.

## <a name="one-to-many-document-key"></a>Chave de documentos de um para muitos

Cada documento que aparece num índice de Pesquisa Cognitiva Azure é identificado exclusivamente por uma chave de documento. 

Quando não é especificado o modo de análise e se não houver [um mapeamento de campo explícito](search-indexer-field-mappings.md) na definição do indexante para a tecla do documento de pesquisa, o indexante blob mapeia automaticamente como chave do `metadata_storage_path property` documento. Este mapeamento garante que cada bolha aparece como um documento de pesquisa distinto, e poupa-lhe o passo de ter que criar este campo mapeando-se (normalmente, apenas campos com nomes e tipos idênticos são automaticamente mapeados).

Ao utilizar qualquer um dos modos de análise listados acima, um blob mapeia para "muitos" documentos de pesquisa, tornando uma chave de documento apenas baseada em metadados blob inadequados. Para ultrapassar este constrangimento, a Azure Cognitive Search é capaz de gerar uma chave de documento "um a muitos" para cada entidade extraída de uma bolha. Esta propriedade é nomeada AzureSearch_DocumentKey e é adicionada a cada entidade extraída da bolha. O valor deste imóvel é garantido ser único para cada entidade individual através de blobs e as entidades aparecerão como documentos de pesquisa separados.

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
{ "temperature": 100, "pressure": 100, "timestamp": "2020-02-13T00:00:00Z" }
{ "temperature" : 33, "pressure" : 30, "timestamp": "2020-02-14T00:00:00Z" }
```

_Blob2.js_

```json
{ "temperature": 1, "pressure": 1, "timestamp": "2019-01-12T00:00:00Z" }
{ "temperature" : 120, "pressure" : 3, "timestamp": "2017-05-11T00:00:00Z" }
```

Quando criar um indexador e definir o **ParsingMode** `jsonLines` para - sem especificar quaisquer mapeamentos de campo explícitos para o campo chave, o seguinte mapeamento será aplicado implicitamente.

```http
{
    "sourceFieldName" : "AzureSearch_DocumentKey",
    "targetFieldName": "id",
    "mappingFunction": { "name" : "base64Encode" }
}
```

Esta configuração resultará em teclas de documento desambiguadas, semelhantes à seguinte ilustração (ID codificado de base64 encurtado para brevidade).

| ID | temperatura | pressão | carimbo de data/hora |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2020-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2020-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2019-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2017-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapeamento de campo personalizado para campo de chave de índice

Assumindo a mesma definição de índice que o exemplo anterior, suponha que o seu recipiente blob tenha bolhas com a seguinte estrutura:

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

## <a name="next-steps"></a>Passos seguintes

Se ainda não está familiarizado com a estrutura básica e o fluxo de trabalho da indexação de bolhas, deve rever o [Indexing Azure Blob Storage com Azure Cognitive Search](search-howto-index-json-blobs.md) em primeiro lugar. Para obter mais informações sobre modos de análise para diferentes tipos de conteúdo de bolhas, reveja os seguintes artigos.

> [!div class="nextstepaction"]
> [Indexação de bolhas](search-howto-index-csv-blobs.md) 
>  de CSV [Indexação de bolhas JSON](search-howto-index-json-blobs.md)
