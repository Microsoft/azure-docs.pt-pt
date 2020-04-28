---
title: Pesquisar por blobs CSV
titleSuffix: Azure Cognitive Search
description: Extrair e importar CSV do armazenamento Azure Blob utilizando o modo de análise detexto delimitado.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf600890bfed570e712a159005b8ef5267298cc0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122326"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Como indexar as bolhas CSV utilizando o modo de análise detexto delimitado e os indexadores blob em Pesquisa Cognitiva Azure

Por padrão, [o indexante blob de pesquisa cognitiva Azure](search-howto-indexing-azure-blob-storage.md) analisa bolhas de texto delimitadas como um único pedaço de texto. No entanto, com bolhas que contêm dados de CSV, muitas vezes pretende tratar cada linha na bolha como um documento separado. Por exemplo, dado o seguinte texto delimitado, é melhor analisá-lo em dois documentos, cada um contendo campos "id", "datePublished" e "tags": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Neste artigo, você aprenderá a analisar bolhas CSV com um indexador de `delimitedText` blob de pesquisa cognitiva Azure, definindo o modo de análise. 

> [!NOTE]
> Siga as recomendações de configuração do indexante em [indexação de um a muitos](search-howto-index-one-to-many-blobs.md) para obter vários documentos de pesquisa a partir de uma bolha Azure.

## <a name="setting-up-csv-indexing"></a>Configuração da indexação do CSV
Para indexar as bolhas CSV, crie ou `delimitedText` atualize uma definição indexante com o modo de análise num pedido de [Create Indexer:](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`indica que a primeira linha (não em branco) de cada bolha contém cabeçalhos.
Se as bolhas não contiverem uma linha inicial do cabeçalho, os cabeçalhos devem ser especificados na configuração do indexante: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Pode personalizar o carácter delimitador utilizando a `delimitedTextDelimiter` definição de configuração. Por exemplo:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Atualmente, apenas a codificação UTF-8 é suportada. Se precisar de apoio para outras codificações, vote no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando utiliza o modo de análise de texto delimitado, a Pesquisa Cognitiva Azure assume que todas as bolhas na sua fonte de dados serão CSV. Se necessitar de suportar uma mistura de bolhas CSV e não-CSV na mesma fonte de dados, por favor vote nele no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Solicitar exemplos
Juntando tudo isto, aqui estão os exemplos completos da carga útil. 

Fonte de dados: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexante:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a tornar a Pesquisa Cognitiva Azure melhor
Se tiver pedidos de funcionalidades ou ideias para melhorias, forneça a sua entrada no [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

