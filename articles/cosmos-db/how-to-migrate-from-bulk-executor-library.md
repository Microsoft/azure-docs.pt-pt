---
title: Migrar da biblioteca de executor a granel para o suporte a granel em Azure Cosmos DB .NET V3 SDK
description: Saiba como migrar a sua aplicação da utilização da biblioteca de executor a granel para o suporte a granel em Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/24/2020
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: 62a31750fe0c058624c4f69848abb56e7b5095b4
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491024"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migrar da biblioteca de executor a granel para o suporte a granel em Azure Cosmos DB .NET V3 SDK

Este artigo descreve as etapas necessárias para migrar o código de uma aplicação existente que utiliza a biblioteca de [executor a granel .NET](bulk-executor-dot-net.md) para a funcionalidade de suporte a [granel](tutorial-sql-api-dotnet-bulk-import.md) na versão mais recente do .NET SDK.

## <a name="enable-bulk-support"></a>Permitir o suporte a granel

Ativar o suporte a granel no `CosmosClient` caso através da configuração [AllowBulkExecution:](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Criar Tarefas para cada operação

Suporte a granel nas obras .NET SDK aproveitando a [Biblioteca Paralela de Tarefa](/dotnet/standard/parallel-programming/task-parallel-library-tpl) e operações de agrupamento que ocorrem simultaneamente. 

Não existe um único método no SDK que leve a sua lista de documentos ou operações como parâmetro de entrada, mas, pelo contrário, precisa de criar uma Tarefa para cada operação que pretende executar a granel e, em seguida, simplesmente esperar que eles completem.

Por exemplo, se a sua entrada inicial for uma lista de itens onde cada item tem o seguinte esquema:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Se pretender fazer importações a granel (semelhantes à utilização do BulkExecutor.BulkImportAsync), tem de ter chamadas simultâneas para `CreateItemAsync` . Por exemplo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Se pretender fazer *atualizações* a granel (semelhante à utilização [do BulkExecutor.BulkUpdateAsync),](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)tem de ter chamadas simultâneas para `ReplaceItemAsync` o método após a atualização do valor do produto. Por exemplo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

E se pretender fazer *a eliminação* a granel (semelhante à utilização [do BulkExecutor.BulkDeleteAsync),](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)precisa de ter chamadas simultâneas `DeleteItemAsync` para, com a chave e `id` partição de cada item. Por exemplo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Estado de resultado da tarefa de captura

Nos exemplos de código anteriores, criámos uma lista simultânea de tarefas, e chamámos o `CaptureOperationResponse` método em cada uma dessas tarefas. Este método é uma extensão que nos permite manter um *esquema de resposta semelhante* ao BulkExecutor, capturando quaisquer erros e rastreando a utilização das [unidades de pedido](request-units.md).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Se o `OperationResponse` for declarado:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Executar operações simultaneamente

Para acompanhar o âmbito de toda a lista de Tarefas, utilizamos esta classe de ajudante:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

O método aguardará até que `ExecuteAsync` todas as operações estejam concluídas e poderá usá-lo assim:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Estatísticas de captura

O código anterior aguarda até que todas as operações estejam concluídas e calcule as estatísticas necessárias. Estas estatísticas são semelhantes às da [BulkImportResponse](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)da biblioteca de executores a granel.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

`BulkOperationResponse`Contém:

1. O tempo total necessário para processar a lista de operações através de suporte a granel.
1. O número de operações bem sucedidas.
1. O total de unidades de pedido consumidas.
1. Se houver falhas, apresenta uma lista de tuples que contêm a exceção e o item associado para o registo e finalidade de identificação.

## <a name="retry-configuration"></a>Configuração de retíria

A biblioteca de executores a granel tinha [orientações](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) que mencionavam para definir o `MaxRetryWaitTimeInSeconds` e de `MaxRetryAttemptsOnThrottledRequests` [RetryOptions](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) `0` para delegar o controlo na biblioteca.

Para suporte a granel no .NET SDK, não existe nenhum comportamento oculto. Pode configurar as opções de retícula diretamente através das [Opções CosmosClient.MaxRetryAttemptsOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) e [CosmosClientOptions.MaxRetryWaitTimeOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> Nos casos em que as unidades de pedido previstas sejam muito inferiores às esperadas com base na quantidade de dados, talvez deva considerar a definição destes para valores elevados. A operação a granel demorará mais tempo, mas tem uma maior probabilidade de sucesso devido às maiores retréis.

## <a name="performance-improvements"></a>Melhorias de desempenho

Tal como acontece com outras operações com o .NET SDK, a utilização do fluxo APIs resulta num melhor desempenho e evita qualquer serialização desnecessária. 

A utilização de APIs de fluxo só é possível se a natureza dos dados utilizados corresponder à de um fluxo de bytes (por exemplo, streams de ficheiros). Nesses casos, a `CreateItemStreamAsync` `ReplaceItemStreamAsync` utilização, ou `DeleteItemStreamAsync` métodos e o trabalho com `ResponseMessage` (em vez de ) aumenta a `ItemResponse` produção que pode ser alcançada.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os lançamentos .NET SDK, consulte o artigo [da Azure Cosmos DB SDK.](sql-api-sdk-dotnet.md)
* Obtenha o código fonte de [migração](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) completo do GitHub.
* [Amostras adicionais a granel no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)