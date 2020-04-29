---
title: Ligue a Faísca Apache ao Azure Cosmos DB
description: Saiba mais sobre o conector Azure Cosmos DB Spark que lhe permite ligar a Faísca Apache ao Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: edfaf50b701f64b12f9cf5fcc9ab8d2c6d241d0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81482179"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Acelere a análise de big data usando o conector Apache Spark para Azure Cosmos DB

Você pode executar trabalhos [spark](https://spark.apache.org/) com dados armazenados em Azure Cosmos DB usando o conector Cosmos DB Spark. Cosmos pode ser usado para processamento de lote e fluxo, e como uma camada de serviço para baixo acesso de latência.

Pode utilizar o conector com [Os Tijolos de Dados Azure](https://azure.microsoft.com/services/databricks) ou [Azure HDInsight,](https://azure.microsoft.com/services/hdinsight/)que fornecem clusters spark geridos no Azure. A tabela seguinte mostra versões spark suportadas.

| Componente | Versão |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x e 2.1.x |
| Scala | 2.11 |
| Versão runtime azure Databricks | > 3.4 |

> [!WARNING]
> Este conector suporta a API central (SQL) de Azure Cosmos DB.
> Para cosmos DB para MongoDB API, use o [conector MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Para cosmos DB Cassandra API, use o [conector Cassandra Spark.](https://github.com/datastax/spark-cassandra-connector)
>

## <a name="quickstart"></a>Início Rápido

* Siga os passos no [Get started com o Java SDK](sql-api-async-java-get-started.md) para criar uma conta Cosmos DB, e povoar alguns dados.
* Siga os passos da [Azure Databricks começando](/azure/azure-databricks/quickstart-create-databricks-workspace-portal) a criar um espaço de trabalho e cluster Azure Databricks.
* Agora pode criar novos Cadernos e importar a biblioteca de conector Cosmos DB. Salte para [trabalhar com o conector Cosmos DB](#bk_working_with_connector) para obter detalhes sobre como configurar o seu espaço de trabalho.
* A secção seguinte tem excertos sobre como ler e escrever utilizando o conector.

### <a name="batch-reads-from-cosmos-db"></a>Leituras de lote de Cosmos DB

O seguinte corte mostra como criar um Spark DataFrame para ler a partir de Cosmos DB em PySpark.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

E o mesmo código em Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Lote escreve para Cosmos DB

O seguinte corte mostra como escrever uma moldura de dados para cosmos DB em PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

E o mesmo código em Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Leituras de streaming de Cosmos DB

O seguinte corte mostra como se conectar e ler a partir de Azure Cosmos DB Change Feed.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
E o mesmo código em Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Streaming escreve para Cosmos DB

O seguinte corte mostra como escrever uma moldura de dados para cosmos DB em PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

E o mesmo código em Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Mais cortes e amostras de ponta a ponta, ver [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a>Trabalhar com o conector

Você pode construir o conector a partir de fonte no GitHub, ou baixar os frascos uber de Maven nos links abaixo.

| Spark | Scala | Versão mais recente |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Utilização de cadernos databricks

Crie uma biblioteca utilizando o seu espaço de trabalho Databricks seguindo a orientação no Guia de Tijolos de Dados Azure > [Use o conector Azure Cosmos DB Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Nota: a página **Use the Azure Cosmos DB Spark Connector** não está atualmente atualizada. Em vez de descarregar os seis frascos separados em seis bibliotecas https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) diferentes, você pode baixar o jarro uber de maven at e instalar este jarro/biblioteca.
> 

### <a name="using-spark-cli"></a>Usando spark-cli

Para trabalhar com o conector utilizando o `spark-shell` `pyspark`spark-cli (isto é, , `spark-submit`pode utilizar o `--packages` parâmetro com as [coordenadas maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)do conector .

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Usando cadernos Jupyter

Se estiver a usar cadernos Jupyter dentro do HDInsight, pode utilizar uma célula mágica `%%configure` de faísca para especificar as coordenadas maven do conector.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Nota, a inclusão `spark.jars.excludes` do é específico para remover potenciais conflitos entre o conector, Apache Spark, e Livy.

### <a name="build-the-connector"></a>Construa o conector

Atualmente, este projeto `maven` de conector utiliza-o para construir sem dependências, pode executar:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Trabalhando com as nossas amostras

O [repositório Cosmos DB Spark GitHub](https://github.com/Azure/azure-cosmosdb-spark) tem os seguintes cadernos e scripts de amostra que pode experimentar.

* **Performance de voo on-time com Spark and Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Connect Spark to Cosmos DB utilizando o serviço de portátil HDInsight Jupyter para mostrar Spark SQL, GraphFrames e prever atrasos de voo usando os oleodutos ML.
* **Twitter Source with Apache Spark and Azure Cosmos DB Change Feed**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Usando Apache Spark para consultar cosmos DB Graphs**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Ligar os tijolos de dados azure ao Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** utilizando `azure-cosmosdb-spark`.  Linked aqui também é uma versão Azure Databricks do caderno de desempenho de [voo a tempo.](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)
* **[Lambda Architecture com Azure Cosmos DB e HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Você pode reduzir a sobrecarga operacional de manutenção de grandes oleodutos de dados usando Cosmos DB e Spark.

## <a name="more-information"></a>Mais Informações

Temos mais informações `azure-cosmosdb-spark` no [wiki,](https://github.com/Azure/azure-cosmosdb-spark/wiki) incluindo:

* [Guia de utilizador do conector de faíscas Azure Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Exemplos de agregações](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Configuração e Definições

* [Configuração do conector de faíscas](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Configuração do conector Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (em curso)
* [Configurar a consulta direta do Power BI para o Azure Cosmos DB via Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Resolução de problemas

* [Usando agregados DB Cosmos](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Questões Conhecidas](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Desempenho

* [Dicas de Desempenho](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Corridas de teste de consulta](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Testes de escrita](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Feed de Alterações

* [Mudanças de processamento de fluxo usando Azure Cosmos DB Change Feed e Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Alterar Demos feed](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Demos de fluxo estruturado](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitorização

* [Monitorização de trabalhos de faíscacom insights de aplicação](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, descarregue o conector Spark para Azure Cosmos DB do repositório GitHub de [faísca azure-cosmosdb.](https://github.com/Azure/azure-cosmosdb-spark) Explore os seguintes recursos adicionais no repo:

* [Exemplos de agregações](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exemplo de scripts e cadernos](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Também pode querer rever o [Apache Spark SQL, DataFrames e Datasets Guide,](https://spark.apache.org/docs/latest/sql-programming-guide.html)e o Apache Spark no artigo [Azure HDInsight.](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
