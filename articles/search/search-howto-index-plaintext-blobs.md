---
title: Procure por bolhas de texto simples
titleSuffix: Azure Cognitive Search
description: Configure um indexante de pesquisa para extrair texto simples das bolhas de Azure para pesquisa completa de texto na Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 422346430e32ccb8745d5a5d829c5d61089a99c6
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430433"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Como indexar bolhas de texto simples na Pesquisa Cognitiva Azure

Ao utilizar um [indexante blob](search-howto-indexing-azure-blob-storage.md) para extrair texto pesmável para pesquisa completa de texto, pode invocar vários modos de análise para obter melhores resultados de indexação. Por predefinição, o indexante analisa o conteúdo blob como um único pedaço de texto. No entanto, se todas as bolhas contiverem texto simples na mesma codificação, pode melhorar significativamente o desempenho da indexação utilizando o `text` modo de análise.

Deve utilizar o `text` modo de análise quando:

+ O tipo de ficheiro é .txt
+ Os ficheiros são de qualquer tipo, mas o conteúdo em si é texto (por exemplo, código fonte de programa, HTML, XML, etc. Para ficheiros num idioma de marcação, quaisquer caracteres de sintaxe serão enviados como texto estático.

Lembre-se que os indexantes serializam para JSON. O conteúdo de todo o ficheiro de texto será indexado num campo único, uma vez `"content": "<file-contents>"` que . As novas instruções de linha e devolução são expressas como `\r\n\` .

Se quiser um resultado mais granular, considere as seguintes soluções:

+ [`delimitedText`](search-howto-index-csv-blobs.md) modo de análise, se a fonte for CSV
+ [ `jsonArray` ou `jsonLines` ](search-howto-index-json-blobs.md), se a fonte for JSON

Uma terceira opção para quebrar o conteúdo em várias partes requer funcionalidades avançadas sob a forma de [enriquecimento](cognitive-search-concept-intro.md)de IA . Adiciona análise que identifica e atribui pedaços do ficheiro a diferentes campos de pesquisa. Pode encontrar uma solução completa ou parcial através [de competências incorporadas,](cognitive-search-predefined-skills.md)mas uma solução mais provável seria aprender modelo que compreenda o seu conteúdo, articulado em modelo de aprendizagem personalizado, envolto numa [habilidade personalizada.](cognitive-search-custom-skill-interface.md)

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

## <a name="next-steps"></a>Passos seguintes

+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [Como configurar um indexador de bolhas](search-howto-indexing-azure-blob-storage.md)
+ [Visão geral da indexação da bolha](search-blob-storage-integration.md)