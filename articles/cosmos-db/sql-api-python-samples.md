---
title: Exemplos de Python da API do SQL para Azure Cosmos DB
description: Encontre exemplos do Python no GitHub para tarefas comuns no Azure Cosmos DB, incluindo operações CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: bd0981de1464fb3458ed5d0e1d2967a667eaf2ff
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383954"
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

Soluções de exemplo que fazem operações CRUD e outras operações comuns em Azure Cosmos DB recursos estão incluídas no repositório GitHub [Azure-documentdb-Python](https://github.com/Azure/azure-documentdb-python) . Este artigo fornece:

* Ligações para as tarefas em cada um dos ficheiros do projeto de exemplo do Python. 
* Ligações para o conteúdo relacionado de referência da API.

**Pré-requisitos**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Você pode [ativar os benefícios do assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Sua assinatura do Visual Studio oferece créditos a cada mês que você pode usar para serviços pagos do Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Também precisa do [SDK do Python](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Cada amostra é independente, configura-se automaticamente e limpa-se em seguida. Os exemplos emitem várias chamadas para [CosmosClient. CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createcontainer-database-link--collection--options-none-). Cada vez que isto é feito, a sua subscrição é cobrada por uma hora de utilização. Para obter mais informações sobre a faturação do Azure Cosmos DB, veja [Preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Exemplos de base de dados
O arquivo [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) do projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) mostra como realizar as tarefas a seguir. Para saber mais sobre os bancos de dados do Azure Cosmos antes de executar os seguintes exemplos, consulte o artigo conceitual [trabalhando com bancos de dados, contêineres e itens](databases-containers-items.md) . 

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L65-L76) |[CosmosClient.CreateDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createdatabase-database--options-none-) |
| [Ler uma base de dados por ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L79-L96) |[CosmosClient.ReadDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readdatabase-database-link--options-none-) |
| [Listar bases de dados para uma conta](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L99-L110) |[CosmosClient.ReadDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readdatabases-options-none-) |
| [Eliminar uma base de dados](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L113-L126) |[CosmosClient.DeleteDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#deletedatabase-database-link--options-none-) |

## <a name="collection-examples"></a>Exemplos de coleção
O arquivo [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) do projeto [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) mostra como realizar as tarefas a seguir. Para saber mais sobre as coleções de Cosmos do Azure antes de executar os seguintes exemplos, consulte o artigo conceitual [trabalhando com bancos de dados, contêineres e itens](databases-containers-items.md) . 

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L84-L135) |[CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createcontainer-database-link--collection--options-none-) |
| [Ler uma lista de todas as coleções numa base de dados](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L210-L222) |[CosmosClient.ReadContainers](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readcontainers-database-link--options-none-) |
| [Obter uma coleção por ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L190-L208) |[CosmosClient.ReadContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readcontainer-collection-link--options-none-) |
| [Alterar o débito de uma coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L184-L188) | [CosmosClient.ReplaceOffer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replaceoffer-offer-link--offer-)|
| [Eliminar uma coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L224-L238) |[CosmosClient.DeleteContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#deletecontainer-collection-link--options-none-) |

## <a name="document-examples"></a>Exemplos de documento
O arquivo [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) do projeto [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) mostra como realizar as tarefas a seguir. Para saber mais sobre os documentos do Azure Cosmos antes de executar os exemplos a seguir, consulte o artigo conceitual [trabalhando com bancos de dados, contêineres e itens](databases-containers-items.md) . 

| Tarefa | Referência da API |
| --- | --- |
| [Criar um documento](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createitem-database-or-container-link--document--options-none-) |
| [Criar uma coleção de documentos](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createitem-database-or-container-link--document--options-none-) |
| [Ler um documento por ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[CosmosClient.ReadItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readitem-document-link--options-none-) |
| [Ler todos os documentos numa coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[CosmosClient.ReadItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readitems-collection-link--feed-options-none-) |
| [Substituir documento com verificação ETag condicional](https://github.com/Azure/azure-cosmos-python/blob/a21f6fb4bad3f59909ef43558b598f9fb476b7bc/test/crud_tests.py#L1216-L1218) | [CosmosClient.ReplaceItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replaceitem-document-link--new-document--options-none-) |

## <a name="indexing-examples"></a>Exemplos de indexação
O arquivo [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) do projeto [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) mostra como realizar as tarefas a seguir.  Para saber mais sobre a indexação no Azure Cosmos DB antes de executar os exemplos a seguir, confira [políticas](index-policy.md)de indexação, [tipos](index-types.md)de indexação e artigos conceituais de [caminhos](index-paths.md) de indexação. 

| Tarefa | Referência da API |
| --- | --- |
| [Utilizar indexação manual (em vez do automática)](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) | Política de indexação automática |
| [Excluir caminhos do documento especificado do índice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) | Política de indexação com caminhos excluídos|
| [Excluir um documento do índice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[IndexingDirective.Exclude](/python/api/azure-cosmos/azure.cosmos.documents.indexingdirective#exclude) |
| [Definir modo de indexação](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[IndexingMode](/python/api/azure-cosmos/azure.cosmos.documents.indexingmode) |
| [Utilizar índices de intervalo em cadeias](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) | Política de indexação com caminhos incluídos|
| [Realizar uma transformação de índice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[CosmosClient.ReplaceContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replacecontainer-collection-link--collection--options-none-) |

## <a name="query-examples"></a>Exemplos de consultas
Os projetos de exemplo também mostram como fazer as tarefas de consulta a seguir. Para saber mais sobre a referência de consulta SQL no Azure Cosmos DB antes de executar os exemplos a seguir, consulte o artigo conceitual de [exemplos de consulta SQL](how-to-sql-query.md) . Para saber mais sobre a referência de consulta SQL no Azure Cosmos DB antes de executar os exemplos a seguir, consulte o artigo conceitual de [exemplos de consulta SQL](how-to-sql-query.md) . 


| Tarefa | Referência da API |
| --- | --- |
| [Consultar uma conta para uma base de dados](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L49-L62) |[CosmosClient.QueryDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#querydatabases-query--options-none-) |
| [Consultar documentos](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[CosmosClient.QueryItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#queryitems-database-or-container-link--query--options-none--partition-key-none-) |
| [Forçar uma operação de análise de intervalo num caminho com índice de hash](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[HttpHeaders.EnableScanInQuery](/python/api/azure-cosmos/azure.cosmos.http_constants.httpheaders#enablescaninquery) |

