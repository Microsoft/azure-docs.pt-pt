---
title: Indexantes para rastreio de dados durante a importação
titleSuffix: Azure Cognitive Search
description: Base de Dados De Azure SQL Crawl, SQL Managed Instance, Azure Cosmos DB ou armazenamento Azure para extrair dados pesquisáveis e povoar um índice de Pesquisa Cognitiva azul.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 253cd8174ec523f6c8a6aae2b94f7ed367701fec
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146771"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexadores na Pesquisa Cognitiva do Azure

Um *indexante* na Azure Cognitive Search é um crawler que extrai dados e metadados pes pesjáveis de uma fonte externa de dados Azure e povoa um índice baseado em mapeamentos campo-a-campo entre o índice e a sua fonte de dados. Esta abordagem é por vezes referida como um "modelo de puxar" porque o serviço retira dados sem ter de escrever qualquer código que adicione dados a um índice.

Os indexantes baseiam-se em tipos ou plataformas de fontes de dados, com indexadores individuais para O Servidor SQL em Azure, Cosmos DB, Azure Table Storage e Blob Storage. Os indexantes de armazenamento blob têm propriedades adicionais específicas para os tipos de conteúdo blob.

Pode utilizar um indexador como único meio para ingestão de dados ou utilizar uma combinação de técnicas que incluem a utilização de um indexador para carregar apenas alguns dos campos no seu índice.

Pode executar indexadores a pedido ou num calendário de atualização de dados recorrente que funciona com a frequência de cinco em cinco minutos. Atualizações mais frequentes requerem um modelo push que atualiza simultaneamente os dados tanto na Pesquisa Cognitiva Azure como na sua fonte de dados externa.

## <a name="approaches-for-creating-and-managing-indexers"></a>Abordagens para criar e gerir indexadores

Pode criar e gerir indexadores com estas abordagens:

* [Portal > Assistente de Dados de Importação](search-import-data-portal.md)
* [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Inicialmente, um indexador novo é anunciado como uma funcionalidade de pré-visualização. As funcionalidades de pré-visualização são introduzidas em APIs (REST e .NET) e, em seguida, são integradas no portal, após passarem para disponibilidade geral. Se estiver a avaliar um indexador novo, deverá planear sobre como escrever código.

## <a name="permissions"></a>Permissões

Todas as operações relacionadas com indexantes, incluindo pedidos get para o estado ou definições, requerem [uma api-chave de administração](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Supported data sources (Origens de dados suportadas)

Indexantes rastejam lojas de dados em Azure.

* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (em pré-visualização)
* [Table Storage do Azure](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database e SQL Managed Instance](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server nas Máquinas Virtuais do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Instância Gerida do SQL](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Passos de configuração básica
Os indexadores podem oferecer funcionalidades que são exclusivas da origem de dados. Relativamente a isto, alguns aspetos de configuração do indexador ou da origem de dados irão variar consoante o tipo de indexador. No entanto, todos os indexadores partilham da mesma composição e requisitos básicos. Os passos que são comuns a todos os indexadores são abordados abaixo.

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados
Um indexante obtém a ligação de fonte de dados a partir de um objeto *de fonte de dados.* A definição de fonte de dados fornece uma cadeia de ligação e possivelmente credenciais. Ligue para a classe [Create Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API ou [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) para criar o recurso.

As origens de dados são configuradas e geridas independentemente dos indexadores que as utilizam, o que significa que uma origem de dados pode ser utilizada por vários indexadores para carregar mais de um índice de cada vez.

### <a name="step-2-create-an-index"></a>Passo 2: criar um índice
Um indexador irá automatizar algumas tarefas relacionadas com a ingestão de dados, mas geralmente a criação de um índice não é uma delas. Como pré-requisito, tem de ter um índice predefinido com campos que correspondam aos existentes na origem de dados externa. Os campos precisam de corresponder pelo nome e pelo tipo de dados. Para obter mais informações sobre a estruturação de um índice, consulte [Criar um Índice (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) ou [classe Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Para obter ajuda com associações de campo, consulte [mapeamentos de campo em indexadores de Pesquisa Cognitiva Azure](search-indexer-field-mappings.md).

> [!Tip]
> Apesar de os indexadores não poderem gerar um índice para si, o assistente **Importar dados** do portal pode ser útil. Na maioria dos casos, o assistente pode inferir um esquema de índice a partir dos metadados existentes na origem, apresentando um esquema de índice preliminar que pode editar em linha enquanto o assistente está ativo. Assim que o índice é criado no serviço, as outras edições no portal são limitadas principalmente à adição de novos campos. Considere o assistente para criar, mas não para rever um índice. Para aprendizagem prática, siga os passos no [portal de instruções](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Passo 3: criar e agendar o indexador
A definição indexante é uma construção que reúne todos os elementos relacionados com a ingestão de dados. Os elementos necessários incluem uma fonte de dados e um índice. Os elementos opcionais incluem um horário e mapeamentos de campo. O mapeamento de campo só é opcional se os campos de origem e os campos de índice corresponderem claramente. Para obter mais informações sobre a estruturação de um indexante, consulte [Create Indexer (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Os indexantes de execução a pedido

Embora seja comum agendar indexação, um indexante também pode ser invocado a pedido usando o [comando Run](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> Quando a API run retorna com sucesso, a invocação do indexante foi agendada, mas o processamento real acontece de forma assíncrona. 

Pode monitorizar o estado do indexante no portal ou através da API do Estado do Indexante. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Obtenha o estado do indexante

Pode recuperar o estado e o histórico de execução de um indexante através do [comando 'Obter Indexer Status'](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

A resposta contém o estado global do indexante, a última invocação indexante (ou em curso) e a história das recentes invocações indexantes.

```output
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
```

O histórico de execução contém até as 50 execuções concluídas mais recentes, que são ordenadas por ordem cronológica inversa (assim, a última execução vem em primeiro lugar na resposta).

## <a name="next-steps"></a>Passos seguintes
Agora que tem uma noção básica, o passo seguinte é rever os requisitos e as tarefas específicas de cada tipo de origem de dados.

* [Base de Dados Azure SQL, SQL Managed Instance ou SQL Server em uma máquina virtual Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Table Storage do Azure](search-howto-indexing-azure-tables.md)
* [Indexação de bolhas de CSV utilizando o indexante Azure Cognitive Search Blob](search-howto-index-csv-blobs.md)
* [Indexação de bolhas JSON com indexante de blob de pesquisa cognitiva Azure](search-howto-index-json-blobs.md)
