---
title: SQL API Python exemplos para Azure Cosmos DB
description: Encontre exemplos python no GitHub para tarefas comuns em Azure Cosmos DB, incluindo operações crud.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 05/20/2020
ms.author: sngun
ms.openlocfilehash: 3b92ae206aa623e948a612f31b48213421bf9da3
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798500"
---
# <a name="azure-cosmos-db-python-examples"></a>Exemplos do Python do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET V2 SDK Exemplos](sql-api-dotnet-samples.md)
> * [.NET V3 SDK Exemplos](sql-api-dotnet-v3sdk-samples.md)
> * [Exemplos Java V4 SDK](sql-api-java-sdk-samples.md)
> * [Exemplos de Node.js](sql-api-nodejs-samples.md)
> * [Exemplos python](sql-api-python-samples.md)
> * [Galeria de Exemplo de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

As soluções de amostra que fazem operações crud e outras operações comuns sobre os recursos db da Azure Cosmos estão incluídas no repositório GitHub [azure-documentdb-python.](https://github.com/Azure/azure-documentdb-python) Este artigo fornece:

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

A amostra [database_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) Python mostra como fazer as seguintes tarefas. Para conhecer as bases de dados da Azure Cosmos antes de executar as seguintes amostras, consulte Trabalhar com bases de [dados, contentores e artigos conceptuais.](databases-containers-items.md)

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [Leia uma base de dados por ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [Consulta das bases de dados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [Listar bases de dados para uma conta](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [Eliminar uma base de dados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>Exemplos de contentor

A amostra [de python container_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) mostra como fazer as seguintes tarefas. Para conhecer as coleções da Azure Cosmos antes de executar as seguintes amostras, consulte Trabalhar com bases de [dados, contentores e artigos conceptuais.](databases-containers-items.md)

| Tarefa | Referência da API |
| --- | --- |
| [Consulta para um recipiente](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |base de dados.query_containers |
| [Criar um contentor](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |base de dados.create_container |
| [Listar todos os recipientes numa base de dados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |base de dados.list_containers |
| [Obtenha um recipiente pela sua identificação](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |base de dados.get_container_client |
| [Gerir a entrada do contentor aprovisionado](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |recipiente.read_offer, contentor.replace_throughput|
| [Eliminar um contentor](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |base de dados.delete_container |

## <a name="item-examples"></a>Exemplos de itens

A amostra [de python item_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) mostra como fazer as seguintes tarefas. Para conhecer os documentos do Azure Cosmos antes de executar as seguintes amostras, consulte Trabalhar com bases de [dados, contentores e artigos conceptuais.](databases-containers-items.md)

| Tarefa | Referência da API |
| --- | --- |
| [Criar itens num recipiente](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |recipiente.create_item |
| [Leia um item pelo seu ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |contentor.read_item |
| [Leia todos os itens em um recipiente](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |recipiente.read_all_items |
| [Consultar um item pelo seu ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |recipiente.query_items |
| [Substituir um item](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |recipiente.replace_items |
| [Upsert um item](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |recipiente.upsert_item |
| [Eliminar um item](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |recipiente.delete_item |
| [Obtenha a alimentação de mudança de itens em um recipiente](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |recipiente.query_items_change_feed |

## <a name="indexing-examples"></a>Exemplos de indexação

A amostra [index_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) Python mostra como fazer as seguintes tarefas. Para aprender sobre indexação em Azure Cosmos DB antes de executar as seguintes amostras, consulte [políticas de indexação,](index-policy.md)tipos de [indexação](index-types.md)e indexação de [caminhos](index-paths.md) conceptuais.

| Tarefa | Referência da API |
| --- | --- |
| [Excluir um item específico da indexação](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Utilize indexação manual com itens específicos indexados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | documentos. IndexaçãoDirective.Incluir |
| [Excluir caminhos da indexação](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Definir caminhos a excluir em `IndexingPolicy` propriedade |
| [Utilizar índices de intervalo em cadeias](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Defina a política de indexação com índices de gama no tipo de dados de cordas. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Realizar uma transformação de índice](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |base de dados.replace_container (utilize a política de indexação atualizada)|
| [Use exames quando só existe índice de haxixe no caminho](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | definir o `enable_scan_in_query=True` e ao consultar os `enable_cross_partition_query=True` itens |