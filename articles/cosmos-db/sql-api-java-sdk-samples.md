---
title: 'Azure Cosmos DB SQL API: Java SDK v4 exemplos'
description: Encontre exemplos Java no GitHub para tarefas comuns com a API SQL do Azure Cosmos BD, incluindo operações do CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: db291b735ed6456a007446ad23373ff25e806aab
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727168"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API: Java SDK v4 exemplos

> [!div class="op_single_selector"]
> * [.NET V2 SDK Exemplos](sql-api-dotnet-samples.md)
> * [.NET V3 SDK Exemplos](sql-api-dotnet-v3sdk-samples.md)
> * [Exemplos Java V4 SDK](sql-api-java-sdk-samples.md)
> * [Exemplos de Node.js](sql-api-nodejs-samples.md)
> * [Exemplos python](sql-api-python-samples.md)
> * [Galeria de Exemplo de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Para saber mais sobre java SDK v4, consulte as notas de [lançamento](sql-api-sdk-java-v4.md)do Azure Cosmos DB Java SDK v4 , [repositório Maven,](https://mvnrepository.com/artifact/com.azure/azure-cosmos)Dicas de [desempenho](performance-tips-java-sdk-v4-sql.md)do Azure Cosmos DB Java SDK v4 e Azure Cosmos DB Java SDK v4 guia de resolução de [problemas](troubleshoot-java-sdk-v4-sql.md) para mais informações. Se está a utilizar atualmente uma versão mais antiga do que a v4, consulte o guia [Migrate to Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) para ajudar a atualizar para v4.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Pode [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): a sua subscrição do Visual Studio dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

As mais recentes aplicações de amostra que realizam operações crud e outras operações comuns nos recursos da Azure Cosmos DB estão incluídas no repositório GitHub [azure-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) GitHub. Este artigo fornece:

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

Pode executar as amostras utilizando um IDE (Eclipse, IntelliJ ou VSCODE) ou a partir da linha de comando utilizando maven.

Estas variáveis ambientais devem ser definidas

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account master key
```

a fim de dar às amostras acesso de leitura/escrita à sua conta.

Para executar uma amostra, especifique a sua Classe Principal 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

onde *a amostra.sincronidade.MainClass* pode ser
* crudquickstart.sync.SampleCRUDQuickstart
* crudquickstart.async.SampleCRUDQuickstartAsync
* indexmanagement.sync.SampleIndexManagement
* indexmanagement.async.SampleIndexManagementAsync
* procedimento armazenado.sync.SampleStoredProcedure
* procedimento armazenado.async.SampleStoredProcedureAsync
* changefeed. SampleChangeFeedProcessor *(Changefeed tem apenas uma amostra de asincronização, nenhuma amostra de sincronização.)* ... etc...

> [!NOTE]
> Cada amostra é independente, configura-se automaticamente e limpa-se em seguida. As amostras emitem várias chamadas para criar um `CosmosContainer` . Cada vez que isto é feito, a sua subscrição é cobrada por 1 hora de utilização pelo escalão de desempenho da coleção criada. 
> 
> 

## <a name="database-examples"></a>Exemplos de base de dados
O ficheiro [Database CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) mostra como executar as seguintes tarefas. Para conhecer as bases de dados da Azure Cosmos antes de executar as seguintes amostras, consulte Trabalhar com bases de [dados, contentores e artigos conceptuais.](databases-containers-items.md) 

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L76-L84) | CosmosClient.createDatabaseIfNotExists |
| [Leia uma base de dados por ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L86-L94) | CosmosClient.getDatabase |
| [Ler todas as bases de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L96-L111) | CosmosClient.readAllDatabases |
| [Eliminar uma base de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L113-L122) | CosmosDatabase.delete |

## <a name="collection-examples"></a>Exemplos de coleção
O ficheiro [Amostras CRUD collection](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) mostra como executar as seguintes tarefas. Para conhecer as coleções da Azure Cosmos antes de executar as seguintes amostras, consulte Trabalhar com bases de [dados, contentores e artigos conceptuais.](databases-containers-items.md)

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L94-106) | CosmosDatabase.createContainerIfNotExists |
| [Alterar um desempenho configurado de uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L108-116) | CosmosContainer.replaceProvisionedThroughput |
| [Obtenha uma coleção por ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L118-126) | CosmosDatabase.getContainer |
| [Ler todas as coleções numa base de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L128-143) | CosmosDatabase.readAllContainers |
| [Delete a collection (Eliminar uma coleção)](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L145-154) | CosmosContainer.delete |

## <a name="autoscale-collection-examples"></a>Exemplos de recolha de escala automática

Para saber mais sobre a escala automática antes de executar estas amostras, dê uma olhada nestas instruções para permitir a escala automática na sua [conta](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) e nas suas bases de [dados e contentores](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale).

O ficheiro [de amostras CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) de base de dados de escala automática mostra como executar as seguintes tarefas.

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma base de dados com uma potência máxima de escala automática especificada](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L76-L87) | CosmosClient.createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

O ficheiro [de amostras CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) de recolha automática mostra como executar as seguintes tarefas. 

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção com uma potência máxima de escala automática especificada](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L95-L107) | CosmosDatabase.createContainerIfNotExists |
| [Alterar a potência máxima configurada de uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L109-L117) | CosmosContainer.replaceThroughput |
| [Leia a configuração de entrada em escala automática de uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L119-L130) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Exemplos de recolha de armazenamento analítico

O ficheiro [Amostras CRUD Da Recolha de Armazenamento Analítico](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) mostra como executar as seguintes tarefas. Para conhecer as coleções Azure Cosmos antes de executar as seguintes amostras, leia sobre a Azure Cosmos DB Synapse e a Analytical Store.

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L94-L109) | CosmosDatabase.createContainerIfNotExists |

## <a name="document-examples"></a>Exemplos de documento
O ficheiro [amostras CRUD](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) do documento mostra como executar as seguintes tarefas. Para conhecer os documentos do Azure Cosmos antes de executar as seguintes amostras, consulte Trabalhar com bases de [dados, contentores e artigos conceptuais.](databases-containers-items.md)

| Tarefa | Referência da API |
| --- | --- |
| [Criar um documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L127-L141) | CosmosContainer.createItem |
| [Leia um documento por ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L143-L154) | CosmosContainer.readItem |
| [Ler todos os documentos numa coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L156-171) | CosmosContainer.readAllItems |
| [Consultar documentos](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L173-L187) | CosmosContainer.consultaItems |
| [Substituir um documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L189-L204) | CosmosContainer.replaceItem |
| [Upsert um documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L206-L219) | CosmosContainer.upsertItem |
| [Eliminar um documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L302-L310) | CosmosContainer.deleteItem |
| [Substitua um documento por verificação etag condicional](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L221-L261) | AccessCondition.setType<br>AccessCondition.setCondition |
| [Ler documento apenas se este for alterado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L263-L300) | AccessCondition.setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>Exemplos de indexação
O ficheiro [Amostras CRUD collection](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) mostra como executar as seguintes tarefas. Para aprender sobre indexação em Azure Cosmos DB antes de executar as seguintes amostras, consulte [políticas de indexação,](index-policy.md)tipos de [indexação](index-types.md)e indexação de [caminhos](index-paths.md) conceptuais. 

| Tarefa | Referência da API |
| --- | --- |
| Excluir um documento do índice | Índice excluído<br>IndexingPolicy |
| Use indexação preguiçosa | IndexaçãoPolítica.IndexingMode |
| [Incluir caminhos de documentos especificados no índice](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L142-L147) | IndexingPolicy.IncludedPaths |
| [Excluir caminhos de documentos especificados do índice](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L149-L154) | IndexingPolicy.ExcludedPaths |
| [Criar um índice composto](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L170-L188) | IndexingPolicy.setCompositeIndexes<br>Caminho Compósito |
| Forçar uma operação de análise de intervalo num caminho com índice de hash | FeedOptions.EnableScanInQuery |
| Use índices de gama em Cordas | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Realizar uma transformação de índice | - |
| [Criar um índice geoespacial](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L156-L168) | IndexaçãoPolítica.setSpatialIndexes<br>Espectro Espacial<br>Tipo Espacial |

Para obter mais informações sobre a indexação, veja [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Exemplos de consultas
O ficheiro [Query Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) mostra como fazer as seguintes tarefas utilizando a gramática de consulta SQL. Para saber sobre a referência à consulta SQL em Azure Cosmos DB antes de executar as seguintes amostras, consulte exemplos de [consulta SQL para Azure Cosmos DB](how-to-sql-query.md). 

| Tarefa | Referência da API |
| --- | --- |
| [Consultar todos os documentos](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L194-L198) | CosmosContainer.consultaItems |
| [Consultar igualdade com ==](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L279-183) | CosmosContainer.consultaItems |
| [Consultar desigualdade com != e NOT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L285-L293) | CosmosContainer.consultaItems |
| [Consultar com operadores de intervalo como >, <, >=, <=](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L295-L300) | CosmosContainer.consultaItems |
| [Consultar com operadores de intervalo em relação a cadeias](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L302-L307) | CosmosContainer.consultaItems |
| [Consulta com ORDEM POR](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L309-L314) | CosmosContainer.consultaItems |
| [Consulta com DISTINTO](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L316-L321) | CosmosContainer.consultaItems |
| [Consulta com funções agregadas](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L323-L331) | CosmosContainer.consultaItems |
| [Trabalhar com subdocumentos](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L333-L341) | CosmosContainer.consultaItems |
| [Consultar com Associações dentro de documentos](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L343-L365) | CosmosContainer.consultaItems |
| [Consulta com cordas, matemática e operadores de matriz](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L367-L378) | CosmosContainer.consultaItems |
| [Consultar com SQL parametrizado com SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L380-L409) |CosmosContainer.consultaItems |
| [Consultar com paginação explícita](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L200-L254) | CosmosContainer.consultaItems |
| [Consultar coleções particionadas em paralelo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L256-L277) | CosmosContainer.consultaItems |
| Consulta com ORDER BY para coleções divididas | CosmosContainer.consultaItems |

## <a name="change-feed-examples"></a>Exemplos de feed de alterações 
O ficheiro de amostra do [processador change feed](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) mostra como fazer as seguintes tarefas. Para saber sobre a mudança de feed no Azure Cosmos DB antes de executar as seguintes amostras, consulte [Read Azure Cosmos DB change feed](read-change-feed.md) e Change feed [processador](change-feed-processor.md).

| Tarefa | Referência da API |
| --- | --- |
| [Funcionalidade básica de feed de mudança](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Ler feed de mudança de um momento específico | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Ler feed de mudança desde o início](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) | - |

## <a name="server-side-programming-examples"></a>Exemplos de programação do lado do servidor

O ficheiro amostra de [procedimento armazenado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) mostra como fazer as seguintes tarefas. Para saber sobre a programação do lado do servidor no Azure Cosmos DB antes de executar as seguintes amostras, consulte [procedimentos armazenados, gatilhos e funções definidas pelo utilizador](stored-procedures-triggers-udfs.md).

| Tarefa | Referência da API |
| --- | --- |
| [Criar um procedimento armazenado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L124-L143) | CosmosScripts.createStoredProcedure |
| [Executar um procedimento armazenado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L160-L174) | CosmosStoredProcedure.execute |
| [Eliminar um procedimento armazenado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L176-L186) | CosmosStoredProcedure.delete |

## <a name="user-management-examples"></a>Exemplos de gestão de utilizadores
O ficheiro da amostra de gestão do utilizador mostra como fazer as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| Criar um utilizador | - |
| Definir permissões numa coleção ou documento | - |
| Obter uma lista de permissões de um utilizador |- |