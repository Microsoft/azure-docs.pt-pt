---
title: Dados de primavera Azure Cosmos DB v3 para notas e recursos de lançamento da SQL API
description: Saiba tudo sobre os dados de primavera Azure Cosmos DB v3 para API SQL incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e67e6911eeac29036dee2b68c19395b34e1d11da
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228040"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Dados de primavera Azure Cosmos DB v3 para Core (SQL) API: notas de lançamento e recursos
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

O Spring Data Azure Cosmos DB v3 para Core (SQL) permite que os desenvolvedores utilizem Azure Cosmos DB em aplicações primavera. A Spring Data Azure Cosmos DB expõe a interface de Dados da primavera para manipular bases de dados e coleções, trabalhar com documentos e emitir consultas. As APIs de Sincronização e Async (Reativa) são suportadas no mesmo artefacto Maven. 

Dados de primavera Azure Cosmos DB assume uma dependência da estrutura de dados da primavera. A equipa da Azure Cosmos DB SDK lança artefactos Maven para Spring Data v2.2 e v2.3.

O [quadro primavera](https://spring.io/projects/spring-framework) é um modelo de programação e configuração que dinamiza o desenvolvimento de aplicações java. Para citar o site da organização, a primavera dinamiza a "canalização" das aplicações utilizando a injeção de dependência. Muitos desenvolvedores como a primavera porque as aplicações de construção e teste tornam-se mais simples. [O Spring Boot](https://spring.io/projects/spring-boot) estende esta ideia de manusear a canalização com um olho para a aplicação web e desenvolvimento de microserviços. [O Spring Data](https://spring.io/projects/spring-data) é um modelo de programação e enquadramento para aceder a datastores como Azure Cosmos DB a partir do contexto de uma aplicação Spring ou Spring Boot. 

Pode utilizar o Spring Data Azure Cosmos DB nas suas aplicações [Azure Spring Cloud.](https://azure.microsoft.com/services/spring-cloud/)

> [!IMPORTANT]  
> Estas notas de lançamento são para v3 de Spring Data Azure Cosmos DB. Pode encontrar [aqui](sql-api-sdk-java-spring-v2.md)as notas de lançamento v2. 
>
> Dados de primavera Azure Cosmos DB suporta apenas API SQL.
>
> Os seguintes guias suportam dados de primavera em outras APIs Azure Cosmos DB:
> * [Dados da primavera para Apache Cassandra com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Dados de primavera MongoDB com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Comece aqui

# <a name="explore"></a>[Explorar](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

### <a name="navigate-the-tabs-above-for-basic-spring-data-azure-cosmos-db-samples"></a>Navegue nos separadores acima para obter amostras básicas de Dados de primavera Azure Cosmos DB.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Configure dependências

Estão disponíveis dois artefactos de primavera Azure Cosmos DB v3 Maven.

Artefacto que depende da estrutura dos dados de primavera v2.2:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-2-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

Artefacto que depende da estrutura dos dados de primavera v2.3:
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

### <a name="query"></a>Consulta

[Consulta:](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java)[!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="helpful-content"></a>Conteúdo útil

| Conteúdo | Quadro de dados de primavera v2.2 | Quadro de dados de primavera v2.3 |
|---|---|
| **Download SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**Contribuir para a SDK** | [Dados de primavera Azure Cosmos DB repo no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [Dados de primavera Azure Cosmos DB repo no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**Tutorial**| [Spring Data Azure Cosmos DB tutorial no GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [Spring Data Azure Cosmos DB tutorial no GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>História do lançamento

### <a name="300-beta2-unreleased"></a>3.0.0-beta.2 (Não lançado)

### <a name="300-beta1-2020-08-17"></a>3.0.0-beta.1 (2020-08-17)
#### <a name="new-features"></a>Novas funcionalidades
* ID de grupo atualizado para `com.azure` .
* Identificação de artefactos atualizado para `azure-spring-data-2-3-cosmos` .
* Dependência Azure-cosmos SDK para `4.3.2-beta.2` .
* Apoio a entidades de auditoria - gestão automática de createdBy, createdDate, lastModifiedBy e últimos Campos Anotados DeModified.
* `@GeneratedValue` suporte de anotação para geração automática de id para campos de ID de `String` tipo.
* Suporte de configuração multi-base para conta cosmos única com várias bases de dados e várias contas cosmos com várias bases de dados.
* Suporte para `@Version` anotação em qualquer campo de cordas.
* Os tipos de retorno de APIs de sincronização atualizados para `Iterable` os tipos em vez de `List` .
* Exposto `CosmosClientBuilder` da Cosmos SDK como feijão de primavera para as `@Configuration` aulas.
* Atualizado `CosmosConfig` para conter métricas de consulta e implementação de processador de diagnóstico de resposta.
* Suporte para retornar o tipo de `Optional` dados para consultas de resultados únicos.
#### <a name="renames"></a>Renomes
* `CosmosDbFactory` para `CosmosFactory` .
* `CosmosDBConfig` para `CosmosConfig` .
* `CosmosDBAccessException` para `CosmosAccessException` .
* `Document` anotação à `Container` anotação.
* `DocumentIndexingPolicy` anotação à `CosmosIndexingPolicy` anotação.
* `DocumentQuery` para `CosmosQuery` .
* application.properties flag `populateQueryMetrics` to `queryMetricsEnabled` .
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Agendar a tarefa de registo de diagnósticos para `Parallel` os fios para evitar bloquear os fios netty I/O.
* Bloqueio otimista fixo na operação de eliminação.
* Problema fixo com a fuga de consultas por `IN` cláusula.
* Problema fixo permitindo o tipo de `long` dados para `@Id` .
* Problema fixo `boolean` permitindo, , , como tipos de `long` `int` `double` dados para `@PartitionKey` anotação.
* `IgnoreCase`  &  `AllIgnoreCase` Palavras-chave fixas para ignorar consultas de casos.
* Removido valor da unidade de pedido predefinido de 4000 ao criar recipientes automaticamente.

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre cosmos DB, consulte a página de serviço [do Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

Para saber mais sobre o quadro da primavera, consulte a página inicial do [projeto.](https://spring.io/projects/spring-framework)

Para saber mais sobre a Bota de primavera, consulte a página inicial do [projeto.](https://spring.io/projects/spring-boot)

Para saber mais sobre os Dados da primavera, consulte a página inicial do [projeto.](https://spring.io/projects/spring-data)