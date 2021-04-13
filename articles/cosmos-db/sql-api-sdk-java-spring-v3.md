---
title: Dados de primavera Azure Cosmos DB v3 para notas e recursos de lançamento da SQL API
description: Saiba mais sobre os dados de primavera Azure Cosmos DB v3 para API SQL, incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 594d38425be0304a9f7737bdfba60b29187a2e2d
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363517"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Dados de primavera Azure Cosmos DB v3 para Core (SQL) API: Notas de lançamento e recursos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 de .NET Core](sql-api-sdk-dotnet-core.md)
> * [SDK Feed de Alterações .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK v4 de Java](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Conector de faísca 3 OLTP](sql-api-sdk-java-spark-v3.md)
> * [Conector de faísca 2 OLTP](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Executor em massa - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa - Java](sql-api-sdk-bulk-executor-java.md)

A versão DB DB do Spring Data Azure Cosmos para Core (SQL) permite que os desenvolvedores utilizem O Azure Cosmos DB em aplicações primavera. A Spring Data Azure Cosmos DB expõe a interface de Dados da primavera para manipular bases de dados e coleções, trabalhar com documentos e emitir consultas. As APIs de Sincronização e Async (Reativa) são suportadas no mesmo artefacto Maven. 

> [!IMPORTANT]
> Dados de primavera Azure Cosmos DB tem uma dependência da estrutura de dados da primavera.
> 
> versões azure-spring-data-cosmos de 3.0.0 a 3.4.0 suportam as versões 2.2 e 2.3.
> 
> versões azure-spring-data-cosmos 3.5.0 e acima suportam as versões 2.4.3 ou superior.
>

O [Quadro primavera](https://spring.io/projects/spring-framework) é um modelo de programação e configuração que dinamiza o desenvolvimento de aplicações java. A mola simplifica a "canalização" das aplicações utilizando a injeção de dependência. Muitos desenvolvedores como a primavera porque torna as aplicações de construção e teste mais simples. [O Boot spring](https://spring.io/projects/spring-boot) estende este manuseamento da canalização com um olho para aplicação web e desenvolvimento de microserviços. [O Spring Data](https://spring.io/projects/spring-data) é um modelo de programação e enquadramento para aceder a datastores como a Azure Cosmos DB a partir do contexto de uma aplicação Spring ou Spring Boot. 

Pode utilizar o Spring Data Azure Cosmos DB nas suas aplicações [Azure Spring Cloud.](https://azure.microsoft.com/services/spring-cloud/)

> [!IMPORTANT]  
> Estas notas de lançamento são para a versão 3 da Spring Data Azure Cosmos DB. Pode encontrar [aqui as notas de lançamento para a versão 2](sql-api-sdk-java-spring-v2.md). 
>
> Dados de primavera Azure Cosmos DB suporta apenas o SQL API.
>
> Consulte estes artigos para obter informações sobre dados da primavera sobre outras APIs Azure Cosmos DB:
> * [Dados da primavera para Apache Cassandra com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Dados de primavera MongoDB com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="get-started-fast"></a>Começa depressa.

  Levante-se ecorda com a Spring Data Azure Cosmos DB seguindo o nosso [guia de Arranque de Botas de primavera](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). A abordagem Starter Boot Spring é a forma recomendada de começar a usar o conector DB de Dados de primavera Azure Cosmos.

  Em alternativa, pode adicionar a dependência DB do Spring Data Azure Cosmos ao seu `pom.xml` ficheiro, conforme mostrado abaixo:

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>Conteúdo útil

| Conteúdo | Ligação |
|---|---|
|**Transferência de SDK**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**Documentação da API** | [Documentação de referência da API de Java](/java/api/com.azure.spring.data.cosmos) |
|**Contribuir para a SDK** | [Azure SDK para Java Central Repo no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Introdução** | [Quickstart: Construa uma app DB Azure Cosmos para gerir dados API API da Azure Cosmos DB SQL](./create-sql-api-spring-data.md) <br> [GitHub repo com código de arranque rápido](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Amostras de código básico** | [Azure Cosmos DB: Dados de primavera Azure Cosmos DB exemplos para o SQL API](sql-api-spring-data-sdk-samples.md) <br> [GitHub repo com código de amostra](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Sugestões de desempenho**| [Dicas de desempenho para Java SDK v4 (aplicável aos dados da primavera)](performance-tips-java-sdk-v4-sql.md)| 
| **Resolução de problemas** | [Resolução de problemas Java SDK v4 (aplicável aos dados da primavera)](troubleshoot-java-sdk-v4-sql.md) | 
| **Workshops e laboratórios DB da Azure Cosmos** |[Página inicial dos workshops do Cosmos DB](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>Notas adicionais

* Dados de primavera Azure Cosmos DB suporta Java JDK 8 e Java JDK 11.
* Os Dados da primavera 2.3 são atualmente suportados, os Dados da primavera 2.4 não são suportados atualmente.

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Saiba mais sobre o [Quadro da primavera.](https://spring.io/projects/spring-framework)

Saiba mais sobre [a Bota de primavera.](https://spring.io/projects/spring-boot)

Saiba mais sobre [os Dados da primavera.](https://spring.io/projects/spring-data)