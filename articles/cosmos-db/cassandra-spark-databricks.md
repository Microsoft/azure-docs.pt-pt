---
title: Acesso Azure Cosmos DB Cassandra API da Azure Databricks
description: Este artigo abrange como trabalhar com Azure Cosmos DB Cassandra API da Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 5aaf31d8c629e0770b913485de175c38f708390c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359630"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Acesso Azure Cosmos DB Cassandra Dados API da Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo detalha como trabalhar com a Azure Cosmos DB Cassandra API da Spark on [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks).

## <a name="prerequisites"></a>Pré-requisitos

* [Provisão de uma conta Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account)

* [Reveja os fundamentos da ligação à Azure Cosmos DB Cassandra API](cassandra-spark-generic.md)

* [Provisão de um cluster Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)

* [Reveja as amostras de código para trabalhar com a Cassandra API](cassandra-spark-generic.md#next-steps)

* [Use cqlsh para validação se preferir](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Configuração de instância da Cassandra API para conector Cassandra:**

  O conector da API cassandra requer que os detalhes da ligação cassandra sejam inicializados como parte do contexto de faísca. Quando se lança um caderno Databricks, o contexto de faísca já está inicializado e não é aconselhável parar e reinitializá-lo. Uma solução é adicionar a configuração de instância da API cassandra a um nível de cluster, na configuração de faíscas de cluster. Esta é uma atividade única por cluster. Adicione o seguinte código à configuração Spark como um par de valor de chave separado do espaço:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

* **Conector Cassandra Spark:** - Para integrar a Azure Cosmos DB Cassandra API com a Spark, o conector Cassandra deve ser ligado ao cluster Azure Databricks. Para fixar o cluster:

  * Reveja a versão de runtime databricks, a versão Spark. Em seguida, encontre as [coordenadas maven](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) compatíveis com o conector Cassandra Spark, e prenda-as ao cluster. Consulte o artigo ["Carregar um pacote Maven ou pacote Spark"](https://docs.databricks.com/user-guide/libraries.html) para anexar a biblioteca do conector ao cluster. Por exemplo, a coordenada maven para "Databricks Runtime versão 4.3", "Spark 2.3.1" e "Scala 2.11" é `spark-cassandra-connector_2.11-2.3.1`

* **Biblioteca específica da Azure Cosmos DB Cassandra:** - Uma fábrica de conexão personalizada é necessária para configurar a política de retripagem do conector Cassandra Spark para a Azure Cosmos DB Cassandra API. Adicione as `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` [coordenadas maven](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) para anexar a biblioteca ao cluster.

## <a name="sample-notebooks"></a>Blocos de notas de exemplo

Uma lista de cadernos de [amostras](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/main/notebooks/scala) de Azure Databricks estão disponíveis no gitHub repo para você baixar. Estas amostras incluem como ligar-se à Azure Cosmos DB Cassandra API da Spark e realizar diferentes operações crud nos dados. Você também pode [importar todos os cadernos](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/main/dbc) para o seu espaço de trabalho do cluster Databricks e executá-lo. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Acesso a Azure Cosmos DB Cassandra API dos programas Spark Scala

Os programas spark a serem executados como processos automatizados em Azure Databricks são submetidos ao cluster usando [o spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)) e programados para executar através dos trabalhos Azure Databricks.

Seguem-se links para o ajudar a começar a construir programas Spark Scala para interagir com a Azure Cosmos DB Cassandra API.
* [Como ligar-se à Azure Cosmos DB Cassandra API de um programa Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Como executar um programa Spark Scala como um trabalho automatizado na Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Lista completa de amostras de código para trabalhar com a Cassandra API](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Passos seguintes

Começar a [criar uma conta de API para Cassandra do Azure Cosmos DB, uma base de dados e uma tabela](create-cassandra-api-account-java.md) com uma aplicação Java.