---
title: Node.js exemplos para gerir dados na base de dados da Azure Cosmos
description: Encontre exemplos de Node.js no GitHub para tarefas comuns no Azure Cosmos BD, incluindo operações do CRUD.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: 97563b8b442429a77bfb4d19f14e26a8158e2b3b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477900"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Node.js exemplos para gerir dados em Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET V2 SDK Exemplos](sql-api-dotnet-samples.md)
> * [.NET V3 SDK Exemplos](sql-api-dotnet-v3sdk-samples.md)
> * [Exemplos java V4 SDK](sql-api-java-sdk-samples.md)
> * [Dados de primavera V3 Exemplos SDK](sql-api-spring-data-sdk-samples.md)
> * [Exemplos de Node.js](sql-api-nodejs-samples.md)
> * [Exemplos de Python](sql-api-python-samples.md)
> * [Galeria de Exemplo de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Estão incluídas soluções de exemplo que executam operações CRUD e outras operações comuns em recursos do Azure Cosmos DB no repositório do GitHub [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples). Este artigo fornece:

* Ligações para as tarefas em cada um dos ficheiros do projeto de exemplo do Node.js.
* Ligações para o conteúdo de referência da API relacionada.

**Pré-requisitos**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Pode [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): a sua subscrição do Visual Studio dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Também precisa do [SDK JavaScript](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Cada amostra é independente, configura-se automaticamente e limpa-se em seguida. Como tal, os exemplos emitem várias chamadas para [Containers.create](/javascript/api/%40azure/cosmos/containers?preserve-view=true&view=azure-node-latest). Cada vez que isto é feito, a sua subscrição será cobrada por uma hora de utilização de acordo com o escalão de desempenho do contentor que está a ser criado.
   > 
   > 

## <a name="database-examples"></a>Exemplos de base de dados

O [ficheiro DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) mostra como executar as operações crud na base de dados. Para conhecer as bases de dados do Azure Cosmos antes de executar as seguintes amostras, consulte [Trabalhar com bases de dados, contentores e artigos](account-databases-containers-items.md) conceptuais. 

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados se não existir](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases?view=azure-node-latest&preserve-view=true#createifnotexists-databaserequest--requestoptions-) |
| [Listar bases de dados para uma conta](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases?view=azure-node-latest&preserve-view=true#readall-feedoptions-) |
| [Leia uma base de dados por ID](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database?view=azure-node-latest&preserve-view=true#read-requestoptions-) |
| [Eliminar uma base de dados](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database?view=azure-node-latest&preserve-view=true#delete-requestoptions-) |

## <a name="container-examples"></a>Exemplos de contentor

O [ficheiro ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) mostra como executar as operações CRUD no contentor. Para conhecer as coleções Azure Cosmos antes de executar as seguintes amostras, consulte [Trabalhar com bases de dados, contentores e artigos](account-databases-containers-items.md) conceptuais. 

| Tarefa | Referência da API |
| --- | --- |
| [Criar um contentor se não existir](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers?view=azure-node-latest&preserve-view=true#createifnotexists-containerrequest--requestoptions-) |
| [Listar contentores de uma conta](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers?view=azure-node-latest&preserve-view=true#readall-feedoptions-) |
| [Leia a definição de um recipiente](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container?view=azure-node-latest&preserve-view=true#read-requestoptions-) |
| [Eliminar um contentor](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container?view=azure-node-latest&preserve-view=true#delete-requestoptions-) |

## <a name="item-examples"></a>Exemplos de itens

O [ficheiro ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) mostra como executar as operações CRUD no item. Para conhecer os documentos do Azure Cosmos antes de executar as [seguintes amostras, consulte Trabalhar com bases de dados, contentores e artigos conceptuais.](account-databases-containers-items.md) 

| Tarefa | Referência da API |
| --- | --- |
| [Criar itens](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items?view=azure-node-latest&preserve-view=true#create-t--requestoptions-) |
| [Ler todos os itens num contentor](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items?view=azure-node-latest&preserve-view=true#readall-feedoptions-) |
| [Ler um item por ID](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item?view=azure-node-latest&preserve-view=true#read-requestoptions-) |
| [Ler item apenas se o mesmo tiver sido alterado](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#accesscondition) |
| [Consultar documentos](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](/javascript/api/%40azure/cosmos/items?preserve-view=true&view=azure-node-latest) |
| [Substituir um item](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest) |
| [Substituir item com verificação ETag condicional](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#accesscondition) |
| [Eliminar um item](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](/javascript/api/%40azure/cosmos/item?preserve-view=true&view=azure-node-latest) |

## <a name="indexing-examples"></a>Exemplos de indexação

O [ficheiro IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) mostra como gerir a indexação. Para aprender sobre a indexação em Azure Cosmos DB antes de executar as seguintes amostras, consulte [políticas de indexação,](index-policy.md) [tipos de indexação](index-overview.md#index-kinds)e [artigos conceptuais de caminhos de indexação.](index-policy.md#include-exclude-paths) 

| Tarefa | Referência da API |
| --- | --- |
| [Indexar manualmente um item específico](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#indexingdirective) |
| [Excluir manualmente um item específico do índice](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](/javascript/api/%40azure/cosmos/requestoptions?preserve-view=true&view=azure-node-latest#indexingdirective) |
| [Excluir um caminho do índice](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](/javascript/api/%40azure/cosmos/indexingpolicy?preserve-view=true&view=azure-node-latest#excludedpaths) |
| [Criar um índice de intervalo num caminho de cadeia](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](/javascript/api/%40azure/cosmos/indexkind?preserve-view=true&view=azure-node-latest), [IndexingPolicy](/javascript/api/%40azure/cosmos/indexingpolicy?preserve-view=true&view=azure-node-latest), [Items.query](/javascript/api/%40azure/cosmos/items?preserve-view=true&view=azure-node-latest) |
| [Criar um contentor com indexPolicy predefinido e, em seguida, atualizá-lo online](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](/javascript/api/%40azure/cosmos/containers?preserve-view=true&view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Exemplos de programação do lado do servidor

O ficheiro [index.ts](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) do projeto [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) mostra como executar as seguintes tarefas. Para saber mais sobre a programação do lado do Servidor em Azure Cosmos DB antes de executar as [seguintes amostras, consulte procedimentos armazenados, gatilhos e funções de funções definidas pelo utilizador](stored-procedures-triggers-udfs.md) artigo conceptual. 

| Tarefa | Referência da API |
| --- | --- |
| [Criar um procedimento armazenado](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](/javascript/api/%40azure/cosmos/storedprocedures?preserve-view=true&view=azure-node-latest) |
| [Executar um procedimento armazenado](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](/javascript/api/%40azure/cosmos/storedprocedure?preserve-view=true&view=azure-node-latest) |

Para obter mais informações sobre a programação do lado do servidor, veja [Programação do lado do servidor do Azure Cosmos DB: procedimentos armazenados, acionadores de base de dados e UDFs](stored-procedures-triggers-udfs.md).
