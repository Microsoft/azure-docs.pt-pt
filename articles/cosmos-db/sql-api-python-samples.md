---
title: SQL API Python exemplos para Azure Cosmos DB
description: Encontre exemplos python no GitHub para tarefas comuns em Azure Cosmos DB, incluindo operações crud.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: 89c1b6b63c0c423400a5197c460a44f68f13d9d2
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683804"
---
# <a name="azure-cosmos-db-python-examples"></a>Exemplos do Python do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET V2 SDK Exemplos](sql-api-dotnet-samples.md)
> * [.NET V3 SDK Exemplos](sql-api-dotnet-v3sdk-samples.md)
> * [Exemplos Java V4 SDK](sql-api-java-sdk-samples.md)
> * [Exemplos de Node.js](sql-api-nodejs-samples.md)
> * [Exemplos python](sql-api-python-samples.md)
> * [Galeria de Exemplo de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

As soluções de amostra que fazem operações crud e outras operações comuns sobre os recursos db da Azure Cosmos estão incluídas no repositório GitHub [azure-documentdb-python.](https://github.com/Azure/azure-documentdb-python) Este artigo fornece:

* Ligações para as tarefas em cada um dos ficheiros do projeto de exemplo do Python. 
* Ligações para o conteúdo de referência da API relacionada.

**Pré-requisitos**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Pode [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): a sua subscrição do Visual Studio dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Também precisa do [SDK do Python](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Cada amostra é independente, configura-se automaticamente e limpa-se em seguida. As amostras emitem várias chamadas para [CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createcontainer-database-link--collection--options-none-). Cada vez que isto é feito, a sua subscrição é cobrada por uma hora de utilização. Para obter mais informações sobre a faturação do Azure Cosmos DB, veja [Preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Exemplos de base de dados
O ficheiro [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) do projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) mostra como fazer as seguintes tarefas. Para conhecer as bases de dados da Azure Cosmos antes de executar as seguintes amostras, consulte Trabalhar com bases de [dados, contentores e artigos conceptuais.](databases-containers-items.md) 

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L65-L76) |[CosmosClient.CreateDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createdatabase-database--options-none-) |
| [Leia uma base de dados por ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L79-L96) |[CosmosClient.ReadDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readdatabase-database-link--options-none-) |
| [Listar bases de dados para uma conta](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L99-L110) |[CosmosClient.ReadDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readdatabases-options-none-) |
| [Eliminar uma base de dados](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L113-L126) |[CosmosClient.DeleteDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#deletedatabase-database-link--options-none-) |

## <a name="collection-examples"></a>Exemplos de coleção
O [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) Program.py arquivo do projeto [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) mostra como fazer as seguintes tarefas. Para conhecer as coleções da Azure Cosmos antes de executar as seguintes amostras, consulte Trabalhar com bases de [dados, contentores e artigos conceptuais.](databases-containers-items.md) 

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L84-L135) |[CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createcontainer-database-link--collection--options-none-) |
| [Ler uma lista de todas as coleções numa base de dados](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L210-L222) |[CosmosClient.ReadContainers](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readcontainers-database-link--options-none-) |
| [Obtenha uma coleção por ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L190-L208) |[CosmosClient.ReadContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readcontainer-collection-link--options-none-) |
| [Alterar o débito de uma coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L184-L188) | [CosmosClient.ReplaceOffer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replaceoffer-offer-link--offer-)|
| [Delete a collection (Eliminar uma coleção)](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L224-L238) |[CosmosClient.DeleteContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#deletecontainer-collection-link--options-none-) |

## <a name="document-examples"></a>Exemplos de documento
O ficheiro [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) do projeto [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) mostra como fazer as seguintes tarefas. Para conhecer os documentos do Azure Cosmos antes de executar as seguintes amostras, consulte Trabalhar com bases de [dados, contentores e artigos conceptuais.](databases-containers-items.md) 

| Tarefa | Referência da API |
| --- | --- |
| [Criar um documento](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createitem-database-or-container-link--document--options-none-) |
| [Criar uma coleção de documentos](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createitem-database-or-container-link--document--options-none-) |
| [Leia um documento por ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[CosmosClient.ReadItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readitem-document-link--options-none-) |
| [Ler todos os documentos numa coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[CosmosClient.ReadItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readitems-collection-link--feed-options-none-) |
| [Substituir documento com verificação ETag condicional](https://github.com/Azure/azure-cosmos-python/blob/a21f6fb4bad3f59909ef43558b598f9fb476b7bc/test/crud_tests.py#L1216-L1218) | [CosmosClient.ReplaceItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replaceitem-document-link--new-document--options-none-) |

## <a name="indexing-examples"></a>Exemplos de indexação
O [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) Program.py ficheiro do projeto [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) mostra como fazer as seguintes tarefas.  Para aprender sobre indexação em Azure Cosmos DB antes de executar as seguintes amostras, consulte [políticas de indexação,](index-policy.md)tipos de [indexação](index-types.md)e indexação de [caminhos](index-paths.md) conceptuais. 

| Tarefa | Referência da API |
| --- | --- |
| [Utilizar indexação manual (em vez do automática)](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) | Política de indexação automática |
| [Excluir caminhos do documento especificado do índice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) | Política de indexação com caminhos excluídos|
| [Excluir um documento do índice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[IndexingDirective.Exclude](/python/api/azure-cosmos/azure.cosmos.documents.indexingdirective#exclude) |
| [Definir modo de indexação](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[Modo indexante](/python/api/azure-cosmos/azure.cosmos.documents.indexingmode) |
| [Utilizar índices de intervalo em cadeias](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) | Política de indexação com caminhos incluídos|
| [Realizar uma transformação de índice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[CosmosClient.ReplaceContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replacecontainer-collection-link--collection--options-none-) |

## <a name="query-examples"></a>Exemplos de consultas
Os projetos de amostra mostram também como fazer as seguintes tarefas de consulta. Para saber sobre a referência à consulta SQL em Azure Cosmos DB antes de executar as seguintes amostras, consulte exemplos conceptuais de [consulta SQL.](how-to-sql-query.md) Para saber sobre a referência à consulta SQL em Azure Cosmos DB antes de executar as seguintes amostras, consulte exemplos conceptuais de [consulta SQL.](how-to-sql-query.md) 


| Tarefa | Referência da API |
| --- | --- |
| [Consultar uma conta para uma base de dados](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L49-L62) |[CosmosClient.QueryDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#querydatabases-query--options-none-) |
| [Consultar documentos](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[CosmosClient.QueryItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#queryitems-database-or-container-link--query--options-none--partition-key-none-) |
| [Forçar uma operação de análise de intervalo num caminho com índice de hash](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[HttpHeaders.EnableScanInQuery](/python/api/azure-cosmos/azure.cosmos.http_constants.httpheaders#enablescaninquery) |

