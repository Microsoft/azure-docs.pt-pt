---
title: Usando a biblioteca .NET do executor em massa do grafo para executar operações em massa no Azure Cosmos DB API do Gremlin
description: Saiba como usar a biblioteca de executores em massa para importar dados de grafo maciçamente em um contêiner de API Azure Cosmos DB Gremlin.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: lbosq
ms.reviewer: sngun
ms.openlocfilehash: 35f42f3e222767d9d201d9948581151ae3cb5127
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327180"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Usando a biblioteca .NET do executor em massa do grafo para executar operações em massa no Azure Cosmos DB API do Gremlin

Este tutorial fornece instruções sobre como usar a biblioteca .NET do executor em massa do Azure CosmosDB para importar e atualizar objetos de grafo em um contêiner de API Azure Cosmos DB Gremlin. Esse processo usa a classe de grafo na biblioteca de [executores em massa](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) para criar objetos de vértice e de borda programaticamente para, em seguida, inserir vários deles por solicitação de rede. Esse comportamento é configurável por meio da biblioteca de executores em massa para fazer uso ideal dos recursos de memória local e de banco de dados.

Em vez de enviar consultas Gremlin para um banco de dados, em que o comando é avaliado e, em seguida, executado um de cada vez, usar a biblioteca de executores em massa exigirá criar e validar os objetos localmente. Depois de criar os objetos, a biblioteca permite-lhe enviar objetos de grafo para o serviço de bases de dados sequencialmente. Ao utilizar este método, é possível aumentar as velocidades de ingestão de dados até 100x, o que faz deste um método ideal para migrações de dados iniciais ou operações de movimento de dados periódicas. Saiba mais visitando a página GitHub do aplicativo de [exemplo do executor em massa do Azure Cosmos DB Graph](https://aka.ms/graph-bulkexecutor-sample).

## <a name="bulk-operations-with-graph-data"></a>Operações em massa com dados de grafos

A [biblioteca de executores em massa](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) contém um namespace `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` para fornecer funcionalidade para criar e importar objetos de grafo. 

O seguinte processo descreve de que forma é que a migração de dados pode ser utilizada para um contentor de API Gremlin:
1. Obtenha registos da origem de dados.
2. Construa os objetos `GremlinVertex` e `GremlinEdge` a partir dos registos obtidos e adicione-os a uma estrutura de dados `IEnumerable`. Nesta parte da aplicação, deve ser implementada a lógica para detetar e adicionar relações, caso a origem de dados não seja uma base de dados de grafo.
3. Utilize o [método Graph BulkImportAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet) para inserir os objetos de grafo na coleção.

Este mecanismo melhora a eficácia da migração de dados quando comparado com a utilização de um cliente do Gremlin. Esta melhoria acontece porque a inserção de dados com o Gremlin requer que a aplicação envie consultas uma de cada vez, as quais têm de ser validadas, avaliadas e executadas para criar os dados. A biblioteca de executores em massa tratará a validação no aplicativo e enviará vários objetos de grafo por vez para cada solicitação de rede.

### <a name="creating-vertices-and-edges"></a>Criar Vértices e Arestas

`GraphBulkExecutor` fornece o método `BulkImportAsync` que precisa de uma lista `IEnumerable` de objetos `GremlinVertex` ou `GremlinEdge`, ambos definidos no espaço de nomes `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`. No exemplo, separámos as arestas e os vértices em duas tarefas de importação do BulkExecutor. Veja o exemplo abaixo:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Para obter mais informações sobre os parâmetros da biblioteca de executores em massa, consulte o [tópico BulkImportData to Azure Cosmos DB](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account).

O payload tem de ser instanciado em objetos `GremlinVertex` e `GremlinEdge`. Eis como estes objetos podem ser criados:

**Vértices**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Arestas**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> O utilitário executor em massa não verifica automaticamente os vértices existentes antes de adicionar bordas. A existência de vértices tem de ser validada na aplicação antes de executar as tarefas de BulkImport.

## <a name="sample-application"></a>Aplicação de exemplo

### <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2019 com a carga de trabalho de desenvolvimento do Azure. Você pode começar a usar o [Visual Studio 2019 Community Edition](https://visualstudio.microsoft.com/downloads/) gratuitamente.
* Uma subscrição do Azure. Pode criar uma [conta do Azure gratuita aqui](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Como alternativa, você pode criar uma conta de banco de dados cosmos com [Try Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure.
* Uma base de dados da API Gremlin do Azure Cosmos DB com uma **coleção ilimitada**. Este guia mostra como começar a utilizar a [API Gremlin do Azure Cosmos DB em .NET](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Git. Para obter mais informações, veja a página[Git Downloads](https://git-scm.com/downloads) (Transferências do Git).

### <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo
Neste tutorial, seguiremos as etapas de introdução usando o [exemplo de executor em massa de Azure Cosmos DB Graph](https://aka.ms/graph-bulkexecutor-sample) hospedado no github. Esta aplicação consiste numa solução .NET que gera, aleatoriamente, objetos de vértices e arestas e, depois, executa inserções em massa na conta da base de dados de grafo. Para obter a aplicação, execute o comando `git clone`, abaixo:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Este repositório contém o exemplo GraphBulkExecutor com os seguintes ficheiros:

Ficheiros|Descrição
---|---
`App.config`|É aqui que é especificada a aplicação e os parâmetros específicos da mesma. Este ficheiro deve ser modificado primeiro para ligar à base de dados e às coleções de destino.
`Program.cs`| Esse arquivo contém a lógica por trás da criação da coleção `DocumentClient`, tratando as limpezas e enviando as solicitações de executor em massa.
`Util.cs`| Este ficheiro contém uma classe de programa auxiliar que inclui a lógica subjacente à geração de dados de teste e verifica se a base de dados e a coleção existem.

No ficheiro `App.config`, podem ser fornecidos os seguintes valores de configuração:

Definição|Descrição
---|---
`EndPointUrl`|Este é o **ponto final do SDK .NET** que está disponível no painel Descrição Geral da sua conta de base de dados da API Gremlin do Azure Cosmos DB. Tem o formato `https://your-graph-database-account.documents.azure.com:443/`.
`AuthorizationKey`|Esta é a chave Primária ou Secundária que aparece na sua conta do Azure Cosmos DB. Saiba mais sobre a proteção do acesso a dados do Azure Cosmos DB em [Securing Access to Azure Cosmos DB data](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#master-keys) (Proteger o Acesso a dados do Azure Cosmos DB)
`DatabaseName`, `CollectionName`|Estes são os **nomes da base de dados e da coleção de destino**. Se `ShouldCleanupOnStart` estiver definido como `true`, estes valores, juntamente com `CollectionThroughput`, serão utilizados para remover essa base de dados e essa coleção e criar uma base de dados e uma coleção nova. Do mesmo modo, se `ShouldCleanupOnFinish` estiver definido como `true`, serão utilizados para eliminar a base de dados assim que a ingestão terminar. Tenha em conta que a coleção de destino tem de ser uma **coleção ilimitada**.
`CollectionThroughput`|É utilizado para criar uma coleção nova se a opção `ShouldCleanupOnStart` estiver definida como `true`.
`ShouldCleanupOnStart`|Remove a conta da base de dados e as coleções antes de o programa ser executado e, depois, criar uma conta e uma coleção com os valores `DatabaseName`, `CollectionName` e `CollectionThroughput`.
`ShouldCleanupOnFinish`|Remove a conta da base de dados e as coleções com `DatabaseName` e `CollectionName` especificados depois de o programa ser executado.
`NumberOfDocumentsToImport`|Determina o número de vértices e arestas de teste que vão ser gerados no exemplo. Este número será aplicado tanto aos vértices, como às arestas.
`NumberOfBatches`|Determina o número de vértices e arestas de teste que vão ser gerados no exemplo. Este número será aplicado tanto aos vértices, como às arestas.
`CollectionPartitionKey`|Será utilizada para criar os vértices e as arestas de teste, em que esta propriedade vai ser atribuída automaticamente. Também será utilizada ao recriar a base de dados e a coleção se a opção `ShouldCleanupOnStart` estiver definida como `true`.

### <a name="run-the-sample-application"></a>Executar o exemplo de aplicação

1. Adicione os parâmetros específicos da configuração da base de dados em `App.config`, que será utilizado para criar uma instância de DocumentClient. Se a base de dados e o contentor ainda não tiverem sido criados, sê-lo-ão automaticamente.
2. Execute a aplicação. `BulkImportAsync` vai ser chamado duas vezes, uma para importar Vértices e outra para importar Arestas. Se algum dos objetos gerar um erro quando for inserido, será adicionado a `.\BadVertices.txt` ou a `.\BadEdges.txt`.
3. Consulte a base de dados de grafo para avaliar os resultados. Se a opção `ShouldCleanupOnFinish` estiver definida como verdadeira, a base de dados será eliminada de forma automática.

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre os detalhes do pacote NuGet e notas de versão da biblioteca .NET do executor em massa, consulte [detalhes do SDK do executor em massa](sql-api-sdk-bulk-executor-dot-net.md). 
* Confira as [dicas de desempenho](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips) para otimizar ainda mais o uso do executor em massa.
* Reveja o artigo [BulkExecutor.Graph Reference](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) (Referência de BulkExecutor.Graph) para obter mais detalhes sobre as classes e os métodos definidos neste espaço de nomes.
