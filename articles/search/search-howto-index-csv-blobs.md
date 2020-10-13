---
title: Pesquisa rumo a blobs CSV
titleSuffix: Azure Cognitive Search
description: Extrair e importar CSV do armazenamento Azure Blob utilizando o modo de análise detexto delimitado.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: f9c01e8e31e78c277a7a3ec1e5d8d0c32b58f8bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403658"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Como indexar as bolhas CSV utilizando o modo de análise detexto delimitado e os indexadores Blob em Azure Cognitive Search

Por padrão, [o indexante de blob de pesquisa cognitiva Azure](search-howto-indexing-azure-blob-storage.md) analisa bolhas de texto delimitadas como um único pedaço de texto. No entanto, com bolhas que contêm dados de CSV, muitas vezes pretende tratar cada linha na bolha como um documento separado. Por exemplo, dado o seguinte texto delimitado, é melhor analisá-lo em dois documentos, cada um contendo campos de "id", "data publicada" e "tags": 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Neste artigo, você aprenderá a analisar as bolhas de CSV com um indexante de blob de pesquisa cognitiva Azure, definindo o `delimitedText` modo de análise. 

> [!NOTE]
> Siga as recomendações de configuração do indexante em [indexação one-to-many](search-howto-index-one-to-many-blobs.md) para obter vários documentos de pesquisa de uma bolha Azure.

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

## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a melhorar a pesquisa cognitiva do Azure
Se tiver pedidos de funcionalidades ou ideias para melhorias, forneça a sua entrada no [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Se precisar de ajuda para utilizar a função existente, publique a sua pergunta no [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).