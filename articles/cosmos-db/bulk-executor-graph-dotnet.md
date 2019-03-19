---
title: Utilizar a biblioteca .NET do BulkExecutor de grafos para realizar operações em massa na API Gremlin do Azure Cosmos DB
description: Saiba como utilizar a biblioteca do BulkExecutor para importar em massa dados de grafos para um contentor da API Gremlin do Azure Cosmos DB.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: lbosq
ms.reviewer: sngun
ms.openlocfilehash: 5e88602aa3b983e1533248253d53967f39e6b5eb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837529"
---
# <a name="using-the-graph-bulkexecutor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Utilizar a biblioteca .NET do BulkExecutor de grafos para realizar operações em massa na API Gremlin do Azure Cosmos DB

Este tutorial disponibiliza instruções para utilizar a biblioteca .NET do BulkExecutor do Azure Cosmos DB para importar e atualizar objetos de grafo para um contentor da API Gremlin do Azure Cosmos DB. Este processo utiliza a classe Graph na [biblioteca do BulkExecutor](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) para criar objetos Vertex e Edge programaticamente para, depois, inserir múltiplos dos mesmos por pedido de rede. Este comportamento é configurável através da biblioteca do BulkExecutor para fazer a melhor utilização dos recursos de bases de dados e da memória local.

Por oposição ao envio de consultas do Gremlin para uma base de dados, em que o comando é avaliado e depois executado individualmente, a utilização da biblioteca do BulkExecutor exige, ao invés, criar e validar os objetos localmente. Depois de criar os objetos, a biblioteca permite-lhe enviar objetos de grafo para o serviço de bases de dados sequencialmente. Ao utilizar este método, é possível aumentar as velocidades de ingestão de dados até 100x, o que faz deste um método ideal para migrações de dados iniciais ou operações de movimento de dados periódicas. Visite a página do GitHub da [aplicação de exemplo BulkExecutor de Grafo do Azure Cosmos DB](https://aka.ms/graph-bulkexecutor-sample) para saber mais.

## <a name="bulk-operations-with-graph-data"></a>Operações em massa com dados de grafos

A [biblioteca do BulkExecutor](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) contém o espaço de nomes `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` para proporcionar funcionalidades para a criação e importação de objetos de grafo. 

O seguinte processo descreve de que forma é que a migração de dados pode ser utilizada para um contentor de API Gremlin:
1. Obtenha registos da origem de dados.
2. Construa os objetos `GremlinVertex` e `GremlinEdge` a partir dos registos obtidos e adicione-os a uma estrutura de dados `IEnumerable`. Nesta parte da aplicação, deve ser implementada a lógica para detetar e adicionar relações, caso a origem de dados não seja uma base de dados de grafo.
3. Utilize o [método Graph BulkImportAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet) para inserir os objetos de grafo na coleção.

Este mecanismo melhora a eficácia da migração de dados quando comparado com a utilização de um cliente do Gremlin. Esta melhoria acontece porque a inserção de dados com o Gremlin requer que a aplicação envie consultas uma de cada vez, as quais têm de ser validadas, avaliadas e executadas para criar os dados. A biblioteca BulkExecutor processa a validação na aplicação e envia múltiplos objetos de grafo ao mesmo tempo para cada pedido de rede.

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

Para obter mais informações sobre os parâmetros da biblioteca do BulkExecutor, veja o tópico [BulkImportData to Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#bulk-import-data-to-azure-cosmos-db) (Importar dados em massa para o Azure Cosmos DB).

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
> O utilitário BulkExecutor não verifica automaticamente a existência de vértices antes de adicionar arestas. A existência de vértices tem de ser validada na aplicação antes de executar as tarefas de BulkImport.

## <a name="sample-application"></a>Aplicação de exemplo

### <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2017 com a carga de trabalho de desenvolvimento do Azure Pode começar a utilizar o [Visual Studio 2017 Community Edition](https://visualstudio.microsoft.com/downloads/) gratuitamente.
* Uma subscrição do Azure. Pode criar uma [conta do Azure gratuita aqui](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Em alternativa, pode utilizar [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) para criar uma conta de base de dados do Cosmos DB sem ter uma subscrição do Azure
* Uma base de dados da API Gremlin do Azure Cosmos DB com uma **coleção ilimitada**. Este guia mostra como começar a utilizar a [API Gremlin do Azure Cosmos DB em .NET](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Git. Para obter mais informações, veja a página[Git Downloads](https://git-scm.com/downloads) (Transferências do Git).

### <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo
Neste tutorial, vamos seguir os passos para utilizar o [exemplo de BulkExecutor de Grafo do Azure Cosmos DB](https://aka.ms/graph-bulkexecutor-sample) alojado no GitHub. Esta aplicação consiste numa solução .NET que gera, aleatoriamente, objetos de vértices e arestas e, depois, executa inserções em massa na conta da base de dados de grafo. Para obter a aplicação, execute o comando `git clone`, abaixo:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Este repositório contém o exemplo GraphBulkExecutor com os seguintes ficheiros:

Ficheiro|Descrição
---|---
`App.config`|É aqui que é especificada a aplicação e os parâmetros específicos da mesma. Este ficheiro deve ser modificado primeiro para ligar à base de dados e às coleções de destino.
`Program.cs`| Este ficheiro contém a lógica subjacente à criação da coleção `DocumentClient` e processa as limpezas e envia os pedidos de BulkExecutor.
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

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre os detalhes do pacote Nuget e notas da biblioteca de .NET de executor em massa de versão, consulte [em massa detalhes sobre o SDK executor](sql-api-sdk-bulk-executor-dot-net.md). 
* Veja [Performance Tips](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips) (Sugestões de Desempenho) para otimizar ainda mais a utilização de BulkExecutor.
* Reveja o artigo [BulkExecutor.Graph Reference](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) (Referência de BulkExecutor.Graph) para obter mais detalhes sobre as classes e os métodos definidos neste espaço de nomes.
