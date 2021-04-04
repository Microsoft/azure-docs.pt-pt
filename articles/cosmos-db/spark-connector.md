---
title: Ligue a Faísca Apache a Azure Cosmos DB
description: Conheça o conector Azure Cosmos DB Spark que lhe permite ligar o Apache Spark ao Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: 06498a27b95a72148497efd2d1e600d802414359
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359562"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Acelere a análise de big data usando o conector Apache Spark para Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Você pode executar trabalhos [spark](https://spark.apache.org/) com dados armazenados em Azure Cosmos DB usando o conector Cosmos DB Spark. Cosmos pode ser usado para processamento de lote e fluxo, e como uma camada de serviço para baixo acesso de latência.

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

> [!IMPORTANT]
> O conector Azure Cosmos DB Spark não é suportado atualmente em contas [sem servidor.](serverless.md) Isto será endereçado à medida que a oferta sem servidor se torna geralmente disponível.

## <a name="quickstart"></a>Início Rápido

* Siga os passos em [Get start with the Java SDK](./create-sql-api-java.md) para criar uma conta Cosmos DB, e povoar alguns dados.
* Siga os passos na [Azure Databricks começando](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) a criar um espaço de trabalho e cluster Azure Databricks.
* Agora pode criar novos Cadernos e importar a biblioteca de conectores Cosmos DB. Salte para [trabalhar com o conector Cosmos DB](#bk_working_with_connector) para obter detalhes sobre como configurar o seu espaço de trabalho.
* A secção seguinte tem cortes sobre como ler e escrever usando o conector.

### <a name="batch-reads-from-cosmos-db"></a>Leituras de lote de Cosmos DB

O seguinte snippet mostra como criar um Spark DataFrame para ler a partir de Cosmos DB em PySpark.

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

### <a name="batch-writes-to-cosmos-db"></a>Batch escreve para Cosmos DB

O seguinte corte mostra como escrever um quadro de dados para Cosmos DB em PySpark.

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
flights.write.mode("overwrite").format("com.microsoft.azure.cosmosdb.spark").options(
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

O seguinte corte mostra como ligar e ler a partir de Azure Cosmos DB Change Feed.

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

O seguinte corte mostra como escrever um quadro de dados para Cosmos DB em PySpark.

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
Mais cortes e mais amostras finais, ver [Jupyter.](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks)

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a> Trabalhar com o conector

Pode construir o conector a partir de fonte no GitHub, ou baixar os frascos uber de Maven nos links abaixo.

| Spark | Scala | Versão mais recente |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Usando cadernos databricks

Crie uma biblioteca utilizando o seu espaço de trabalho Databricks seguindo as orientações no Guia de Dados Azure > [Utilize o conector Azure Cosmos DB Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> A **página de Utilização do Conector de Faíscas DB Azure Cosmos** não está atualmente atualizada. Em vez de descarregar os seis frascos separados em seis bibliotecas diferentes, você pode baixar o frasco uber de maven em [azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) e instalar este frasco/biblioteca.
> 

### <a name="using-spark-cli"></a>Usando spark-cli

Para trabalhar com o conector utilizando o spark-cli (isto é, `spark-shell` , , ), pode utilizar o parâmetro com as `pyspark` `spark-submit` `--packages` [coordenadas maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)do conector .

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Usando cadernos jupyter

Se estiver a usar o Jupyter Notebook dentro do HDInsight, pode utilizar uma célula mágica `%%configure` para especificar as coordenadas de maven do conector.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Note que a inclusão do `spark.jars.excludes` é específica para remover potenciais conflitos entre o conector, Apache Spark, e Livy.

### <a name="build-the-connector"></a>Construa o conector

Atualmente, este projeto de conector utiliza `maven` para construir sem dependências, pode correr:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Trabalhar com as nossas amostras

O [repositório Cosmos DB Spark GitHub](https://github.com/Azure/azure-cosmosdb-spark) tem os seguintes cadernos e scripts de amostra que você pode experimentar.

* **Performance de voo a tempo com Spark e Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Ligue a Spark à Cosmos DB utilizando o serviço HDInsight Jupyter Notebook para mostrar Spark SQL, GraphFrames e prever atrasos de voo usando gasodutos ML.
* **Twitter Source with Apache Spark and Azure Cosmos DB Change Feed**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Usando Apache Spark para consultar cosmos DB Graphs**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Ligação de dados Azure a Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** utilizando `azure-cosmosdb-spark` .  Aqui também está uma versão Azure Databricks do caderno de desempenho de voo no [tempo.](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)
* **[Arquitetura Lambda com Azure Cosmos DB e HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Pode reduzir a sobrecarga operacional da manutenção de grandes oleodutos de dados utilizando Cosmos DB e Spark.

## <a name="more-information"></a>Mais Informações

Temos mais informações no `azure-cosmosdb-spark` [wiki,](https://github.com/Azure/azure-cosmosdb-spark/wiki) incluindo:

* [Guia de utilizador do conector de faíscaS Azure Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Agregações Exemplos](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Configuração e Definições

* [Configuração do conector de faíscas](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Configuração do conector DB da Faísca para Cosmos](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (em curso)
* [Consulta de Power BI Consulta Direta a Azure Cosmos DB via Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Resolução de problemas

* [Usando os agregados DeSB cosmos](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Problemas Conhecidos](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Desempenho

* [Dicas de Desempenho](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Teste de consulta corre](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Execuções de teste de escrita](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Feed de Alterações

* [Alterações de processamento de fluxo usando Azure Cosmos DB Change Feed e Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Alterar demonstrações de alimentação](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Demos de fluxo estruturado](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitorização

* [Monitorização de empregos de faíscas com insights de aplicação](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, baixe o conector Spark para Azure Cosmos DB do [repositório azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub. Explore os seguintes recursos adicionais no repo:

* [Exemplos de agregações](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Scripts e cadernos de amostras](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Também pode querer rever o [Apache Spark SQL, DataFrames e Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html), e o apache spark no artigo [Azure HDInsight.](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)