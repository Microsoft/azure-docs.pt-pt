---
title: Exemplos da SQL API Python para o Azure Cosmos DB
description: Encontre exemplos de Python no GitHub para tarefas comuns no Azure Cosmos DB, incluindo operações CRUD.
keywords: exemplos do python
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: 87558da38b829413c8e0e5857d3b96ff49405c54
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166460"
---
# <a name="azure-cosmos-db-python-examples"></a>Exemplos do Python do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Exemplos .NET](sql-api-dotnet-samples.md)
> * [Exemplos de Java](sql-api-java-samples.md)
> * [Exemplos de Async Java](sql-api-async-java-samples.md)
> * [Exemplos de Node.js](sql-api-nodejs-samples.md)
> * [Exemplos de Python](sql-api-python-samples.md)
> * [Galeria de Exemplos de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Exemplos de soluções que fazer operações CRUD e outras operações comuns em recursos do Azure Cosmos DB estão incluídos no [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) repositório do GitHub. Este artigo fornece:

* Ligações para as tarefas em cada um dos ficheiros do projeto de exemplo do Python. 
* Ligações para o conteúdo relacionado de referência da API.

**Pré-requisitos**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Pode [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Subscrição do Visual Studio dá-lhe créditos todos os meses que pode utilizar para serviços pagos do Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Também precisa do [SDK do Python](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Cada amostra é independente, configura-se automaticamente e limpa-se em seguida. Os exemplos de emitir várias chamadas para [CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createcontainer-database-link--collection--options-none-). Cada vez que isto é feito, a sua subscrição é cobrada por uma hora de utilização. Para obter mais informações sobre a faturação do Azure Cosmos DB, veja [Preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Exemplos de base de dados
O [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) ficheiro da [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) projeto mostra como fazer as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L65-L76) |[CosmosClient.CreateDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createdatabase-database--options-none-) |
| [Ler uma base de dados por ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L79-L96) |[CosmosClient.ReadDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readdatabase-database-link--options-none-) |
| [Listar bases de dados para uma conta](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L99-L110) |[CosmosClient.ReadDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readdatabases-options-none-) |
| [Eliminar uma base de dados](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L113-L126) |[CosmosClient.DeleteDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#deletedatabase-database-link--options-none-) |

## <a name="collection-examples"></a>Exemplos de coleção
O [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) ficheiro da [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) projeto mostra como fazer as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L84-L135) |[CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createcontainer-database-link--collection--options-none-) |
| [Ler uma lista de todas as coleções numa base de dados](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L210-L222) |[CosmosClient.ReadContainers](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readcontainers-database-link--options-none-) |
| [Obter uma coleção por ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L190-L208) |[CosmosClient.ReadContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readcontainer-collection-link--options-none-) |
| [Alterar o débito de uma coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L184-L188) | [CosmosClient.ReplaceOffer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replaceoffer-offer-link--offer-)|
| [Eliminar uma coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L224-L238) |[CosmosClient.DeleteContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#deletecontainer-collection-link--options-none-) |

## <a name="document-examples"></a>Exemplos de documento
O [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) ficheiro da [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) projeto mostra como fazer as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Criar um documento](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createitem-database-or-container-link--document--options-none-) |
| [Criar uma coleção de documentos](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createitem-database-or-container-link--document--options-none-) |
| [Ler um documento por ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[CosmosClient.ReadItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readitem-document-link--options-none-) |
| [Ler todos os documentos numa coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[CosmosClient.ReadItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readitems-collection-link--feed-options-none-) |

## <a name="indexing-examples"></a>Exemplos de indexação
O [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) ficheiro da [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) projeto mostra como fazer as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Utilizar indexação manual (em vez do automática)](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) |[IndexingPolicy.Automatic](https://docs.microsoft.com/python/api/pydocumentdb.documents?view=azure-python) |
| [Excluir caminhos do documento especificado do índice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) |[IndexingPolicy.ExcludedPaths](https://docs.microsoft.com/python/api/pydocumentdb.documents.indexingdirective?view=azure-python) |
| [Excluir um documento do índice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[IndexingDirective.Exclude](/python/api/azure-cosmos/azure.cosmos.documents.indexingdirective#exclude) |
| [Definir modo de indexação](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[IndexingMode](/python/api/azure-cosmos/azure.cosmos.documents.indexingmode) |
| [Utilizar índices de intervalo em cadeias](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) |[IndexingPolicy.IncludedPaths](https://docs.microsoft.com/python/api/pydocumentdb.documents.indexingdirective?view=azure-python) |
| [Realizar uma transformação de índice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[CosmosClient.ReplaceContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replacecontainer-collection-link--collection--options-none-) |

## <a name="query-examples"></a>Exemplos de consultas
Os projetos de exemplo também mostram como realizar as seguintes tarefas de consulta:

| Tarefa | Referência da API |
| --- | --- |
| [Consultar uma conta para uma base de dados](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L49-L62) |[CosmosClient.QueryDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#querydatabases-query--options-none-) |
| [Consultar documentos](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[CosmosClient.QueryItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#queryitems-database-or-container-link--query--options-none--partition-key-none-) |
| [Forçar uma operação de análise de intervalo num caminho com índice de hash](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[HttpHeaders.EnableScanInQuery](/python/api/azure-cosmos/azure.cosmos.http_constants.httpheaders#enablescaninquery) |
