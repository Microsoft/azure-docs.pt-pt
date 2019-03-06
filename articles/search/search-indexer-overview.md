---
title: Pesquisa do Azure de indexadores para rastreamento de origens de dados durante a indexação-
description: Pesquise a base de dados SQL do Azure, o Azure Cosmos DB ou o armazenamento do Azure para extrair dados pesquisáveis e preencher um índice do Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b485b6b7f6ddbdb45d3ca6170c29a9af3c5b63dc
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407991"
---
# <a name="indexers-in-azure-search"></a>Indexadores na Pesquisa do Azure

Uma *indexador* no Azure Search é um crawler que extrai dados pesquisáveis e metadados de uma origem de dados do Azure externa e preenche um índice com base nos mapeamentos campo a campo entre o índice e sua origem de dados. Essa abordagem é por vezes, referida como 'modelo de extração' porque o serviço obtém dados sem a necessidade de escrever qualquer código que adiciona dados a um índice.

Indexadores baseiam-se em tipos de origens de dados ou plataformas, com indexadores individuais para o SQL Server no Azure, o Cosmos DB, o armazenamento de tabelas do Azure e o armazenamento de Blobs. Os indexadores de armazenamento de BLOBs tem as propriedades adicionais específicas para tipos de conteúdo do blob.

Pode utilizar um indexador como único meio para ingestão de dados ou utilizar uma combinação de técnicas que incluem a utilização de um indexador para carregar apenas alguns dos campos no seu índice.

Pode executar os indexadores a pedido ou numa agenda de atualização de dados periódica que pode ser executada a cada quinze minutos. Atualizações mais frequentes requerem um modelo de push que atualize em simultâneo os dados na Pesquisa do Azure e a origem de dados externa.

## <a name="approaches-for-creating-and-managing-indexers"></a>Abordagens para criar e gerir indexadores

Pode criar e gerir indexadores com estas abordagens:

* [Portal > Assistente de dados de importação](search-import-data-portal.md)
* [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Inicialmente, um indexador novo é anunciado como uma funcionalidade de pré-visualização. As funcionalidades de pré-visualização são introduzidas em APIs (REST e .NET) e, em seguida, são integradas no portal, após passarem para disponibilidade geral. Se estiver a avaliar um indexador novo, deverá planear sobre como escrever código.

## <a name="permissions"></a>Permissões

Todas as operações relacionadas com indexadores, incluindo os pedidos GET para o estado ou de definições, exigirem um [chave de api de administrador](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Origens de dados suportadas

Indexadores rastrear arquivos de dados no Azure.

* [SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Table Storage do Azure](search-howto-indexing-azure-tables.md) 

> [!Note]
> Armazenamento de tabelas do Azure não é suportado para [pesquisa cognitiva](cognitive-search-concept-intro.md).
>

## <a name="basic-configuration-steps"></a>Passos de configuração básica
Os indexadores podem oferecer funcionalidades que são exclusivas da origem de dados. Relativamente a isto, alguns aspetos de configuração do indexador ou da origem de dados irão variar consoante o tipo de indexador. No entanto, todos os indexadores partilham da mesma composição e requisitos básicos. Os passos que são comuns a todos os indexadores são abordados abaixo.

### <a name="step-1-create-a-data-source"></a>Passo 1: Criar uma origem de dados
Um indexador obtém a ligação à origem de dados de um *origem de dados* objeto. A definição de origem de dados fornece uma cadeia de ligação e, possivelmente, credenciais. Chamar o [criar origem de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API ou [classe de origem de dados](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) para criar o recurso.

As origens de dados são configuradas e geridas independentemente dos indexadores que as utilizam, o que significa que uma origem de dados pode ser utilizada por vários indexadores para carregar mais de um índice de cada vez.

### <a name="step-2-create-an-index"></a>Passo 2: Criar um índice
Um indexador irá automatizar algumas tarefas relacionadas com a ingestão de dados, mas geralmente a criação de um índice não é uma delas. Como pré-requisito, tem de ter um índice predefinido com campos que correspondam aos existentes na origem de dados externa. Campos têm de corresponder ao nome e tipo de dados. Para obter mais informações sobre a estruturação de um índice, consulte [criar um índice (API REST da Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) ou [indexar classe](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Para obter ajuda com associações de campo, veja [Mapeamentos de campo em indexadores do Azure Search](search-indexer-field-mappings.md).

> [!Tip]
> Apesar de os indexadores não poderem gerar um índice para si, o assistente **Importar dados** do portal pode ser útil. Na maioria dos casos, o assistente pode inferir um esquema de índice a partir dos metadados existentes na origem, apresentando um esquema de índice preliminar que pode editar em linha enquanto o assistente está ativo. Assim que o índice é criado no serviço, as outras edições no portal são limitadas principalmente à adição de novos campos. Considere o assistente para criar, mas não para rever um índice. Para aprendizagem prática, siga os passos no [portal de instruções](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Passo 3: Criar e agendar o indexador
A definição de indexador é uma construção que reúne todos os elementos relacionados com a ingestão de dados. Elementos necessários incluem uma origem de dados e um índice. Elementos opcionais incluem uma agenda e campo mapeamentos. Mapeamento de campo apenas são opcionais, se os campos de origem e de campos de índice claramente correspondem. Um indexador pode fazer referência a uma origem de dados de outro serviço, desde que essa origem de dados seja da mesma subscrição. Para mais informações sobre a estruturação de um indexador, consulte o artigo [Create Indexer (Azure Search REST API) (Criar um indexador (API REST do Azure Search))](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Executar os indexadores a pedido

Embora seja comum para agendar a indexação, um indexador também pode ser chamado a pedido através da [execute o comando](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2017-11-11
    api-key: [Search service admin key]

> [!NOTE]
> Quando executar a API devolve com êxito, a invocação de indexador foi agendada, mas o processamento real acontece de forma assíncrona. 

Pode monitorizar o estado do indexador no portal ou através da API de estado de indexador obter. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Obter estado do indexador

Pode recuperar o histórico de estado e a execução de um indexador através do [comando do obter estado do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2017-11-11
    api-key: [Search service admin key]

A resposta contém o estado geral do indexador, a invocação de indexador último (ou em curso) e o histórico de invocações de indexador recentes.

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

Histórico de execução contém até as 50 execuções concluídas mais recentes, que são classificadas em ordem cronológica reversa (para que a execução mais recente acontecer primeira na resposta).

## <a name="next-steps"></a>Passos Seguintes
Agora que tem uma noção básica, o passo seguinte é rever os requisitos e as tarefas específicas de cada tipo de origem de dados.

* [Base de Dados SQL ou SQL Server do Azure numa máquina virtual do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Table Storage do Azure](search-howto-indexing-azure-tables.md)
* [Indexar blobs CSV com o indexador Blob do Azure Search](search-howto-index-csv-blobs.md)
* [Indexar blobs JSON com o indexador Blob do Azure Search](search-howto-index-json-blobs.md)
