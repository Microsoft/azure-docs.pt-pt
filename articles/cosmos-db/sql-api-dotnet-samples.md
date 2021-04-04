---
title: 'Azure Cosmos DB: Exemplos .NET para a API do SQL'
description: Encontre exemplos C# .NET no GitHub para tarefas comuns utilizando a API API AZure Cosmos DB SQL, incluindo operações CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 2e7bfd3735a57ddfa5d6100e18cf7a5676c1b9b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019045"
---
# <a name="azure-cosmos-db-net-examples-for-the-sql-api"></a>Azure Cosmos DB: Exemplos .NET para a API do SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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

O [repositório azure-cosmos-dotnet-v2](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples) GitHub inclui as mais recentes soluções de amostra .NET para executar CRUD e outras operações comuns em recursos DB da Azure Cosmos. Este artigo fornece:

* Ligações para as tarefas em cada um dos ficheiros do projeto de C# de exemplo. 
* Ligações para o conteúdo de referência da API relacionada.

Para amostras de código .NET SDK Version 3.0 (Preview), consulte as amostras mais recentes no [repositório azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3) GitHub. 

## <a name="prerequisites"></a>Pré-requisitos

Visual Studio 2019 com o fluxo de trabalho de desenvolvimento Azure instalado
- Você pode baixar  e usar a [Edição Comunitária visual 2019 gratuita do Visual Studio 2019.](https://www.visualstudio.com/downloads/) Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio. 

O [ pacoteMicrosoft.Azure.DocumentDB NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 

Uma subscrição Azure ou conta de testes gratuitos da Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- Pode [ativar os benefícios do assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): A subscrição do Visual Studio dá-lhe créditos mensais, que pode utilizar para serviços Azure pagos.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> As amostras são autossuficientes e configuradas e limpas depois de si mesmas com múltiplas chamadas para [CreateDocumentCollectionAsync()](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync). Cada ocorrência fatura a sua subscrição por uma hora de utilização no nível de desempenho da sua coleção. 
> 

## <a name="database-examples"></a>Exemplos de base de dados
O método [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L75-L91) do projeto *Sample DatabaseManagement* mostra como fazer as seguintes tarefas. Para saber mais sobre as bases de dados da Azure Cosmos antes de executar as seguintes amostras, consulte [Work with databases, containers e itens](account-databases-containers-items.md). 

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L77) |[DocumentClient.CreateDataIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) |
| [Leia uma base de dados por ID](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L79) |[DocumentClient.ReadDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabaseasync) |
| [Ler todas as bases de dados](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L83) |[DocumentClient.ReadDatabaseFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabasefeedasync) |
| [Eliminar uma base de dados](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L89) |[DocumentClient.DeleteDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedatabaseasync) |

## <a name="collection-examples"></a>Exemplos de coleção
O método [RunCollectionDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L86-L104) do projeto *CollectionManagement* mostra como fazer as seguintes tarefas. Para saber mais sobre as coleções da Azure Cosmos antes de executar as seguintes amostras, consulte [Work with databases, containers e itens](account-databases-containers-items.md). 

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L109) |[DocumentClient.CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync) 
| [Alterar um desempenho configurado de uma coleção](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L190) |[DocumentClient.ReplaceOfferAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync) |
| [Obtenha uma coleção por ID](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L202) |[DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) |
| [Ler todas as coleções numa base de dados](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L215) |[DocumentClient.ReadDocumentCollectionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionfeedasync) |
| [Delete a collection (Eliminar uma coleção)](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L228) |[DocumentClient.DeleteDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentcollectionasync) |

## <a name="document-examples"></a>Exemplos de documento
O método [RunDocumentsDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L109-L118) do projeto *DocumentManagement* da amostra mostra como fazer as seguintes tarefas. Para saber mais sobre os documentos da Azure Cosmos antes de executar as seguintes amostras, consulte [Trabalhar com bases de dados, contentores e itens.](account-databases-containers-items.md) 

| Tarefa | Referência da API |
| --- | --- |
| [Criar um documento](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L154) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) |
| [Leia um documento por ID](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L168) |[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync) |
| [Ler todos os documentos numa coleção](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L190) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Consultar documentos](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L18-L222) |[DocumentClient.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Substituir um documento](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L240-L242) |[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Upsert um documento](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L254) |[DocumentClient.UpsertDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.upsertdocumentasync) |
| [Eliminar um documento](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L275-L277) |[DocumentClient.DeleteDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentasync) |
| [Trabalhar com objetos dinâmicos .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L349-L397) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync)<br>[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync)<br>[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Substituir documento com verificação ETag condicional](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L405-L501) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |
| [Ler documento apenas se este for alterado](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L454-L500) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |

## <a name="indexing-examples"></a>Exemplos de indexação
O método [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L93-L115) do projeto *IndexManagement* da amostra mostra como fazer as seguintes tarefas. Para saber mais sobre a indexação em Azure Cosmos DB antes de executar as seguintes amostras, consulte as políticas de [índice,](index-policy.md) [os tipos de índices](index-overview.md#index-types)e [os caminhos de índice](index-policy.md#include-exclude-paths). 

| Tarefa | Referência da API |
| --- | --- |
| [Excluir um documento do índice](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L123-L162) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.documents.indexingdirective) |
| [Use indexação preguiçosa](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L174-L192) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.documents.indexingpolicy.indexingmode) |
| [Excluir caminhos do documento especificado do índice](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L202-L263) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths) |
| [Forçar uma operação de análise de intervalo num caminho com índice de hash](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L305-L340) |[FeedOptions.EnableScanInQuery](/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery) |
| [Utilizar índices de intervalo em cadeias](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L265-L316) |[IndexingPolicy.IncludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths)<br>[RangeIndex](/dotnet/api/microsoft.azure.documents.rangeindex) |
| [Realizar uma transformação de índice](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L318-L370) |[ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync) |

## <a name="geospatial-examples"></a>Exemplos geoespaciais
O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L94-L139) do projeto *Geoespacial* da amostra mostra como fazer as seguintes tarefas.  Para saber mais sobre geoJSON e dados geoespacial antes de executar as [seguintes amostras, consulte os dados de localização geoespaciais e geojson](./sql-query-geospatial-intro.md). 

| Tarefa | Referência da API |
| --- | --- |
| [Ativar indexação geoespacial numa nova coleção](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L48) |[IndexingPolicy](/dotnet/api/microsoft.azure.documents.indexingpolicy) <br> [IndexKind.Spatial](/dotnet/api/microsoft.azure.documents.indexkind) <br>[DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Inserir documentos com pontos GeoJSON](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L104-L114) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) </br> [DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Encontrar pontos dentro de uma distância especificada](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L158-L199) |[ST_DISTANCE](sql-query-st-distance.md) </br> [GeometryOperationExtensions.Distance](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) |
| [Encontrar pontos dentro de um polígono](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L204-L227) |[ST_WITHIN](sql-query-st-within.md) </br> [GeometriaOperationExtensions.Within](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) </br>[Polígono](/dotnet/api/microsoft.azure.documents.spatial.polygon) |
| [Ativar indexação geoespacial numa coleção existente](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L385-L391) |[DocumentClient.ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync)<br>[DocumentCollection.IndexingPolicy](/dotnet/api/microsoft.azure.documents.documentcollection.indexingpolicy) |
| [Validar dados de ponto e polígono](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L290-L326) |[ST_ISVALID](sql-query-st-isvalid.md)<br>[ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md)<br>[GeometryOperationExtensions.IsValid](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvalid)<br>[GeometryOperationExtensions.IsValidDetailed](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvaliddetailed) |

## <a name="query-examples"></a>Exemplos de consultas
O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L64-L129) do projeto *de consultas* de amostra mostra como fazer as seguintes tarefas usando a gramática de consulta SQL, o provedor LINQ com consulta, e Lambda. Para saber mais sobre a referência de consulta SQL em Azure Cosmos DB antes de executar as [seguintes amostras, consulte exemplos de consulta SQL para Azure Cosmos DB](./sql-query-getting-started.md). 

| Tarefa | Referência da API |
| --- | --- |
| [Consultar todos os documentos](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L131-L147) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consultar igualdade com ==](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L186-L198) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consultar desigualdade com != e NOT](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L288-L332) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consultar com operadores de intervalo como >, <, >=, <=](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L334-L355) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consultar com operadores de intervalo em relação a cadeias](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L355-L370) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consulta com ORDER BY](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L422-L446) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consulta com funções agregadas](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L448-L496) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Trabalhar com subdocumentos](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L498-L524) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consultar com Associações dentro de documentos](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L526-L540) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consulta com operadores de cordas, matemática e matrizes](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L636-L673) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consultar com SQL parametrizado com SqlQuerySpec](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L149-L184) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100))<br>[SqlQuerySpec](/dotnet/api/microsoft.azure.documents.sqlqueryspec) |
| [Consultar com paginação explícita](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L675-L734) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consultar coleções particionadas em paralelo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L736-L807) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consulta com ORDER BY para coleções divididas](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L809-L882) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |

## <a name="change-feed-examples"></a>Exemplos de feed de alterações 
O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L54-L97) do projeto *ChangeFeed* da amostra mostra como fazer as seguintes tarefas. Para saber mais sobre a mudança de feed em Azure Cosmos DB antes de executar as seguintes amostras, consulte [ler Azure Cosmos DB alterar feed feed](read-change-feed.md) e alterar processador de [feed](change-feed-processor.md). 

| Tarefa | Referência da API |
| --- | --- |
| [Ler feed de alterações](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L132) |[DocumentClient.CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery) | 
| [Ler intervalos de chave de partição](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L118) |[DocumentClient.ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync) | 

A amostra do processador change feed, [ChangeFeedMigrationTool,](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/ChangeFeedMigrationTool)mostra como usar a biblioteca do processador de feed change para replicar dados para outro recipiente Cosmos.   

## <a name="server-side-programming-examples"></a>Exemplos de programação do lado do servidor
O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ServerSideScripts/Program.cs#L58-L91) do projeto *ServerSideScripts* da amostra mostra como fazer as seguintes tarefas. Para saber mais sobre a programação do lado do servidor no Azure Cosmos DB antes de executar as [seguintes amostras, consulte procedimentos armazenados, gatilhos e funções definidas pelo utilizador](stored-procedures-triggers-udfs.md). 

| Tarefa | Referência da API |
| --- | --- |
| [Criar um procedimento armazenado](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L110) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Executar um procedimento armazenado](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L125) |[DocumentClient.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.executestoredprocedureasync) |
| [Ler um feed de documento para um procedimento armazenado](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L198) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Criar um procedimento armazenado com ORDER BY](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L223) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Criar um pré-acionador](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L273) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Criar um pós-acionador](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L341) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Criar uma função definida pelo utilizador (UDF)](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L421) |[DocumentClient.CreateUserDefinedFunctionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserdefinedfunctionasync) |

## <a name="user-management-examples"></a>Exemplos de gestão de utilizadores
O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/UserManagement/Program.cs#L55-L129) do projeto *UserManagement* da amostra mostra como fazer as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Criar um utilizador](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L93) |[DocumentClient.CreateUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserasync) |
| [Definir permissões numa coleção ou documento](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L97) |[DocumentClient.CreatePermissionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createpermissionasync) |
| [Obter uma lista de permissões de um utilizador](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L241) |[DocumentClient.ReadUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readuserasync)<br>[DocumentClient.ReadPermissionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpermissionfeedasync) |
