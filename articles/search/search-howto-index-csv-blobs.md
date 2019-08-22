---
title: Indexar BLOBs CSV com Azure Search indexador de BLOBs-Azure Search
description: Rastreie BLOBs CSV no armazenamento de BLOBs do Azure para pesquisa de texto completo usando um índice de Azure Search. Indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o armazenamento de BLOBs do Azure.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: b135fd1a0758567a7b504996bf442a913741fe59
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656762"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexando BLOBs CSV com o indexador de blob Azure Search

> [!Note]
> o modo de análise delimitedText está em visualização e não se destina ao uso em produção. A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para o SDK do .NET no momento.
>

Por padrão, [Azure Search indexador de blob](search-howto-indexing-azure-blob-storage.md) analisa blobs de texto delimitados como uma única parte do texto. No entanto, com os blobs que contêm dados CSV, muitas vezes você deseja tratar cada linha no blob como um documento separado. Por exemplo, considerando o seguinte texto delimitado, talvez você queira analisá-lo em dois documentos, cada um contendo os campos "ID", "datePublished" e "tags": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Neste artigo, você aprenderá a analisar BLOBs CSV com um Azure Search blob indexerby definindo o modo de `delimitedText` análise. 

> [!NOTE]
> Siga as recomendações de configuração do indexador em indexação de [um para muitos](search-howto-index-one-to-many-blobs.md) para produzir vários documentos de pesquisa de um blob do Azure.

## <a name="setting-up-csv-indexing"></a>Configurando a indexação de CSV
Para indexar BLOBs CSV, crie ou atualize uma definição de indexador `delimitedText` com o modo de análise em uma solicitação [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer) :

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`indica que a primeira linha (não em branco) de cada blob contém cabeçalhos.
Se os BLOBs não contiverem uma linha de cabeçalho inicial, os cabeçalhos deverão ser especificados na configuração do indexador: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Você pode personalizar o caractere delimitador `delimitedTextDelimiter` usando a definição de configuração. Por exemplo:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Atualmente, há suporte apenas para a codificação UTF-8. Se você precisar de suporte para outras codificações, vote nela no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando você usa o modo de análise de texto delimitado, Azure Search pressupõe que todos os BLOBs em sua fonte de dados serão CSV. Se você precisar dar suporte a uma combinação de BLOBs CSV e não CSV na mesma fonte de dados, vote para ele no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exemplos de solicitação
Reunindo tudo isso, aqui estão os exemplos de conteúdo completo. 

Fonte 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexador

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Ajude-nos a tornar Azure Search melhor
Se você tiver solicitações de recursos ou ideias para melhorias, forneça sua entrada [](https://feedback.azure.com/forums/263029-azure-search/)no UserVoice.

