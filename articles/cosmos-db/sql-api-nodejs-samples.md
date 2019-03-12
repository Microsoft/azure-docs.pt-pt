---
title: Exemplos de node. js para o Azure Cosmos DB
description: Encontre exemplos de Node.js no GitHub para tarefas comuns no Azure Cosmos BD, incluindo operações do CRUD.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/24/2018
ms.author: deborahc
ms.openlocfilehash: b5f79fccf040c893d02ed82a397f361ae9bf3fd2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537546"
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Exemplos do Node.js do Azure Cosmos DB
> [!div class="op_single_selector"]
> * [Exemplos .NET](sql-api-dotnet-samples.md)
> * [Exemplos de Java](sql-api-java-samples.md)
> * [Exemplos de Async Java](sql-api-async-java-samples.md)
> * [Exemplos de Node.js](sql-api-nodejs-samples.md)
> * [Exemplos de Python](sql-api-python-samples.md)
> * [Galeria de Exemplos de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Estão incluídas soluções de exemplo que executam operações CRUD e outras operações comuns em recursos do Azure Cosmos DB no repositório do GitHub [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples). Este artigo fornece:

* Ligações para as tarefas em cada um dos ficheiros do projeto de exemplo do Node.js.
* Ligações para o conteúdo relacionado de referência da API.

**Pré-requisitos**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Pode [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Subscrição do Visual Studio dá-lhe créditos todos os meses que pode utilizar para serviços pagos do Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Também precisa do [SDK JavaScript](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Cada amostra é independente, configura-se automaticamente e limpa-se em seguida. Como tal, os exemplos emitem várias chamadas para [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest). Cada vez que isto é feito, a sua subscrição será cobrada por uma hora de utilização de acordo com o escalão de desempenho do contentor que está a ser criado.
   > 
   > 

## <a name="database-examples"></a>Exemplos de base de dados
O ficheiro [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement/app.js) do projeto [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/DatabaseManagement) mostra como realizar as seguintes tarefas. Para saber mais sobre as bases de dados do Cosmos do Azure antes de executar os exemplos seguintes, veja [trabalhar com bancos de dados, contentores e itens](databases-containers-items.md) artigo conceitual. 

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados se não existir](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L35-L37) |[Databases.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest) |
| [Listar bases de dados para uma conta](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Databases.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest) |
| [Ler uma base de dados por Id](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Database.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest) |
| [Eliminar uma base de dados](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L57-L60) |[Database.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest) |

## <a name="container-examples"></a>Exemplos de contentor
O ficheiro [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement/app.js) do projeto [ContainerManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) mostra como realizar as seguintes tarefas. Para saber mais sobre as coleções do Cosmos do Azure antes de executar os exemplos seguintes, veja [trabalhar com bancos de dados, contentores e itens](databases-containers-items.md) artigo conceitual. 

| Tarefa | Referência da API |
| --- | --- |
| [Criar um contentor se não existir](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest) |
| [Listar contentores de uma conta](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest) |
| [Ler uma coleção por ID](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L47-L51) |[Container.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest) |
| [Eliminar um contentor](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L54-L55) |[Container.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest) |

## <a name="item-examples"></a>Exemplos de itens
O ficheiro [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement/app.js) do projeto [ItemManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) mostra como realizar as seguintes tarefas. Para saber mais sobre os documentos do Cosmos do Azure antes de executar os exemplos seguintes, veja [trabalhar com bancos de dados, contentores e itens](databases-containers-items.md) artigo conceitual. 

| Tarefa | Referência da API |
| --- | --- |
| [Criar itens](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L49-L56) |[Items.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Ler todos os itens num contentor](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Items.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Ler um item por ID](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Ler item apenas se o mesmo tiver sido alterado](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L73-L94) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Consultar documentos](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L97-L118) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Substituir um item](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L131-L136) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Substituir item com verificação ETag condicional](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L139-L160) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Eliminar um item](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L162-L164) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |

## <a name="indexing-examples"></a>Exemplos de indexação
O ficheiro [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement/app.js) do projeto [IndexManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/IndexManagement) mostra como fazer as seguintes tarefas.  Para saber mais sobre a indexação no Azure Cosmos DB, antes de executar os exemplos seguintes, veja [políticas de indexação](index-policy.md), [indexação tipos](index-types.md), e [indexação caminhos](index-paths.md) artigos conceptuais. 

| Tarefa | Referência da API |
| --- | --- |
| [Indexar manualmente um documento específico](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L135-L177) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Excluir manualmente um documento específico do índice](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L90-L131) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Utilizar a indexação lenta para importação em volume ou ler ces pesados](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L183-L214) |[IndexingMode.Lazy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingmode?view=azure-node-latest) |
| [Excluir um caminho do índice](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L352-L429) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [Permitir uma análise num caminho de cadeia durante uma operação de intervalo](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L219-L275) |[FeedOptions.EnableScanInQuery](https://docs.microsoft.com/javascript/api/%40azure/cosmos/feedoptions?view=azure-node-latest#enablescaninquery) |
| [Criar um índice de intervalo num caminho de cadeia](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L281-L346) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Criar um contentor com indexPolicy predefinido e, em seguida, atualizá-lo online](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L435-L507) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Exemplos de programação do lado do servidor
O ficheiro [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/app.js) do projeto [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) mostra como fazer as seguintes tarefas. Para saber mais sobre a programação do lado do servidor no Azure Cosmos DB antes de executar os exemplos seguintes, veja [procedimentos armazenados, acionadores e funções definidas pelo utilizador](stored-procedures-triggers-udfs.md) artigo conceitual. 

| Tarefa | Referência da API |
| --- | --- |
| [Criar um procedimento armazenado](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/JS/upsert.js#L12-L72) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [Executar um procedimento armazenado](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/app.js#L44) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest) |

Para obter mais informações sobre a programação do lado do servidor, consulte [programação no lado do servidor do Azure Cosmos DB: Os procedimentos armazenados, acionadores de base de dados e UDFs](stored-procedures-triggers-udfs.md).

