---
title: Acesso do Azure Cosmos DB Cassandra API do Azure Databricks
description: Este artigo aborda como trabalhar com a API de Cassandra do Azure Cosmos DB do Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 37a06b19285c1196b5d87830ea176d4bd0d4eade
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60894016"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Aceder a dados Cassandra API do Azure Cosmos DB do Azure Databricks

Este artigo descreve como workwith do Azure Cosmos DB Cassandra API do Spark no [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Pré-requisitos

* [Aprovisionar uma conta de API de Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Reveja as noções básicas de conexão a Cassandra API do Azure Cosmos DB](cassandra-spark-generic.md)

* [Aprovisionar um cluster do Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Reveja os exemplos de código para trabalhar com a API de Cassandra](cassandra-spark-generic.md#next-steps)

* [Utilize cqlsh para validação, se preferir por isso](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Configuração de instância da API de Cassandra para o conector do Cassandra:**

  O conector para a API de Cassandra requer os detalhes de ligação do Cassandra para ser inicializado como parte do contexto do spark. Quando inicia um bloco de notas do Databricks, o contexto do spark já foi inicializado e não é recomendado parar e reinicializá-lo. Uma solução é adicionar a configuração de instância de API para Cassandra num nível de cluster, a configuração do cluster do spark. Esta é uma atividade de uso individual por cluster. Adicione o seguinte código para a configuração de Spark, como um espaço separados par chave-valor:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

* **Conector do Spark de Cassandra:** - para integrar a API de Cassandra do Azure Cosmos DB com o Spark, o Cassandra conector deverá ligar ao cluster do Azure Databricks. Para anexar o cluster:

  * Consulte a versão de runtime do Databricks, a versão do Spark. Em seguida, localize a [coordenadas do maven](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) que são compatíveis com o conector do Spark de Cassandra e anexá-lo para o cluster. Ver ["Carregar um pacote Maven ou um pacote Spark"](https://docs.databricks.com/user-guide/libraries.html) artigo para anexar a biblioteca de conectores para o cluster. Por exemplo, coordenada do maven para "Tempo de execução versão 4.3 do Databricks", "2.3.1 do Spark," e "Scala 2.11" é `spark-cassandra-connector_2.11-2.3.1`

* **Biblioteca de específico do Cosmos DB Cassandra API do Azure:** -uma fábrica de conexão personalizada é necessária para configurar a política de repetição a partir do conector do Spark do Cassandra para a API de Cassandra do Azure Cosmos DB. Adicionar a `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` [coordenadas do maven](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) anexar biblioteca ao cluster.

## <a name="sample-notebooks"></a>Blocos de notas de exemplo

Uma lista do Azure Databricks [blocos de notas de exemplo](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) estão disponíveis no repositório do GitHub para transferência. Estes exemplos incluem como ligar à API de Cassandra do Azure Cosmos DB a partir do Spark e executar diferentes operações de CRUD nos dados. Também pode [importar todos os blocos de notas](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) para o seu Databricks a área de trabalho de cluster e executá-lo. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Aceder à API de Cassandra do Azure Cosmos DB a partir de programas do Spark Scala

Programas do Spark para ser executado como processos automatizados no Azure Databricks são submetidos para o cluster, utilizando [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)) e agendada para executar as tarefas do Azure Databricks.

Seguem-se ligações para ajudá-lo a começar a criar programas do Spark Scala para interagir com a API de Cassandra do Azure Cosmos DB.
* [Como ligar à API de Cassandra do Azure Cosmos DB a partir de um programa do Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Como executar um programa do Spark Scala como um trabalho automatizado no Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Lista completa das amostras de código para trabalhar com a API de Cassandra](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Passos Seguintes

Começar a [criar uma conta de API para Cassandra do Azure Cosmos DB, uma base de dados e uma tabela](create-cassandra-api-account-java.md) com uma aplicação Java.
