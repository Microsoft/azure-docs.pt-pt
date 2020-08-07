---
title: Azure Cosmos DB Apache Spark Connector para notas e recursos de lançamento da SQL API
description: Saiba tudo sobre o Azure Cosmos DB Apache Spark Connector para a SQL API, incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 72b3b190492be5cec9986729875c5b09e2559ae5
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854319"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark Connector for Core (SQL) API: notas de lançamento e recursos
> [!div class="op_single_selector"]
> * [SDK .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 de .NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK v4 de Java](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Spring Data](sql-api-sdk-java-spring.md)
> * [Conector de faíscas](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

Acelere a análise de big data utilizando o Conector de Faísca Apache Azure Cosmos DB para Core (SQL). O Spark Connector permite-lhe executar trabalhos [de Faísca](https://spark.apache.org/) em dados armazenados em Azure Cosmos DB. O processamento de lote e fluxo são suportados.

Pode utilizar o conector com [Azure Databricks](https://azure.microsoft.com/services/databricks) ou [Azure HDInsight,](https://azure.microsoft.com/services/hdinsight/)que fornecem clusters de faíscas geridos em Azure. A tabela seguinte mostra versões Spark suportadas.

| Componente | Versão |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x e 2.1.x |
| Scala | 2.11 |
| Versão runtime Azure Databricks | > 3.4 |

> [!WARNING]
> Este conector suporta a API do núcleo (SQL) da Azure Cosmos DB.
> Para Cosmos DB para MongoDB API, utilize o [conector De Faísca MongoDB](https://docs.mongodb.com/spark-connector/master/).
> Para Cosmos DB Cassandra API, utilize o [conector Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="helpful-content"></a>Conteúdo útil

| Conteúdo | Ligação |
|---|---|
| **Download SDK** | [Download a partir de Apache Spark](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**Documentação da API** | [Referência do conector de faíscas]() |
|**Contribuir para a SDK** | [Conector DB Azure Cosmos para Faísca Apache no GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Introdução** | [Acelere a análise de big data usando o conector Apache Spark para Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Use o Fluxo Estruturado de Faíscas Apache com Apache Kafka e Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>História do lançamento

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>Novas funcionalidades
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige um caso de borda de verificação de streaming onde no "id" contém "/" caractere com o config "ChangeFeedMaxPagesPerBatch" aplicado

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Novas funcionalidades
* Adiciona suporte para atualizações a granel ao utilizar chaves de partição aninhadas
* Adiciona suporte para tipos de dados Decimal e Float durante as escritas para Cosmos DB.
* Adiciona suporte para tipos de timestamp quando estão a usar Long (unix Epoch) como um valor
#### <a name="key-bug-fixes"></a>Correções de erros chave

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>Novas funcionalidades
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Correções Tipo de exceção ao utilizar o config "WriteThroughputBudget".

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Novas funcionalidades
* Adiciona informações de erro para falhas a granel à exceção e registo.
#### <a name="key-bug-fixes"></a>Correções de erros chave

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>Novas funcionalidades
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige problemas de controlo de streaming.

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>Novas funcionalidades
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige o nível de registo de uma mensagem deixada involuntariamente com ERRO de nível para reduzir o ruído

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>Novas funcionalidades
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige um bug em streaming estruturado durante divisórias - possivelmente resultando em falta de alguns registos de feed de alteração ou ver exceções nulas para escritas de ponto de verificação

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>Novas funcionalidades
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige um bug onde um esquema personalizado fornecido para readStream é ignorado

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>Novas funcionalidades
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Regressão de correções (JAR não sombreado inclui todas as dependências sombreadas) que aumentou o tempo de construção em 50%

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>Novas funcionalidades
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige um problema de dependência que faz com que o Transporte Direto sobre a TCP falhe com o RequestTimeoutException

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Novas funcionalidades
* Melhora a gestão de ligação e o agrupamento de ligações para reduzir o número de chamadas de metadados
#### <a name="key-bug-fixes"></a>Correções de erros chave

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre cosmos DB, consulte a página de serviço [do Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

Para saber mais sobre Apache Spark, consulte [a página inicial.](https://spark.apache.org/)