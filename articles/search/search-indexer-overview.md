---
title: Indexadores para rastreamento de dados durante a importação
titleSuffix: Azure Cognitive Search
description: Rastreie o banco de dados SQL do Azure, o Azure Cosmos DB ou o armazenamento do Azure para extrair e popular um índice de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 09a1b8e54726accf705515bf077f6a599f0f9b78
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74266908"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexadores no Azure Pesquisa Cognitiva

Um *indexador* no Azure pesquisa cognitiva é um rastreador que extrai dados pesquisáveis e metadados de uma fonte de dados externa do Azure e popula um índice com base em mapeamentos de campo para campo entre o índice e sua fonte de dados. Essa abordagem, às vezes, é chamada de "modelo de pull" porque o serviço recebe dados sem a necessidade de escrever nenhum código que adicione dados a um índice.

Os indexadores são baseados em tipos de fontes de dados ou plataformas, com indexadores individuais para SQL Server no Azure, Cosmos DB, armazenamento de tabelas do Azure e armazenamento de BLOBs. Indexadores de armazenamento de BLOBs têm propriedades adicionais específicas para tipos de conteúdo de BLOB.

Pode utilizar um indexador como único meio para ingestão de dados ou utilizar uma combinação de técnicas que incluem a utilização de um indexador para carregar apenas alguns dos campos no seu índice.

Você pode executar indexadores sob demanda ou em uma agenda de atualização de dados recorrente que é executada sempre que a cada cinco minutos. Atualizações mais frequentes exigem um modelo de push que atualiza dados simultaneamente no Azure Pesquisa Cognitiva e na fonte de dados externa.

## <a name="approaches-for-creating-and-managing-indexers"></a>Abordagens para criar e gerir indexadores

Pode criar e gerir indexadores com estas abordagens:

* [Assistente de > importar dados do portal](search-import-data-portal.md)
* [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Inicialmente, um indexador novo é anunciado como uma funcionalidade de pré-visualização. As funcionalidades de pré-visualização são introduzidas em APIs (REST e .NET) e, em seguida, são integradas no portal, após passarem para disponibilidade geral. Se estiver a avaliar um indexador novo, deverá planear sobre como escrever código.

## <a name="permissions"></a>Permissões

Todas as operações relacionadas a indexadores, incluindo solicitações GET para status ou definições, exigem uma [chave de API de administração](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Origens de dados suportadas

Os indexadores rastreiam armazenamentos de dados no Azure.

* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Azure data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (em versão prévia)
* [Table Storage do Azure](search-howto-indexing-azure-tables.md)
* [BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
* [Base de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server nas Máquinas Virtuais do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Instâncias gerenciadas do SQL no Azure](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Passos de configuração básica
Os indexadores podem oferecer funcionalidades que são exclusivas da origem de dados. Relativamente a isto, alguns aspetos de configuração do indexador ou da origem de dados irão variar consoante o tipo de indexador. No entanto, todos os indexadores partilham da mesma composição e requisitos básicos. Os passos que são comuns a todos os indexadores são abordados abaixo.

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados
Um indexador Obtém a conexão da fonte de dados de um objeto de *fonte de dados* . A definição da fonte de dados fornece uma cadeia de conexão e possivelmente credenciais. Chame a API REST de [DataSource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) ou a [classe DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) para criar o recurso.

As origens de dados são configuradas e geridas independentemente dos indexadores que as utilizam, o que significa que uma origem de dados pode ser utilizada por vários indexadores para carregar mais de um índice de cada vez.

### <a name="step-2-create-an-index"></a>Passo 2: criar um índice
Um indexador irá automatizar algumas tarefas relacionadas com a ingestão de dados, mas geralmente a criação de um índice não é uma delas. Como pré-requisito, tem de ter um índice predefinido com campos que correspondam aos existentes na origem de dados externa. Os campos precisam corresponder por nome e tipo de dados. Para obter mais informações sobre como estruturar um índice, consulte [criar um índice (API REST do Azure pesquisa cognitiva)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) ou [classe de índice](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Para obter ajuda com associações de campo, consulte [mapeamentos de campo no Azure pesquisa cognitiva indexadores](search-indexer-field-mappings.md).

> [!Tip]
> Apesar de os indexadores não poderem gerar um índice para si, o assistente **Importar dados** do portal pode ser útil. Na maioria dos casos, o assistente pode inferir um esquema de índice a partir dos metadados existentes na origem, apresentando um esquema de índice preliminar que pode editar em linha enquanto o assistente está ativo. Assim que o índice é criado no serviço, as outras edições no portal são limitadas principalmente à adição de novos campos. Considere o assistente para criar, mas não para rever um índice. Para aprendizagem prática, siga os passos no [portal de instruções](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Passo 3: criar e agendar o indexador
A definição do indexador é um constructo que reúne todos os elementos relacionados à ingestão de dados. Os elementos necessários incluem uma fonte de dados e um índice. Os elementos opcionais incluem um agendamento e mapeamentos de campo. O mapeamento de campos só será opcional se os campos de origem e de índice corresponderem claramente. Um indexador pode fazer referência a uma origem de dados de outro serviço, desde que essa origem de dados seja da mesma subscrição. Para obter mais informações sobre como estruturar um indexador, consulte [criar indexador (API REST do Azure pesquisa cognitiva)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Executar indexadores sob demanda

Embora seja comum agendar a indexação, um indexador também pode ser invocado sob demanda usando o [comando executar](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Quando a API de execução é retornada com êxito, a invocação do indexador foi agendada, mas o processamento real ocorre de forma assíncrona. 

Você pode monitorar o status do indexador no portal ou por meio da API obter status do indexador. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Obter o status do indexador

Você pode recuperar o status e o histórico de execução de um indexador por meio do [comando obter status do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A resposta contém o status geral do indexador, a última invocação (ou em andamento) do indexador e o histórico de invocações recentes do indexador.

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

O histórico de execução contém até as 50 execuções concluídas mais recentes, que são classificadas em ordem cronológica inversa (para que a execução mais recente venha primeiro na resposta).

## <a name="next-steps"></a>Passos seguintes
Agora que tem uma noção básica, o passo seguinte é rever os requisitos e as tarefas específicas de cada tipo de origem de dados.

* [Base de Dados SQL ou SQL Server do Azure numa máquina virtual do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Table Storage do Azure](search-howto-indexing-azure-tables.md)
* [Indexando BLOBs CSV usando o indexador de blob Pesquisa Cognitiva do Azure](search-howto-index-csv-blobs.md)
* [Indexando BLOBs JSON com o indexador de blob Pesquisa Cognitiva do Azure](search-howto-index-json-blobs.md)
