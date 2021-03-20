---
title: Cosmos DB Apache Spark Connector para notas e recursos de lançamento da SQL API
description: Conheça o Azure Cosmos DB Apache Spark Connector para a API SQL, incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 7ff75bf9d45803dd96927bcf7c70e7c7912db979
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93097163"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark Connector for Core (SQL) API: Liberar notas e recursos
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
> * [Spark Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Executor em massa - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa - Java](sql-api-sdk-bulk-executor-java.md)

Pode acelerar a análise de big data utilizando o Conector de Faísca Apache Para Core (SQL) do Azure Cosmos. O Spark Connector permite-lhe executar trabalhos [de Faísca](https://spark.apache.org/) em dados armazenados em Azure Cosmos DB. O processamento de lote e fluxo são suportados.

Pode utilizar o conector com [Azure Databricks](https://azure.microsoft.com/services/databricks) ou [Azure HDInsight,](https://azure.microsoft.com/services/hdinsight/)que fornecem clusters de faíscas geridos em Azure. A tabela a seguir mostra versões suportadas:

| Componente | Versão |
|---------|-------|
| Apache Spark | 2.4.*x*, 2.3. *x*, 2.2. *x*, e 2.1. *x* |
| Scala | 2.11 |
| Azure Databricks (versão runtime) | Mais tarde que 3.4 |

> [!WARNING]
> Este conector suporta a API do núcleo (SQL) da Azure Cosmos DB.
> Para o Cosmos DB API para a MongoDB, utilize o [Conector MongoDB para faíscas](https://docs.mongodb.com/spark-connector/master/).
> Para a Cosmos DB Cassandra API, utilize o [conector Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="resources"></a>Recursos

| Recurso | Ligação |
|---|---|
| **Transferência de SDK** | [Baixar os mais recentes .jar](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG), [Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**Documentação da API** | [Referência do conector de faíscas]() |
|**Contribuir para o SDK** | [Conector DB Azure Cosmos para Faísca Apache no GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Introdução** | [Acelere a análise de big data usando o conector Apache Spark para Azure Cosmos DB](./spark-connector.md#bk_working_with_connector) <br> [Use o Fluxo Estruturado de Faíscas Apache com Apache Kafka e Azure Cosmos DB](../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>História do lançamento

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Novas funcionalidades
- Adiciona uma nova opção config, `changefeedstartfromdatetime` que pode ser usada para especificar a hora de início para quando o feed de mudança deve ser processado. Para mais informações, consulte [as opções Config.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Correções de erros chave
- Corrige uma regressão que causou um consumo excessivo de memória nos executores para grandes conjuntos de resultados (por exemplo, com milhões de linhas), resultando, em última análise, no erro `java.lang.OutOfMemoryError: GC overhead limit exceeded` .

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Fixa uma caixa de borda de verificação de streaming em que `ID` o contém o caractere do tubo (|) com o `ChangeFeedMaxPagesPerBatch` config aplicado.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Novas funcionalidades
* Adiciona suporte para atualizações a granel quando são utilizadas teclas de partição aninhadas.
* Adiciona suporte para tipos de dados Decimal e Float durante as escritas para Azure Cosmos DB.
* Adiciona suporte para tipos de timestamp quando estão a usar Long (época Unix) como um valor.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige a exceção da máquina de escrever que ocorre quando o `WriteThroughputBudget` config é usado.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Novas funcionalidades
* Adiciona informações de erro para falhas a granel à exceção e registo.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige problemas de controlo de streaming.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Para reduzir o ruído, corrige o nível de registo de uma mensagem deixada involuntariamente com o nível ERROR.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige um bug em streaming estruturado durante as divisórias. O bug pode resultar em alguns registos de feed de alteração em falta ou em exceções nulas para as gravações de checkpoint.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige um bug que faz com que um esquema personalizado fornecido para que o readStream seja ignorado.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige uma regressão (JAR não sombreado inclui todas as dependências sombreadas) que aumenta o tempo de construção em 50 por cento.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige um problema de dependência que faz com que o Transporte Direto sobre a TCP falhe com o RequestTimeoutException.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Novas funcionalidades
* Melhora a gestão de ligação e o agrupamento de ligações para reduzir o número de chamadas de metadados.

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Saiba mais sobre o [Apache Spark](https://spark.apache.org/).
