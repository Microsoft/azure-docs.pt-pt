---
title: Utilize o executor a granel de gráficos .NET library com Azure Cosmos DB Gremlin API
description: Aprenda a usar a biblioteca de executores a granel para importar massivamente dados de gráficos num recipiente Azure Cosmos DB Gremlin API.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 05/28/2019
ms.author: jasonh
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 53c770bb8cc9d7a80ae7d11b6b1c089fcc9355da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565637"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Utilização do executor a granel de gráficos .NET library para realizar operações a granel em Azure Cosmos DB Gremlin API

Este tutorial fornece instruções sobre a utilização do executor a granel da Azure CosmosDB .NET library para importar e atualizar objetos gráficos num recipiente API Azure Cosmos DB Gremlin. Este processo faz uso da classe Graph na biblioteca de [executores](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) a granel para criar objetos Vertex e Edge programáticamente para, em seguida, inserir vários deles por pedido de rede. Este comportamento é configurável através da biblioteca do executor a granel para fazer o melhor uso da base de dados e dos recursos de memória locais.

Em oposição ao envio de consultas gremlin para uma base de dados, onde o comando é avaliado e executado um de cada vez, usando a biblioteca de executor a granel exigirá, em vez disso, criar e validar os objetos localmente. Depois de criar os objetos, a biblioteca permite-lhe enviar objetos de grafo para o serviço de bases de dados sequencialmente. Ao utilizar este método, é possível aumentar as velocidades de ingestão de dados até 100x, o que faz deste um método ideal para migrações de dados iniciais ou operações de movimento de dados periódicas. Saiba mais visitando a página GitHub da aplicação de amostra de [execução a granel Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started).

## <a name="bulk-operations-with-graph-data"></a>Operações em massa com dados de grafos

A [biblioteca de executores a granel](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet&preserve-view=true) contém um espaço de nome para fornecer `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` funcionalidades para criar e importar objetos gráficos. 

O seguinte processo descreve de que forma é que a migração de dados pode ser utilizada para um contentor de API Gremlin:
1. Obtenha registos da origem de dados.
2. Construa os objetos `GremlinVertex` e `GremlinEdge` a partir dos registos obtidos e adicione-os a uma estrutura de dados `IEnumerable`. Nesta parte da aplicação, deve ser implementada a lógica para detetar e adicionar relações, caso a origem de dados não seja uma base de dados de grafo.
3. Utilize o [método Graph BulkImportAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet&preserve-view=true) para inserir os objetos de grafo na coleção.

Este mecanismo melhora a eficácia da migração de dados quando comparado com a utilização de um cliente do Gremlin. Esta melhoria acontece porque a inserção de dados com o Gremlin requer que a aplicação envie consultas uma de cada vez, as quais têm de ser validadas, avaliadas e executadas para criar os dados. A biblioteca de executores a granel tratará da validação na aplicação e enviará vários objetos gráficos de cada vez para cada pedido de rede.

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

Para obter mais informações sobre os parâmetros da biblioteca do executor a granel, consulte o [bulkImportData para o tópico DB Azure Cosmos](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account).

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
> O utilitário do executor a granel não verifica automaticamente se existem vértices existentes antes de adicionar Edges. A existência de vértices tem de ser validada na aplicação antes de executar as tarefas de BulkImport.

## <a name="sample-application"></a>Aplicação de exemplo

### <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2019 com a carga de trabalho de desenvolvimento Azure. Pode começar com a [Edição Comunitária do Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) gratuitamente.
* Uma subscrição do Azure. Pode criar uma [conta do Azure gratuita aqui](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Em alternativa, pode criar uma conta de base de dados cosmos com [Try Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure.
* Uma base de dados da API Gremlin do Azure Cosmos DB com uma **coleção ilimitada**. Este guia mostra como começar a utilizar a [API Gremlin do Azure Cosmos DB em .NET](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Git. Para obter mais informações, veja a página[Git Downloads](https://git-scm.com/downloads) (Transferências do Git).

### <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo
Neste tutorial, vamos seguir os passos para começar usando a amostra de [executor a granel Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started) acolhida no GitHub. Esta aplicação consiste numa solução .NET que gera, aleatoriamente, objetos de vértices e arestas e, depois, executa inserções em massa na conta da base de dados de grafo. Para obter a aplicação, execute o comando `git clone`, abaixo:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Este repositório contém o exemplo GraphBulkExecutor com os seguintes ficheiros:

Ficheiro|Descrição
---|---
`App.config`|É aqui que é especificada a aplicação e os parâmetros específicos da mesma. Este ficheiro deve ser modificado primeiro para ligar à base de dados e às coleções de destino.
`Program.cs`| Este ficheiro contém a lógica por trás da criação da `DocumentClient` coleção, manuseamento das limpezas e envio dos pedidos do executor a granel.
`Util.cs`| Este ficheiro contém uma classe de programa auxiliar que inclui a lógica subjacente à geração de dados de teste e verifica se a base de dados e a coleção existem.

No ficheiro `App.config`, podem ser fornecidos os seguintes valores de configuração:

Definições|Descrição
---|---
`EndPointUrl`|Este é o **ponto final do SDK .NET** que está disponível no painel Descrição Geral da sua conta de base de dados da API Gremlin do Azure Cosmos DB. Tem o formato `https://your-graph-database-account.documents.azure.com:443/`.
`AuthorizationKey`|Esta é a chave Primária ou Secundária que aparece na sua conta do Azure Cosmos DB. Saiba mais sobre a proteção do acesso a dados do Azure Cosmos DB em [Securing Access to Azure Cosmos DB data](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#primary-keys) (Proteger o Acesso a dados do Azure Cosmos DB)
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

* Para saber mais sobre os detalhes do pacote NuGet e lançar notas do executor a granel .NET library, consulte [detalhes do executor a granel SDK](sql-api-sdk-bulk-executor-dot-net.md). 
* Consulte as [Dicas de Desempenho](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips) para otimizar ainda mais a utilização do executor a granel.
* Reveja o artigo [BulkExecutor.Graph Reference](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet&preserve-view=true) (Referência de BulkExecutor.Graph) para obter mais detalhes sobre as classes e os métodos definidos neste espaço de nomes.