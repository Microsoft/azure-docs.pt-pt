---
title: SQL API Python exemplos para Azure Cosmos DB
description: Encontre exemplos de Python no GitHub para tarefas comuns em Azure Cosmos DB, incluindo operações CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 05/20/2020
ms.author: sngun
ms.custom: tracking-python
ms.openlocfilehash: c52651e15f648bede419501f516392acbc266e8a
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558014"
---
# <a name="azure-cosmos-db-python-examples"></a>Exemplos do Python do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET V2 SDK Exemplos](sql-api-dotnet-samples.md)
> * [.NET V3 SDK Exemplos](sql-api-dotnet-v3sdk-samples.md)
> * [Exemplos java V4 SDK](sql-api-java-sdk-samples.md)
> * [Exemplos de Node.js](sql-api-nodejs-samples.md)
> * [Exemplos de Python](sql-api-python-samples.md)
> * [Galeria de Exemplo de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

As soluções de amostra que fazem operações CRUD e outras operações comuns em recursos DB Azure Cosmos estão incluídas no repositório gitHub de [azure-documentdb-python.](https://github.com/Azure/azure-documentdb-python) Este artigo fornece:

* Ligações para as tarefas em cada um dos ficheiros do projeto de exemplo do Python.
* Ligações para o conteúdo de referência da API relacionada.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Pode [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): a sua subscrição do Visual Studio dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Também precisa do [SDK do Python](sql-api-sdk-python.md).

   > [!NOTE]
   > Cada amostra é independente, configura-se automaticamente e limpa-se em seguida. As amostras emitem várias chamadas para `CosmosClient.CreateContainer` . Cada vez que isto é feito, a sua subscrição é cobrada por uma hora de utilização. Para obter mais informações sobre a faturação do Azure Cosmos DB, veja [Preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="database-examples"></a>Exemplos de base de dados

A amostra [database_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) Python mostra como fazer as seguintes tarefas. Para conhecer as bases de dados do Azure Cosmos antes de executar as seguintes amostras, consulte [Trabalhar com bases de dados, contentores e artigos](databases-containers-items.md) conceptuais.

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [Leia uma base de dados por ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [Consultar as bases de dados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [Listar bases de dados para uma conta](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [Eliminar uma base de dados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>Exemplos de contentor

A amostra [container_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) Python mostra como fazer as seguintes tarefas. Para conhecer as coleções Azure Cosmos antes de executar as seguintes amostras, consulte [Trabalhar com bases de dados, contentores e artigos](databases-containers-items.md) conceptuais.

| Tarefa | Referência da API |
| --- | --- |
| [Consulta para um recipiente](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |base de dados.query_containers |
| [Criar um contentor](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |base de dados.create_container |
| [Listar todos os contentores numa base de dados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |base de dados.list_containers |
| [Obtenha um recipiente pelo seu ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |base de dados.get_container_client |
| [Gerir a produção do contentor a provisionada](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer, container.replace_throughput|
| [Eliminar um contentor](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |base de dados.delete_container |

## <a name="item-examples"></a>Exemplos de itens

A amostra [item_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) Python mostra como fazer as seguintes tarefas. Para conhecer os documentos do Azure Cosmos antes de executar as [seguintes amostras, consulte Trabalhar com bases de dados, contentores e artigos conceptuais.](databases-containers-items.md)

| Tarefa | Referência da API |
| --- | --- |
| [Criar itens num recipiente](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [Leia um item pelo seu ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [Leia todos os itens num recipiente](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [Consulta um item pelo seu ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [Substituir um item](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [Aumentar um item](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [Eliminar um item](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [Obtenha a alteração de itens num recipiente](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>Exemplos de indexação

A amostra [index_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) Python mostra como fazer as seguintes tarefas. Para aprender sobre a indexação em Azure Cosmos DB antes de executar as seguintes amostras, consulte [políticas de indexação,](index-policy.md) [tipos de indexação](index-types.md)e [artigos conceptuais de caminhos de indexação.](index-paths.md)

| Tarefa | Referência da API |
| --- | --- |
| [Excluir um item específico da indexação](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Utilize indexação manual com itens específicos indexados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | documentos. IndexingDirective.Include |
| [Excluir caminhos da indexação](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Definir caminhos para excluir em `IndexingPolicy` propriedade |
| [Utilizar índices de intervalo em cadeias](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Defina a política de indexação com índices de gama no tipo de dados de cadeia. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Realizar uma transformação de índice](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |base de dados.replace_container (utilizar a política de indexação atualizada)|
| [Use digitalizações quando só existe índice de haxixe no caminho](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | definir o `enable_scan_in_query=True` e ao consultar os `enable_cross_partition_query=True` itens |
