---
title: Procure por bolhas de texto simples
titleSuffix: Azure Cognitive Search
description: Configure um indexante de pesquisa para extrair texto simples das bolhas de Azure para pesquisa completa de texto na Pesquisa Cognitiva Azure.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 417bdacc3ce8b619d5ec9618e6060ac071882471
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533930"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Como indexar bolhas de texto simples na Pesquisa Cognitiva Azure

Ao utilizar um [indexante blob](search-howto-indexing-azure-blob-storage.md) para extrair texto pesmável para pesquisa completa de texto, pode invocar vários modos de análise para obter melhores resultados de indexação. Por predefinição, o indexante analisa as bolhas de texto delimitadas como um único pedaço de texto. No entanto, se todas as suas bolhas contiverem texto simples na mesma codificação, pode melhorar significativamente o desempenho da indexação utilizando o **modo de análise de texto**.

## <a name="set-up-plain-text-indexing"></a>Configurar a indexação de texto simples

Para indexar bolhas de texto simples, crie ou atualize uma definição de indexante com a `parsingMode` propriedade de configuração para `text` um pedido de Criar [Indexer:](/rest/api/searchservice/create-indexer)

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

Por predefinição, a `UTF-8` codificação é assumida. Para especificar uma codificação diferente, utilize a `encoding` propriedade de configuração: 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>Exemplo de pedido

Os modos de análise são especificados na definição do indexante.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a melhorar a pesquisa cognitiva do Azure

Se tiver pedidos de funcionalidades ou ideias para melhorias, forneça a sua entrada no [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Se precisar de ajuda para utilizar a função existente, publique a sua pergunta no [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="next-steps"></a>Passos seguintes

* [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
* [Como configurar um indexador de bolhas](search-howto-indexing-azure-blob-storage.md)
* [Visão geral da indexação da bolha](search-blob-storage-integration.md)