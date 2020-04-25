---
title: Migrar da biblioteca executor a granel para o suporte a granel em Azure Cosmos DB .NET V3 SDK
description: Aprenda a migrar a sua aplicação desde a utilização da biblioteca de executora a granel para o suporte a granel em Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: maquaran
ms.openlocfilehash: d63b34c118cd719f73abbd6711dcb3ef02a6fb28
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146296"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migrar da biblioteca executor a granel para o suporte a granel em Azure Cosmos DB .NET V3 SDK

Este artigo descreve as etapas necessárias para migrar o código de uma aplicação existente que utiliza a biblioteca de [executora](bulk-executor-dot-net.md) a granel .NET para a funcionalidade de suporte a [granel](tutorial-sql-api-dotnet-bulk-import.md) na versão mais recente do .NET SDK.

## <a name="enable-bulk-support"></a>Ativar suporte a granel

Ativar o `CosmosClient` suporte a granel na instância através da configuração [AllowBulkExecution:](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Criar Tarefas para cada operação

O suporte a granel no .NET SDK funciona alavancando a Biblioteca Paralela de [Tarefas](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) e operações de agrupamento que ocorrem simultaneamente. 

Não existe um único método no SDK que leve a sua lista de documentos ou operações como parâmetro de entrada, mas, em vez disso, precisa de criar uma Tarefa para cada operação que pretende executar a granel, e depois simplesmente esperar que eles completem.

Por exemplo, se a sua entrada inicial for uma lista de itens onde cada item tem o seguinte esquema:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Se pretender fazer a importação a granel (semelhante à utilização de BulkExecuteor.BulkImportAsync), precisa de ter chamadas simultâneas para `CreateItemAsync`. Por exemplo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Se pretender fazer *a atualização* a granel (semelhante à utilização de [BulkExecuteor.BulkUpdateAsync),](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)terá de ter chamadas simultâneas para `ReplaceItemAsync` o método após atualizar o valor do artigo. Por exemplo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

E se pretender eliminar *a* granel (semelhante à utilização de [BulkExecuteor.BulkDeleteAsync),](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)é necessário ter chamadas simultâneas para, `DeleteItemAsync`com a `id` tecla e a tecla de partição de cada item. Por exemplo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Estado de resultado de tarefa de captura

Nos exemplos de código anteriores, criámos uma lista simultânea de tarefas e chamámos o `CaptureOperationResponse` método de cada uma dessas tarefas. Este método é uma extensão que nos permite manter um esquema de *resposta semelhante* ao BulkExecutor, capturando quaisquer erros e rastreando a utilização das [unidades de pedido.](request-units.md)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Quando `OperationResponse` o declarado como:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Executar operações em simultâneo

Para acompanhar o âmbito de toda a lista de Tarefas, utilizamos esta classe de ajudante:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

O `ExecuteAsync` método aguardará até que todas as operações estejam concluídas e poderá usá-lo assim:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Estatísticas de captura

O código anterior aguarda até que todas as operações estejam concluídas e calcule as estatísticas necessárias. Estas estatísticas são semelhantes às da resposta [bulkimportresponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)da biblioteca executora a granel.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

Contém: `BulkOperationResponse`

1. O tempo total decurso para processar a lista de operações através de suporte a granel.
1. O número de operações bem sucedidas.
1. O total de unidades de pedido consumidas.
1. Se houver falhas, apresenta uma lista de tuples que contêm a exceção e o item associado para fins de registo e identificação.

## <a name="retry-configuration"></a>Configuração de retry

A biblioteca de executores a `MaxRetryWaitTimeInSeconds` `MaxRetryAttemptsOnThrottledRequests` granel tinha [orientação](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) que mencionava para definir o e de [RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) para `0` delegar o controlo na biblioteca.

Para suporte a granel no .NET SDK, não há nenhum comportamento oculto. Pode configurar as opções de retry diretamente através das [Opções CosmosClientOptions.MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) e [CosmosClientOptions.MaxRetryWaitTimeOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> Nos casos em que as unidades de pedido previstas são muito inferiores às esperadas com base na quantidade de dados, é possível que considere defini-las para valores elevados. A operação a granel demorará mais tempo, mas tem uma maior probabilidade de ser totalmente bem sucedida devido às tentativas mais elevadas.

## <a name="performance-improvements"></a>Melhorias no desempenho

Tal como acontece com outras operações com o .NET SDK, a utilização das APIs de fluxo resulta num melhor desempenho e evita qualquer serialização desnecessária. 

A utilização de APIs de fluxo só é possível se a natureza dos dados que utiliza corresponder à de um fluxo de bytes (por exemplo, fluxos de ficheiros). Nesses casos, `CreateItemStreamAsync`a `ReplaceItemStreamAsync`utilização dos métodos ou `DeleteItemStreamAsync` métodos e o trabalho com `ResponseMessage` (em vez de `ItemResponse`) aumenta o mópode ser alcançado.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os lançamentos .NET SDK, consulte o artigo [da Azure Cosmos DB SDK.](sql-api-sdk-dotnet.md)
* Obtenha o código fonte de [migração](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) completo do GitHub.
* [Amostras a granel adicionais no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
