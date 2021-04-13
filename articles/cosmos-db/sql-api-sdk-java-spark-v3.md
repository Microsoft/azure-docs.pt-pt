---
title: Azure Cosmos DB Apache Spark 3 OLTP Connector para SQL API (Preview) notas e recursos
description: Conheça o Conector Azure Cosmos DB Apache Spark 3 OLTP para API SQL (Preview), incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e7d206b63d6e1bf741a5dc298dd5bbc2d48ab11b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368641"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-preview-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 3 OLTP Connector para Core (SQL) API (Preview): Notas de lançamento e recursos
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

**O conector Azure Cosmos DB Spark 3 OLTP (Preview)** fornece suporte Apache Spark v3 para Azure Cosmos DB utilizando o SQL API.
[Azure Cosmos DB](introduction.md) é um serviço de base de dados distribuído globalmente que permite aos desenvolvedores trabalhar com dados usando uma variedade de APIs padrão, tais como SQL, MongoDB, Cassandra, Graph e Table.

> [!Note]
> Esta versão do conector Azure Cosmos DB Spark 3 OLTP é uma construção de pré-visualização.
> Esta construção não foi testada em carga ou desempenho.
> Esta construção não é recomendada para uso em cenários de produção.
>

## <a name="documentation"></a>Documentação

- [Introdução](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
- [API do Catálogo](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
- [Referência do parâmetro de configuração](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)


## <a name="version-compatibility"></a>Compatibilidade de versões

| Conector     | Spark         | Versão mínima java | Versões Scala suportadas |
| ------------- | ------------- | -------------------- | -----------------------  |
| 4.0.0-beta.1  | 3.1.1         |        8             | 2.12                     |

## <a name="download"></a>Download

Pode utilizar a coordenada maven do frasco para instalar automaticamente o Conector de Faíscas no seu Databricks Runtime 8 de Maven: `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.0.0-beta.1`

Também pode integrar-se contra o Cosmos DB Spark Connector no seu projeto SBT:
```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.0.0-beta.1"
```

Cosmos DB Spark Connector está disponível no [Maven Central Repo.](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/4.0.0-beta.1/jar)

### <a name="general"></a>Geral

Se encontrar algum bug, por favor preencha um problema [aqui](https://github.com/Azure/azure-sdk-for-java/issues/new).

Para sugerir uma nova funcionalidade ou alterações que possam ser feitas, arquive um problema da mesma forma que faria para um bug.

[!INCLUDE[Changelog](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos-spark_3-1_2-12/CHANGELOG.md)]

## <a name="next-steps"></a>Passos seguintes

Reveja o nosso [guia de arranque rápido para trabalhar com o conector Azure Cosmos DB Spark 3 OLTP](create-sql-api-spark.md).
