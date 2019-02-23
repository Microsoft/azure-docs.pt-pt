---
title: Ligar o Apache Spark para Azure Cosmos DB
description: Saiba mais sobre o conector do Spark do Azure Cosmos DB permite-lhe ligar o Apache Spark para Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: ramkris
ms.openlocfilehash: 99c0675f8dc7f392e6c98abbe43b84eb14dbddbc
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675352"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Acelerar a análise de macrodados utilizando o Apache Spark para o conector do Azure Cosmos DB

Pode executar [Spark](https://spark.apache.org/) tarefas com os dados armazenados no Azure Cosmos DB com o conector do Spark do Cosmos DB. Cosmos pode ser utilizado para batch e de processamento de fluxos e como uma camada de entrega para o acesso de baixa latência.

Pode utilizar o conector com [Azure Databricks](https://azure.microsoft.com/services/databricks) ou [o Azure HDInsight, que fornecem geridos os clusters do Spark no Azure. A tabela seguinte mostra as versões de Spark suportado.

| Componente | Versão |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x e 2.1.x |
| Scala | 2.11 |
| Versão de runtime do Azure Databricks | > 3.4 |

> [!WARNING]
> Este conector suporta a API do core (SQL) do Azure Cosmos DB.
> Para o Cosmos DB para a MongoDB API, utilize o [conector do Spark de MongoDB](https://docs.mongodb.com/spark-connector/master/).
> Para a API de Cassandra do Cosmos DB, utilize o [conector do Spark de Cassandra](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Início Rápido

* Siga os passos indicados em [começar com o SDK de Java](sql-api-async-java-get-started.md) para configurar uma conta do Cosmos DB e insira alguns dados.
* Siga os passos indicados em [de introdução do Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) para configurar uma área de trabalho do Azure Databricks e o cluster.
* Agora pode criar novos blocos de notas e importar a biblioteca de conectores do Cosmos DB. Ir para [trabalhar com o conector do Cosmos DB](#bk_working_with_connector) para obter detalhes sobre como configurar a sua área de trabalho.
* A secção seguinte tem fragmentos sobre como ler e escrever com o conector.

### <a name="reading-from-cosmos-db"></a>Ler a partir do Cosmos DB

O fragmento seguinte mostra como criar um DataFrame do Spark para ler a partir do Cosmos DB no PySpark.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

E o mesmo fragmento de código no Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
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

### <a name="writing-to-cosmos-db"></a>Escrever no Cosmos DB

O fragmento seguinte mostra como escrever um quadro de dados para Cosmos DB no PySpark.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

E o mesmo fragmento de código no Scala:

```scala
// Configure connection to the sink collection
val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Upsert the dataframe to Cosmos DB
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Mais mais trechos de código e amostras de ponto a ponto, consulte [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="bk_working_with_connector"></a> Trabalhar com o conector

Pode criar o conector de origem no Github ou transferir os jars uber a partir do Maven nas ligações abaixo.

| Spark | Scala | Versão mais recente |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.3.5](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2)

### <a name="using-databricks-notebooks"></a>Utilizar blocos de notas do Databricks

Criar uma biblioteca com dentro de sua área de trabalho do Databricks ao seguir as orientações no Guia do Databricks do Azure > [utilizar o conector do Spark do Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Tenha em atenção que o **utilizar o conector do Spark do Azure Cosmos DB** página atualmente não está atualizada. Em vez de transferirem as seis jars separados em seis bibliotecas diferentes, pode baixar o jar uber do maven na https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar) e instalar esta biblioteca/jar um.
> 

### <a name="using-spark-cli"></a>Com a cli do spark

Para trabalhar com o conector com a cli do spark (ou seja, `spark-shell`, `pyspark`, `spark-submit`), pode utilizar os `--packages` parâmetro com o conector [coordenadas do maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5"

```

### <a name="using-jupyter-notebooks"></a>Utilizar blocos de notas do Jupyter

Se estiver a utilizar blocos de notas do Jupyter no HDInsight, pode utilizar o spark mágica `%%configure` célula para especificar as coordenadas do maven o conector.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Nota, a inclusão do `spark.jars.excludes` é específico para remover a possíveis conflitos entre o conector, o Apache Spark e o Livy.

### <a name="build-the-connector"></a>Criar o conector

Atualmente, esse projeto de conector usa `maven` criar sem dependências, pode executar:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Trabalhar com os nossos exemplos

O [repositório do GitHub de Spark do Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark) tem os seguintes blocos de notas de exemplo e os scripts que pode experimentar.

* **No tempo de desempenho de voo com o Spark e Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Ligar o Spark para o Cosmos DB com o serviço de bloco de notas do Jupyter do HDInsight para demonstrar o Spark SQL, GraphFrames e atrasos de voos prever com pipelines de ML.
* **[Ligar o Spark com o feed de alterações do Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark%2Band%2BCosmos%2BDB%2BChange%2BFeed.ipynb)**: Uma demonstração rápida sobre como ligar o Spark para o Feed de alterações do Cosmos DB.
* **Origem com o Apache Spark e o Azure Cosmos DB alterar Feed do twitter**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Com o Apache Spark para consultas de gráficos do Cosmos DB**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Ligar o Azure Databricks para o Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**  usando `azure-cosmosdb-spark`.  Ligado aqui também é uma versão do Azure Databricks dos [bloco de notas do vôo de desempenho de chegadas](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Arquitetura de lambda com o Azure Cosmos DB e o HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Pode reduzir os custos operacionais da manutenção pipelines de macrodados com o Cosmos DB e do Spark.

## <a name="more-information"></a>Mais Informações

Temos mais informações a `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) incluindo:

* [Guia de utilizador do Azure Cosmos DB Spark Connector](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Exemplos de agregações](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Configuração e instalação

* [Configuração do conector do Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark para configuração do conector do Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (em curso)
* [Configurar o Power BI Direct Query ao Azure Cosmos DB através do Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Resolução de problemas

* [Utiliza agregados do Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Problemas Conhecidos](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Desempenho

* [Sugestões de desempenho](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Execuções de teste de consulta](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Execuções de testes de escrita](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Feed de alterações

* [Alterações de processamento do Stream com o Azure Cosmos DB de Feed de alterações e do Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Demonstrações de Feed de alterações](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Demonstrações do Stream estruturado](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitorização

* [Monitorização de tarefas do Spark com o application insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Passos Seguintes

Se ainda não o fez, transfira o Spark para o conector do Azure Cosmos DB a partir da [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark) repositório do GitHub. Explore os seguintes recursos adicionais no repositório:

* [Exemplos de agregações](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Scripts de exemplo e blocos de notas](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Pode também querer rever os [guia de conjuntos de dados, Apache Spark SQL e DataFrames](https://spark.apache.org/docs/latest/sql-programming-guide.html)e o [Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artigo.
