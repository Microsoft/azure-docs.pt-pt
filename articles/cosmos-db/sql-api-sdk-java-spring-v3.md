---
title: Dados de primavera Azure Cosmos DB v3 para notas e recursos de lançamento da SQL API
description: Saiba mais sobre os dados de primavera Azure Cosmos DB v3 para API SQL, incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 4b2d474f25209034034db092ca971bff6b78d73a
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068746"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Dados de primavera Azure Cosmos DB v3 para Core (SQL) API: Notas de lançamento e recursos
> [!div class="op_single_selector"]
> * [SDK .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 de .NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK v4 de Java](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Dados da primavera v2](sql-api-sdk-java-spring-v2.md)
> * [Dados da primavera v3](sql-api-sdk-java-spring-v3.md)
> * [Spark Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

A versão DB DB do Spring Data Azure Cosmos para Core (SQL) permite que os desenvolvedores utilizem O Azure Cosmos DB em aplicações primavera. A Spring Data Azure Cosmos DB expõe a interface de Dados da primavera para manipular bases de dados e coleções, trabalhar com documentos e emitir consultas. As APIs de Sincronização e Async (Reativa) são suportadas no mesmo artefacto Maven. 

Dados de primavera Azure Cosmos DB tem uma dependência da estrutura de dados da primavera. A equipa da Azure Cosmos DB SDK lança artefactos Maven para as versões 2.2 e 2.3 da Spring Data.

O [Quadro primavera](https://spring.io/projects/spring-framework) é um modelo de programação e configuração que dinamiza o desenvolvimento de aplicações java. A mola simplifica a "canalização" das aplicações utilizando a injeção de dependência. Muitos desenvolvedores como a primavera porque torna as aplicações de construção e teste mais simples. [O Boot spring](https://spring.io/projects/spring-boot) estende este manuseamento da canalização com um olho para aplicação web e desenvolvimento de microserviços. [O Spring Data](https://spring.io/projects/spring-data) é um modelo de programação e enquadramento para aceder a datastores como a Azure Cosmos DB a partir do contexto de uma aplicação Spring ou Spring Boot. 

Pode utilizar o Spring Data Azure Cosmos DB nas suas aplicações [Azure Spring Cloud.](https://azure.microsoft.com/services/spring-cloud/)

> [!IMPORTANT]  
> Estas notas de lançamento são para a versão 3 da Spring Data Azure Cosmos DB. Pode encontrar [aqui as notas de lançamento para a versão 2](sql-api-sdk-java-spring-v2.md). 
>
> Dados de primavera Azure Cosmos DB suporta apenas o SQL API.
>
> Consulte estes artigos para obter informações sobre dados da primavera sobre outras APIs Azure Cosmos DB:
> * [Dados da primavera para Apache Cassandra com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Dados de primavera MongoDB com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Comece aqui

# <a name="explore"></a>[Explorar](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>Estes separadores contêm amostras básicas de Dados de primavera Azure Cosmos DB.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Configure dependências

Estão disponíveis dois artefactos Maven da Spring Data Azure Cosmos.

- Artefacto que depende da versão 2.2 do quadro dos Dados de Mola:
  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-2-2-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

- Artefacto que depende da versão 2.3 do quadro dos Dados de primavera:
  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-2-3-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[Ligar](#tab/connect)

### <a name="connect"></a>Ligar

Especifique a conta DB da Azure Cosmos e os detalhes do contentor. Dados de primavera Azure Cosmos DB cria automaticamente o cliente e conecta-se ao recipiente.

[aplicação.propriedades](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-2-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Doc ops](#tab/docs)

### <a name="document-operations"></a>Operações documentais

[Criar:](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java)[!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]

[Excluir:](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java)[!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

# <a name="query"></a>[Query](#tab/queries)

[Consulta:](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java)[!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="resources"></a>Recursos

| Recurso | Quadro de dados de primavera 2.2 | Quadro de dados de primavera 2.3 |
|---|---|
| **Download SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**Contribuir para o SDK** | [Dados de primavera Azure Cosmos DB repo no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [Dados de primavera Azure Cosmos DB repo no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**Tutorial**| [Spring Data Azure Cosmos DB tutorial no GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [Spring Data Azure Cosmos DB tutorial no GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>História do lançamento

### <a name="300-beta2-unreleased"></a>3.0.0-beta.2 (inédito)

### <a name="300-beta1-august-17-2020"></a>3.0.0-beta.1 (17 de agosto de 2020)
#### <a name="new-features"></a>Novas funcionalidades
* Atualiza o ID do grupo para `com.azure` .
* Atualiza a identificação do artefacto para `azure-spring-data-2-3-cosmos` .
* Atualiza a dependência Azure-cosmos SDK para `4.3.2-beta.2` .
* Acrescenta apoio a entidades de auditoria: gestão automática `createdBy` `createdDate` `lastModifiedBy` de, e `lastModifiedDate` áreas anotadas.
* Adiciona `@GeneratedValue` suporte de anotação para geração automática de ID para campos de ID de `String` tipo.
* Adiciona suporte de configuração multi-base para contas DB únicas do Azure Cosmos com várias bases de dados e várias contas DB do Azure Cosmos com várias bases de dados.
* Adiciona suporte para `@Version` anotação em qualquer campo de cordas.
* Atualiza os tipos de retorno da API sincronizados para `Iterable` os tipos em vez de `List` .
* Expõe `CosmosClientBuilder` do Azure Cosmos DB SDK como feijão de primavera para a `@Configuration` classe.
* Atualizações `CosmosConfig` para conter métricas de consulta e implementação do processador de diagnóstico de resposta.
* Adiciona suporte para devolver o tipo de `Optional` dados para consultas de resultados únicos.
#### <a name="renames"></a>Renomes
* `CosmosDbFactory` para `CosmosFactory` .
* `CosmosDBConfig` para `CosmosConfig` .
* `CosmosDBAccessException` para `CosmosAccessException` .
* `Document` anotação à `Container` anotação.
* `DocumentIndexingPolicy` anotação à `CosmosIndexingPolicy` anotação.
* `DocumentQuery` para `CosmosQuery` .
* application.properties flag `populateQueryMetrics` to `queryMetricsEnabled` .
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Agendamento de diagnósticos registando tarefa para `Parallel` fios para evitar bloquear fios Netty I/O.
* Corrige o bloqueio otimista na operação de eliminação.
* Corrige problema com a fuga de consultas para `IN` cláusula.
* Correções problema permitindo o tipo de `long` dados para `@Id` .
* Corrige a questão `boolean` permitindo, e como tipos de `long` `int` `double` dados para a `@PartitionKey` anotação.
* Correções `IgnoreCase` e `AllIgnoreCase` palavras-chave para ignorar consultas de caso.
* Remove o valor da unidade de pedido predefinido de 4.000 quando os recipientes são criados automaticamente.

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Saiba mais sobre o [Quadro da primavera.](https://spring.io/projects/spring-framework)

Saiba mais sobre [a Bota de primavera.](https://spring.io/projects/spring-boot)

Saiba mais sobre [os Dados da primavera.](https://spring.io/projects/spring-data)