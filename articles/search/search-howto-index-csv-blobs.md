---
title: Pesquisa rumo a blobs CSV
titleSuffix: Azure Cognitive Search
description: Extrair e importar CSV do armazenamento Azure Blob utilizando o modo de análise detexto delimitado.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430484"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Como indexar as bolhas CSV utilizando o modo de análise detexto delimitado e os indexadores Blob em Azure Cognitive Search

O [indexante de blob de](search-howto-indexing-azure-blob-storage.md) pesquisa cognitiva Azure fornece um `delimitedText` modo de análise para ficheiros CSV que trata cada linha no CSV como um documento de pesquisa separado. Por exemplo, dado o seguinte texto delimitado por vírgula, `delimitedText` resultaria em dois documentos no índice de pesquisa: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Sem o `delimitedText` modo de análise, todo o conteúdo do ficheiro CSV seria tratado como um documento de pesquisa.

Sempre que estiver a criar vários documentos de pesquisa a partir de uma única bolha, certifique-se de rever [as bolhas indexantes para produzir vários documentos de pesquisa](search-howto-index-one-to-many-blobs.md) para entender como funcionam as atribuições chave do documento. O indexante blob é capaz de encontrar ou gerar valores que definem de forma única cada novo documento. Especificamente, pode criar um transitório `AzureSearch_DocumentKey` que gerou quando uma bolha é analisada em partes menores, onde o valor é então usado como chave do documento de pesquisa no índice.

## <a name="setting-up-csv-indexing"></a>Criação de indexação de CSV

Para indexar as bolhas CSV, crie ou atualize uma definição de indexante com o `delimitedText` modo de análise num pedido de Criar [Indexer:](/rest/api/searchservice/create-indexer)

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` indica que a primeira linha (não em branco) de cada bolha contém cabeçalhos.
Se as bolhas não contiverem uma linha inicial de cabeçalho, os cabeçalhos devem ser especificados na configuração do indexante: 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

Pode personalizar o carácter delimiter utilizando a `delimitedTextDelimiter` definição de configuração. Por exemplo:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> Atualmente, apenas a codificação UTF-8 é suportada. Se precisar de apoio para outras codificações, vote a favor no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando utiliza o modo de análise de texto delimitado, a Azure Cognitive Search assume que todas as bolhas na sua fonte de dados serão CSV. Se precisar de suportar uma mistura de bolhas de CSV e não-CSV na mesma fonte de dados, por favor vote nele no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
>

## <a name="request-examples"></a>Solicitar exemplos

Juntando tudo isto, aqui estão os exemplos completos da carga útil. 

Fonte de dados: 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

Indexante:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


