---
title: 'Azure Cosmos DB SQL API: Java SDK v4 exemplos'
description: Encontre exemplos Java no GitHub para tarefas comuns com a API SQL do Azure Cosmos BD, incluindo operações do CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: afc9633c32174cbe1d94535d68e4c8f85f3d137b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478071"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API: Java SDK v4 exemplos

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

> [!IMPORTANT]  
> Para saber mais sobre Java SDK v4, consulte as notas de lançamento do Azure Cosmos DB Java SDK [v4,](sql-api-sdk-java-v4.md) [repositório Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), [Azure](performance-tips-java-sdk-v4-sql.md)Cosmos DB Java SDK v4 dicas de desempenho , e guia [de resolução de problemas](troubleshoot-java-sdk-v4-sql.md) Azure Cosmos DB Java SDK v4 para obter mais informações. Se está a utilizar uma versão mais antiga do que v4, consulte o guia [Migrador para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) para ajudar a atualizar para v4.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Pode [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): a sua subscrição do Visual Studio dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

As mais recentes aplicações de amostras que realizam operações CRUD e outras operações comuns em recursos DB Azure Cosmos estão incluídas no repositório GitHub [azure-cosmos-java-sql-api-amostras.](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) Este artigo fornece:

* Ligações para as tarefas em cada um dos ficheiros do projeto de Java de exemplo. 
* Ligações para o conteúdo de referência da API relacionada.

**Pré-requisitos**

Precisa do seguinte apra executar esta aplicação de exemplo:

* Java Development Kit 8
* Azure Cosmos DB Java SDK v4

Você pode opcionalmente usar Maven para obter os mais recentes binários Azure Cosmos DB Java SDK v4 para uso no seu projeto. O Maven adiciona automaticamente quaisquer dependências necessárias. Caso contrário, pode transferir diretamente as dependências listadas no ficheiro pom.xml e adicioná-las ao seu caminho de compilação.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Executar os exemplos de aplicações**

Clonar o repositório de exemplo:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

Pode executar as amostras utilizando um IDE (Eclipse, IntelliJ ou VSCODE) ou a partir da linha de comando utilizando o Maven.

Estas variáveis ambientais devem ser definidas

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account primary key
```

a fim de dar às amostras acesso de leitura/escrita à sua conta.

Para executar uma amostra, especifique a sua Classe Principal 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

onde *a amostra.synchronicity.MainClass* pode ser
* crudquickstart.sync.SampleCRUDQuickstart
* crudquickstart.async.SampleCRUDQuickstartAsync
* indexmanagement.sync.SampleIndexManagement
* indexmanagement.async.SampleIndexManagementAsync
* ressaltado.sync.SampleStoredProcedure
* storedprocedure.async.SampleStoredProcedureAsync
* mudar de alimentação. SampleChangeFeedProcessor *(O Changefeed tem apenas uma amostra de async, sem amostra sincronizada.)* ... etc...

> [!NOTE]
> Cada amostra é independente, configura-se automaticamente e limpa-se em seguida. As amostras emitem várias chamadas para criar um `CosmosContainer` . Cada vez que isto é feito, a sua subscrição é cobrada por 1 hora de utilização pelo escalão de desempenho da coleção criada. 
> 
> 

## <a name="database-examples"></a>Exemplos de base de dados
O ficheiro [De amostras CRUD database](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) mostra como executar as seguintes tarefas. Para conhecer as bases de dados do Azure Cosmos antes de executar as seguintes amostras, consulte [Trabalhar com bases de dados, contentores e artigos](account-databases-containers-items.md) conceptuais. 

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L77-L85) | CosmosClient.createDatabaseIfNotExists |
| [Leia uma base de dados por ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L88-L95) | CosmosClient.getDatabase |
| [Ler todas as bases de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L98-L112) | CosmosClient.readAllDatabases |
| [Eliminar uma base de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L115-L123) | CosmosDatabase.delete |

## <a name="collection-examples"></a>Exemplos de coleção
O ficheiro [Recolha CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) mostra como executar as seguintes tarefas. Para conhecer as coleções Azure Cosmos antes de executar as seguintes amostras, consulte [Trabalhar com bases de dados, contentores e artigos](account-databases-containers-items.md) conceptuais.

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L97-L112) | CosmosDatabase.createContainerIfNotExists |
| [Alterar um desempenho configurado de uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L115-L123) | CosmosContainer.replaceProvisionedThroughput |
| [Obtenha uma coleção por ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L126-L133) | CosmosDatabase.getContainer |
| [Ler todas as coleções numa base de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L136-L150) | CosmosDatabase.readAllContainers |
| [Delete a collection (Eliminar uma coleção)](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L153-L161) | CosmosContainer.delete |

## <a name="autoscale-collection-examples"></a>Exemplos de coleção de autoescala

Para saber mais sobre a autoescalação antes de executar estas amostras, dê uma olhada nestas instruções para permitir a autoescalação na sua [conta](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) e nas suas [bases de dados e contentores.](./provision-throughput-autoscale.md)

O ficheiro [de amostras CRUD de escala automática](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) mostra como executar as seguintes tarefas.

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados com produção máxima de autoescalada especificada](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L78-L89) | CosmosClient.createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

O ficheiro [Recolha automática de Amostras CRUD](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) mostra como executar as seguintes tarefas. 

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção com produção máxima de autoescala especificada](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L97-L110) | CosmosDatabase.createContainerIfNotExists |
| [Alterar a produção máxima de autoescala configurada de uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L113-L120) | CosmosContainer.replaceThroughput |
| [Leia a configuração de produção de escala automática de uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L122-L133) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Exemplos de recolha de armazenamento analítico

O ficheiro [Analítico de Recolha CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) mostra como executar as seguintes tarefas. Para conhecer as coleções Azure Cosmos antes de executar as seguintes amostras, leia sobre Azure Cosmos DB Synapse e Analytical Store.

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L93-L108) | CosmosDatabase.createContainerIfNotExists |

## <a name="document-examples"></a>Exemplos de documento
O ficheiro [Amostras CRUD documentais](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) mostra como executar as seguintes tarefas. Para conhecer os documentos do Azure Cosmos antes de executar as [seguintes amostras, consulte Trabalhar com bases de dados, contentores e artigos conceptuais.](account-databases-containers-items.md)

| Tarefa | Referência da API |
| --- | --- |
| [Criar um documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L133-L147) | CosmosContainer.createItem |
| [Leia um documento por ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L179-L193) | CosmosContainer.readItem |
| [Consultar documentos](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176) | CosmosContainer.queryItems |
| [Substituir um documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L195-L210) | CosmosContainer.replaceItem |
| [Upsert um documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L212-L2225) | CosmosContainer.upsertItem |
| [Eliminar um documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L303-L310) | CosmosContainer.deleteItem |
| [Substitua um documento por verificação ETag condicional](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L227-L264) | AcessoCondition.setType<br>AcessoCondição.setCondição |
| [Ler documento apenas se este for alterado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L266-L300) | AcessoCondition.setType<br>AcessoCondição.setCondição |

## <a name="indexing-examples"></a>Exemplos de indexação
O ficheiro [Recolha CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) mostra como executar as seguintes tarefas. Para aprender sobre a indexação em Azure Cosmos DB antes de executar as seguintes amostras, consulte [políticas de indexação,](index-policy.md) [tipos de indexação](index-overview.md#index-kinds)e [artigos conceptuais de caminhos de indexação.](index-policy.md#include-exclude-paths) 

| Tarefa | Referência da API |
| --- | --- |
| Excluir um documento do índice | ExcluídoSIndex<br>IndexingPolicy |
| Use indexação preguiçosa | IndexingPolicy.IndexingMode |
| [Incluir caminhos de documentos especificados no índice](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L145-L148) | IndexingPolicy.IncludedPaths |
| [Excluir caminhos de documentos especificados do índice](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L150-L153) | IndexingPolicy.ExcludedPaths |
| [Criar um índice composto](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L171-L186) | IndexingPolicy.setCompositeIndexes<br>Caminho composto |
| Forçar uma operação de análise de intervalo num caminho com índice de hash | FeedOptions.EnableScanInQuery |
| Use índices de gama em Cordas | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Realizar uma transformação de índice | - |
| [Criar um índice geoespacial](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L157-L166) | IndexingPolicy.setSpatialIndexes<br>EspacialSpec<br>Tipo espacial |

Para obter mais informações sobre a indexação, veja [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Exemplos de consultas
O ficheiro [Amostras de Consulta](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) mostra como fazer as seguintes tarefas utilizando a gramática de consulta SQL. Para saber mais sobre a referência de consulta SQL em Azure Cosmos DB antes de executar as [seguintes amostras, consulte exemplos de consulta SQL para Azure Cosmos DB](./sql-query-getting-started.md). 

| Tarefa | Referência da API |
| --- | --- |
| [Consultar todos os documentos](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L210-L214) | CosmosContainer.queryItems |
| [Consultar igualdade com ==](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L291-L295) | CosmosContainer.queryItems |
| [Consultar desigualdade com != e NOT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L297-L305) | CosmosContainer.queryItems |
| [Consultar com operadores de intervalo como >, <, >=, <=](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L307-L312) | CosmosContainer.queryItems |
| [Consultar com operadores de intervalo em relação a cadeias](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L314-L319) | CosmosContainer.queryItems |
| [Consulta com ORDER BY](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L321-L326) | CosmosContainer.queryItems |
| [Consulta com DISTINCT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L328-L333) | CosmosContainer.queryItems |
| [Consulta com funções agregadas](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L335-L343) | CosmosContainer.queryItems |
| [Trabalhar com subdocumentos](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L345-L353) | CosmosContainer.queryItems |
| [Consultar com Associações dentro de documentos](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L355-L377) | CosmosContainer.queryItems |
| [Consulta com operadores de cordas, matemática e matrizes](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L379-L390) | CosmosContainer.queryItems |
| [Consultar com SQL parametrizado com SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421) |CosmosContainer.queryItems |
| [Consultar com paginação explícita](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216-L266) | CosmosContainer.queryItems |
| [Consultar coleções particionadas em paralelo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L268-L289) | CosmosContainer.queryItems |
| Consulta com ORDER BY para coleções divididas | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Exemplos de feed de alterações 
O ficheiro [Change Feed Processor Sample](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) mostra como fazer as seguintes tarefas. Para saber mais sobre a mudança de feed em Azure Cosmos DB antes de executar as seguintes amostras, consulte [ler Azure Cosmos DB alterar feed feed](read-change-feed.md) e alterar processador de [feed](change-feed-processor.md).

| Tarefa | Referência da API |
| --- | --- |
| [Funcionalidade básica de feed de alteração](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Leia o feed de mudança a partir de um momento específico | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Leia o feed de mudança desde o início](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) | - |

## <a name="server-side-programming-examples"></a>Exemplos de programação do lado do servidor

O ficheiro [de amostra de procedimento armazenado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) mostra como fazer as seguintes tarefas. Para saber mais sobre a programação do lado do servidor no Azure Cosmos DB antes de executar as [seguintes amostras, consulte procedimentos armazenados, gatilhos e funções definidas pelo utilizador](stored-procedures-triggers-udfs.md).

| Tarefa | Referência da API |
| --- | --- |
| [Criar um procedimento armazenado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L132-L151) | CosmosScripts.createStoredProcedure |
| [Executar um procedimento armazenado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L167-L181) | CosmosStoredProcedure.exefofo |
| [Eliminar um procedimento armazenado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L183-L193) | CosmosStoredProcedure.delete |

## <a name="user-management-examples"></a>Exemplos de gestão de utilizadores
O ficheiro Amostra de Gestão do Utilizador mostra como fazer as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| Criar um utilizador | - |
| Definir permissões numa coleção ou documento | - |
| Obter uma lista de permissões de um utilizador |- |
