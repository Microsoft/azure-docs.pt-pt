---
title: Indexadores para dados de rastreio durante a importação
titleSuffix: Azure Cognitive Search
description: Base de dados Crawl Azure SQL, Azure Cosmos DB ou armazenamento Azure para extrair dados pesquisáveis e povoar um índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379747"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexadores em Pesquisa Cognitiva Azure

Um *indexante* em Azure Cognitive Search é um crawler que extrai dados e metadados pesquisáveis de uma fonte externa de dados do Azure e povoa um índice baseado em mapeamentos de campo para campo entre o índice e a sua fonte de dados. Esta abordagem é por vezes referida como um "modelo de puxar" porque o serviço atrai dados sem ter de escrever qualquer código que adicione dados a um índice.

Os indexadores baseiam-se em tipos ou plataformas de fonte de dados, com indexantes individuais para o SQL Server em Azure, Cosmos DB, Azure Table Storage e Blob Storage. Os indexadores de armazenamento blob têm propriedades adicionais específicas para tipos de conteúdo blob.

Pode utilizar um indexador como único meio para ingestão de dados ou utilizar uma combinação de técnicas que incluem a utilização de um indexador para carregar apenas alguns dos campos no seu índice.

Pode executar indexadores a pedido ou num calendário de atualização de dados recorrente que funciona com a frequência de cinco em cinco minutos. Atualizações mais frequentes requerem um modelo push que simultaneamente atualiza dados tanto na Pesquisa Cognitiva Do Azure como na sua fonte de dados externa.

## <a name="approaches-for-creating-and-managing-indexers"></a>Abordagens para criar e gerir indexadores

Pode criar e gerir indexadores com estas abordagens:

* [Portal > Assistente de Dados de Importação](search-import-data-portal.md)
* [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Inicialmente, um indexador novo é anunciado como uma funcionalidade de pré-visualização. As funcionalidades de pré-visualização são introduzidas em APIs (REST e .NET) e, em seguida, são integradas no portal, após passarem para disponibilidade geral. Se estiver a avaliar um indexador novo, deverá planear sobre como escrever código.

## <a name="permissions"></a>Permissões

Todas as operações relacionadas com os indexadores, incluindo pedidos de estatuto ou definições get, requerem uma [chave de api-key de administração](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Origens de dados suportadas

Os indexadores rastejam lojas de dados no Azure.

* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (em pré-visualização)
* [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Base de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server nas Máquinas Virtuais do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Casos geridos pela SQL em Azure](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Passos de configuração básica
Os indexadores podem oferecer funcionalidades que são exclusivas da origem de dados. Relativamente a isto, alguns aspetos de configuração do indexador ou da origem de dados irão variar consoante o tipo de indexador. No entanto, todos os indexadores partilham da mesma composição e requisitos básicos. Os passos que são comuns a todos os indexadores são abordados abaixo.

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados
Um indexante obtém a ligação de fonte de dados a partir de um objeto de origem de *dados.* A definição de fonte de dados fornece uma cadeia de ligação e possivelmente credenciais. Ligue para a classe [Create Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API ou [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) para criar o recurso.

As origens de dados são configuradas e geridas independentemente dos indexadores que as utilizam, o que significa que uma origem de dados pode ser utilizada por vários indexadores para carregar mais de um índice de cada vez.

### <a name="step-2-create-an-index"></a>Passo 2: criar um índice
Um indexador irá automatizar algumas tarefas relacionadas com a ingestão de dados, mas geralmente a criação de um índice não é uma delas. Como pré-requisito, tem de ter um índice predefinido com campos que correspondam aos existentes na origem de dados externa. Os campos têm de coincidir com o nome e o tipo de dados. Para obter mais informações sobre estruturação de um índice, consulte [Criar um Índice (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) ou [classe Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Para obter ajuda com associações de campo, consulte [mapeamentos de campo em indexadores](search-indexer-field-mappings.md)de pesquisa cognitiva Azure .

> [!Tip]
> Apesar de os indexadores não poderem gerar um índice para si, o assistente **Importar dados** do portal pode ser útil. Na maioria dos casos, o assistente pode inferir um esquema de índice a partir dos metadados existentes na origem, apresentando um esquema de índice preliminar que pode editar em linha enquanto o assistente está ativo. Assim que o índice é criado no serviço, as outras edições no portal são limitadas principalmente à adição de novos campos. Considere o assistente para criar, mas não para rever um índice. Para aprendizagem prática, siga os passos no [portal de instruções](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Passo 3: criar e agendar o indexador
A definição do indexante é uma construção que reúne todos os elementos relacionados com a ingestão de dados. Os elementos necessários incluem uma fonte de dados e um índice. Os elementos opcionais incluem um horário e mapeamento de campo. O mapeamento de campo só é opcional se os campos de origem e os campos de índice corresponderem claramente. Para obter mais informações sobre estruturação de um indexante, consulte [Create Indexer (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Classificação de indexadores a pedido

Embora seja comum agendar indexação, um indexante também pode ser invocado a pedido usando o [comando Run:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Quando a API de execução regressa com sucesso, a invocação do indexante foi programada, mas o processamento real acontece assincronicamente. 

Pode monitorizar o estado do indexador no portal ou através da API do Status Indexer. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Obter estatuto de indexador

Pode recuperar o estado e o histórico de execução de um indexante através do [comando Get Indexer Status:](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A resposta contém o estatuto global do indexante, a última (ou em curso) invocação do indexante, e a história das recentes invocações indexadas.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

O histórico de execução contém até 50 execuções concluídas mais recentes, que são ordenadas por ordem cronológica inversa (assim a última execução vem em primeiro lugar na resposta).

## <a name="next-steps"></a>Passos seguintes
Agora que tem uma noção básica, o passo seguinte é rever os requisitos e as tarefas específicas de cada tipo de origem de dados.

* [Base de Dados SQL ou SQL Server do Azure numa máquina virtual do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
* [Indexação de bolhas CSV usando o indexante de blob de pesquisa cognitiva Azure](search-howto-index-csv-blobs.md)
* [Bolhas JSON indexantes com indexador de blob de pesquisa cognitiva Azure](search-howto-index-json-blobs.md)
