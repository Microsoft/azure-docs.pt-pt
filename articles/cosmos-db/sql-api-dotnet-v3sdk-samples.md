---
title: 'Azure Cosmos DB: .NET (Microsoft.Azure.Cosmos) exemplos para a API SQL'
description: Encontre os exemplos C# .NET V3 SDK no GitHub para tarefas comuns utilizando o Azure Cosmos DB SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.openlocfilehash: d162639885a7736953a3e7388336c3105f2f0893
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683854"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Os exemplos da Azure Cosmos DB.NET V3 SDK (Microsoft.Azure.Cosmos) para a API SQL

> [!div class="op_single_selector"]
> * [.NET V2 SDK Exemplos](sql-api-dotnet-samples.md)
> * [.NET V3 SDK Exemplos](sql-api-dotnet-v3sdk-samples.md)
> * [Exemplos Java V4 SDK](sql-api-java-sdk-samples.md)
> * [Exemplos de Node.js](sql-api-nodejs-samples.md)
> * [Exemplos python](sql-api-python-samples.md)
> * [Galeria de Exemplo de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
>
>

O repositório [azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) GitHub inclui as mais recentes soluções de amostra .NET para executar CRUD e outras operações comuns nos recursos DB da Azure Cosmos. Se estiver familiarizado com a versão anterior do .NET SDK, poderá estar habituado à recolha e documento de termos. Uma vez que o Azure Cosmos DB suporta vários modelos API, a versão 3.0 do .NET SDK utiliza os termos genéricos "recipiente" e "item". Os contentores podem ser uma coleção, um grafo ou uma tabela. Os itens podem ser um documento, um vértice/aresta ou uma linha e são o conteúdo dentro dos contentores. Este artigo fornece:

* Ligações para as tarefas em cada um dos ficheiros do projeto de C# de exemplo.
* Ligações para o conteúdo de referência da API relacionada.

## <a name="prerequisites"></a>Pré-requisitos

Visual Studio 2019 com o fluxo de trabalho de desenvolvimento do Azure instalado

- Você pode baixar e usar o Visual Studio **gratuito** [2019 Community Edition](https://www.visualstudio.com/downloads/). Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

   O [pacote NuGet Microsoft.Azure.cosmos](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

Uma subscrição Azure ou conta experimental gratuita do Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Pode [ativar os benefícios](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)de subscrição do Visual Studio : A subscrição do Estúdio Visual dá-lhe créditos todos os meses, que pode utilizar para serviços Azure pagos.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> As amostras são autossuficientes, e configuradas e limpas depois de si mesmas. Cada ocorrência fatura a sua subscrição por uma hora de utilização no nível de desempenho do seu contentor.
> 

## <a name="database-examples"></a>Exemplos de base de dados

O método [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) do projeto de *gestão* de dados da amostra mostra como fazer as seguintes tarefas. Para saber sobre as bases de dados da Azure Cosmos antes de executar as seguintes amostras, consulte O Trabalho com bases de [dados, contentores e itens](databases-containers-items.md).

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) |
| [Leia uma base de dados por ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Base de Dados.ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync?view=azure-dotnet) |
| [Leia todas as bases de dados para uma conta](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator?view=azure-dotnet) |
| [Eliminar uma base de dados](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Base de Dados.DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) |

## <a name="container-examples"></a>Exemplos de contentor

O método [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) do projeto de gestão de *contentores* da amostra mostra como fazer as seguintes tarefas. Para saber sobre os recipientes Azure Cosmos antes de executar as seguintes amostras, consulte Trabalhar com bases de [dados, recipientes e itens](databases-containers-items.md).

| Tarefa | Referência da API |
| --- | --- |
| [Criar um contentor](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Base de Dados.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [Criar um recipiente com política de índice personalizado](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L111-L127) |[Base de Dados.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [Alterar o desempenho configurado de um recipiente](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L149-L171) |[Recipiente.SubstituaOAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet) |
| [Obter um recipiente por ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L176-L185) |[ContentoraAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync?view=azure-dotnet) |
| [Leia todos os recipientes numa base de dados](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L193-L205) |[Base de Dados.GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator?view=azure-dotnet) |
| [Eliminar um contentor](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L213-L2018) |[Contentorasync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync?view=azure-dotnet) |

## <a name="item-examples"></a>Exemplos de itens

O método [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) do projeto *de gestão* de amostras mostra como fazer as seguintes tarefas. Para saber sobre os itens da Azure Cosmos antes de executar as seguintes amostras, consulte Trabalhar com bases de [dados, contentores e itens.](databases-containers-items.md)

| Tarefa | Referência da API |
| --- | --- |
| [Criar um item](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Recipiente.CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) |
| [Ler um item por ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[recipiente. ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) |
| [Consulta de itens](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[recipiente. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Substituir um item](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L323-L363) |[recipiente. SubstituaItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync?view=azure-dotnet) |
| [Upsert um item](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L366-L411) |[recipiente. UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) |
| [Eliminar um item](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L414-L424) |[recipiente. DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync?view=azure-dotnet) |
| [Substitua um item por verificação etag condicional](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L585-L674) |[Opções de Pedido.IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag?view=azure-dotnet) |

## <a name="indexing-examples"></a>Exemplos de indexação

O método [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) do projeto de *indexmanagement* da amostra mostra como fazer as seguintes tarefas. Para aprender sobre indexação em Azure Cosmos DB antes de executar as seguintes amostras, consulte as políticas de [índice,](index-policy.md)tipos de [índices](index-types.md)e [trajetórias](index-paths.md)de índice . 

| Tarefa | Referência da API |
| --- | --- |
| [Excluir um item do índice](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective?view=azure-dotnet) |
| [Use indexação preguiçosa](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexaçãoPolítica.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode?view=azure-dotnet) |
| [Excluir caminhos de itens especificados do índice](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths?view=azure-dotnet) |

## <a name="query-examples"></a>Exemplos de consultas

O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) do projeto *DemoAsync* da amostra mostra como fazer as seguintes tarefas utilizando a gramática de consulta SQL, o provedor LINQ com consulta, e Lambda. Para saber sobre a referência à consulta SQL em Azure Cosmos DB antes de executar as seguintes amostras, consulte exemplos de [consulta SQL para Azure Cosmos DB](how-to-sql-query.md).

| Tarefa | Referência da API |
| --- | --- |
| [Itens de consulta de uma única divisória](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[recipiente. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Itens de consulta de várias divisórias](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[recipiente. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Consulta usando uma declaração SQL](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[recipiente. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |

## <a name="change-feed-examples"></a>Exemplos de feed de alterações

O método [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) do projeto *ChangeFeed* da amostra mostra como fazer as seguintes tarefas. Para saber sobre a mudança de feed no Azure Cosmos DB antes de executar as seguintes amostras, consulte [Read Azure Cosmos DB change feed](read-change-feed.md) e Change feed [processador](change-feed-processor.md).

| Tarefa | Referência da API |
| --- | --- |
| [Funcionalidade básica de feed de mudança](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Recipiente.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [Ler feed de mudança de um momento específico](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Recipiente.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [Ler feed de mudança desde o início](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder.withstartTime (datatime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime?view=azure-dotnet) |
| [MIgrate do processador de feed de mudança para alterar feed em V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Recipiente.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |

## <a name="server-side-programming-examples"></a>Exemplos de programação do lado do servidor

O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) do projeto *ServerSideScripts* da amostra mostra como fazer as seguintes tarefas. Para saber sobre a programação do lado do servidor no Azure Cosmos DB antes de executar as seguintes amostras, consulte [procedimentos armazenados, gatilhos e funções definidas pelo utilizador](stored-procedures-triggers-udfs.md).

| Tarefa | Referência da API |
| --- | --- |
| [Criar um procedimento armazenado](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Scripts.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync?view=azure-dotnet) |
| [Executar um procedimento armazenado](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync?view=azure-dotnet) |
| [Eliminar um procedimento armazenado](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Scripts.DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync?view=azure-dotnet) |
