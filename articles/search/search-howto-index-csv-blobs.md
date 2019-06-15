---
title: Indexar blobs CSV com o indexador Blob do Azure Search - Azure Search
description: Pesquise blobs CSV no armazenamento de Blobs do Azure para pesquisa em texto completo com um índice da Azure Search. Indexadores automatizam ingestão de dados para origens de dados selecionadas, como o armazenamento de Blobs do Azure.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: e7d959e77d27fb04b18f402e4056d4dea1607039
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522903"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexar blobs CSV com o indexador de Blobs do Azure Search

> [!Note]
> delimitedText modo de análise está em pré-visualização e não se utilização em produção. O [2019 no versão REST API-05-06-Preview](search-api-preview.md) fornece esta funcionalidade. Não existe nenhum suporte de .NET SDK neste momento.
>

Por predefinição, [indexador de Blobs do Azure Search](search-howto-indexing-azure-blob-storage.md) analisa delimitados por blobs de texto como um único segmento de texto. No entanto, com blobs que contém dados do CSV, muitas vezes, pretende processar cada linha no blob como um documento separado. Por exemplo, tendo em conta o seguinte texto delimitado, poderá analisá-lo em dois documentos, cada um contendo "id", "datePublished" e "etiquetas" campos: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Neste artigo, ficará a saber como analisar CSV blobs com uma definição de indexerby de Blobs do Azure Search a `delimitedText` modo de análise. 

> [!NOTE]
> Siga as recomendações de configuração do indexador nas [um-para-muitos indexação](search-howto-index-one-to-many-blobs.md) para vários documentos de pesquisa de um blob do Azure de saída.

## <a name="setting-up-csv-indexing"></a>Configuração de CSV de indexação
Indexar CSV blobs, criar ou atualizar uma definição de indexador com o `delimitedText` modo de análise num [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer) pedido:

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` indica que a primeira linha (não vazia) de cada blob contém os cabeçalhos.
Se os blobs não contêm uma linha de cabeçalho inicial, os cabeçalhos devem ser especificados na configuração do indexador: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Pode personalizar o caráter delimitador a utilizar o `delimitedTextDelimiter` definição de configuração. Por exemplo:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Atualmente, é suportada apenas a codificação UTF-8. Se precisar de suporte para outras codificações, vote em [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando utiliza o modo de análise de texto delimitado, Azure Search parte do princípio de que todos os blobs na sua origem de dados será CSV. Se precisar de suportar uma mistura de blobs CSV e não CSV na mesma origem de dados, vote em [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exemplos de pedido
Colocar isso todos juntos, aqui estão os exemplos de carga completa. 

Origem de dados: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexador de:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Ajude-na melhorar o Azure Search
Se tiver de pedidos de funcionalidades ou ideias para melhorias, fornecer seus comentários sobre [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

