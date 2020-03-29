---
title: Access Azure Cosmos DB Cassandra API da Azure Databricks
description: Este artigo abrange como trabalhar com a Azure Cosmos DB Cassandra API da Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 37a06b19285c1196b5d87830ea176d4bd0d4eade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60894016"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Access Azure Cosmos DB Cassandra API dados da Azure Databricks

Este artigo detalha como trabalhar com a Azure Cosmos DB Cassandra API da Spark on [Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)

## <a name="prerequisites"></a>Pré-requisitos

* [Provisão de uma conta API Da API da Azure Cosmos DB Cassandra](create-cassandra-dotnet.md#create-a-database-account)

* [Reveja os fundamentos da ligação à Azure Cosmos DB Cassandra API](cassandra-spark-generic.md)

* [Provision um cluster de tijolos de dados Azure](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Reveja as amostras de código para trabalhar com a Cassandra API](cassandra-spark-generic.md#next-steps)

* [Use cqlsh para validação se preferir](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Configuração de instância Cassandra API para conector Cassandra:**

  O conector da Cassandra API exige que os detalhes da ligação de Cassandra sejam inicializados como parte do contexto de faísca. Quando lança um caderno Databricks, o contexto de faísca já está inicializado e não é aconselhável parar e reinicializá-lo. Uma solução é adicionar a configuração da instância Cassandra API a um nível de cluster, na configuração de faísca de cluster. Esta é uma atividade única por cluster. Adicione o seguinte código à configuração Spark como um par de valor chave separado do espaço:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

* **Conector Cassandra Spark:** - Para integrar a API da Azure Cosmos DB Cassandra com a Spark, o conector Cassandra deve ser ligado ao cluster Azure Databricks. Para fixar o cluster:

  * Reveja a versão de tempo de execução databricks, a versão Spark. Em seguida, encontre as [coordenadas maven](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) que são compatíveis com o conector Cassandra Spark, e anexe-a ao cluster. Consulte o artigo ["Upload de um pacote Maven ou pacote Spark"](https://docs.databricks.com/user-guide/libraries.html) para anexar a biblioteca do conector ao cluster. Por exemplo, a coordenada Maven para "Databricks Runtime version 4.3", "Spark 2.3.1", e "Scala 2.11" é`spark-cassandra-connector_2.11-2.3.1`

* Biblioteca específica da **Azure Cosmos DB Cassandra API:** - É necessária uma fábrica de ligação personalizada para configurar a política de retry desde o conector Cassandra Spark até à Azure Cosmos DB Cassandra API. Adicione `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0`as [coordenadas maven](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) para anexar a biblioteca ao cluster.

## <a name="sample-notebooks"></a>Blocos de notas de exemplo

Uma lista de cadernos de [amostras](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) Azure Databricks estão disponíveis no repo GitHub para você descarregar. Estas amostras incluem como ligar-se à API De Spark e realizar diferentes operações de CRUD nos dados. Também pode [importar todos os cadernos](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) para o seu espaço de trabalho de cluster Databricks e executá-lo. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Acesso à Azure Cosmos DB Cassandra API dos programas Spark Scala

Os programas de faíscas a serem executados à medida que os processos automatizados em Azure Databricks são submetidos ao cluster utilizando o [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)) e programados para executar através dos trabalhos de Azure Databricks.

Seguem-se links para o ajudar a começar a construir programas Spark Scala para interagir com a API DB Cassandra da Azure Cosmos.
* [Como ligar-se à Azure Cosmos DB Cassandra API de um programa Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Como executar um programa Spark Scala como um trabalho automatizado em Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Lista completa de amostras de código para trabalhar com a Cassandra API](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Passos seguintes

Começar a [criar uma conta de API para Cassandra do Azure Cosmos DB, uma base de dados e uma tabela](create-cassandra-api-account-java.md) com uma aplicação Java.
