---
title: Arquitetura Lambda com Azure Cosmos DB e Apache Spark
description: Este artigo descreve como implementar uma arquitetura lambda usando Azure Cosmos DB, HDInsight e Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 68ce06d8a2904bf99f58a53817444b2992b23501
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76719743"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Implementar uma arquitetura lambda na plataforma Azure 

As arquiteturas Lambda permitem um processamento eficiente de dados de conjuntos de dados maciços. As arquiteturas Lambda usam processamento de lotes, processamento de fluxo e uma camada de serviço para minimizar a latência envolvida na consulta de big data. 

Para implementar uma arquitetura de lambda no Azure, pode combinar as seguintes tecnologias para acelerar a análise de big data em tempo real:
* [Azure Cosmos DB,](https://azure.microsoft.com/services/cosmos-db/)o primeiro serviço de base de dados multimodelo distribuído globalmente pela indústria. 
* [Apache Spark para Azure HDInsight,](https://azure.microsoft.com/services/hdinsight/apache-spark/)um quadro de processamento que executa aplicações de análise de dados em larga escala
* Azure Cosmos DB [change feed](change-feed.md), que transmite novos dados para a camada de lote para hDInsight para processar
* O [Conector DB Da Faísca para O Cosmos Azure](spark-connector.md)

Este artigo descreve os fundamentos de uma arquitetura lambda baseada no design original de várias camadas e os benefícios de uma arquitetura lambda "rearquitetada" que simplifica as operações.  

## <a name="what-is-a-lambda-architecture"></a>O que é uma arquitetura de lambda?
Uma arquitetura lambda é uma arquitetura genérica, escalável e tolerante ao processamento de dados para abordar cenários de latência de lote e velocidade, como descrito por [Nathan Marz.](https://twitter.com/nathanmarz)

![Diagrama mostrando uma arquitetura lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Fonte: http://lambda-architecture.net/

Os princípios básicos de uma arquitetura lambda são [http://lambda-architecture.net](http://lambda-architecture.net/)descritos no diagrama anterior como .

 1. Todos os **dados** são empurrados *para a* camada de *lote* e para a camada de *velocidade*.
 2. A camada de **lote** tem um conjunto de dados principal (imutável, apenas apêndice conjunto de dados brutos) e pré-calcula as vistas do lote.
 3. A **camada de serviço** tem vista para o lote para consultas rápidas. 
 4. A **camada de velocidade** compensa o tempo de processamento (para a camada de serviço) e lida apenas com dados recentes.
 5. Todas as consultas podem ser respondidas através da fusão de resultados a partir de vistas de lote e vistas em tempo real ou pingando-as individualmente.

Após uma nova leitura, poderemos implementar esta arquitetura usando apenas o seguinte:

* Contentores Azure Cosmos
* Aglomerado HDInsight (Apache Spark 2.1)
* Conector de faísca [1.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Camada de velocidade

Do ponto de vista das operações, manter dois fluxos de dados, garantindo ao mesmo tempo o estado correto dos dados, pode ser um esforço complicado. Para simplificar as operações, utilize o suporte de alimentação de mudança De DB Do [Azure Cosmos](change-feed.md) para manter o estado para a camada de *lote,* enquanto revela o registo de mudança de DB Azure Cosmos através da *API change feed* para a sua camada de *velocidade*.  
![Diagrama destacando os novos dados, camada de velocidade e porção de conjunto de dados mestre da arquitetura lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

O que é importante nestas camadas:

 1. Todos os **dados** são empurrados *apenas* para o Azure Cosmos DB, pelo que pode evitar problemas de várias fundição.
 2. A camada de **lote** tem um conjunto de dados principal (imutável, apenas apêndice conjunto de dados brutos) e pré-calcula as vistas do lote.
 3. A camada de **serviço** é discutida na secção seguinte.
 4. A **camada de velocidade** utiliza o HDInsight (Apache Spark) para ler o feed de mudança de DB Azure Cosmos. Isto permite-lhe persistir os seus dados, bem como consultar e processá-lo simultaneamente.
 5. Todas as consultas podem ser respondidas através da fusão de resultados a partir de vistas de lote e vistas em tempo real ou pingando-as individualmente.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Exemplo de código: Fluxo estruturado de faíscas para um feed de mudança de DB Do Cosmos Azure
Para executar um protótipo rápido do feed de mudança de DB Azure Cosmos como parte da camada de **velocidade,** pode testá-lo usando dados do Twitter como parte das Alterações de Processamento de Fluxo usando o exemplo de Mudança de DB Do [Azure Cosmos e Apache Spark.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) Para iniciar a sua saída do Twitter, veja a amostra de código no [Feed Stream do Twitter para cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). Com o exemplo anterior, está a carregar dados do Twitter para o Azure Cosmos DB e pode depois configurar o seu cluster HDInsight (Apache Spark) para se ligar ao feed de mudança. Para obter mais informações sobre como configurar esta configuração, consulte a Configuração do [Conector DB Da Apache Spark para o Azure Cosmos](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup)DB .  

O seguinte código snippet mostra `spark-shell` como configurar para executar um trabalho de streaming estruturado para ligar a um feed de mudança de DB Azure Cosmos, que analisa o fluxo de dados do Twitter em tempo real, para executar uma contagem de intervalos em execução.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Para obter amostras completas de código, consulte [azure-cosmosdb-spark/lambda/samples,](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)incluindo:
* [Consulta de streaming de Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Streaming Tags Consulta de Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

A saída desta `spark-shell` é uma consola, que executa continuamente um trabalho de streaming estruturado que executa uma contagem de intervalos contra os dados do Twitter do feed de mudança de DB Do Azure Cosmos. A imagem seguinte mostra a saída do trabalho de fluxo e o intervalo conta.

![Saída de streaming mostrando a contagem de intervalos contra os dados do Twitter do feed de mudança de DB Do Azure Cosmos](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Para mais informações sobre o feed de mudança de DB da Azure Cosmos, consulte:

* [Trabalhando com o suporte de feed de mudança em Azure Cosmos DB](change-feed.md)
* [Apresentando a Biblioteca de Processadores de Mudanças Azure CosmosDB](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Alterações no processamento de fluxo: Azure CosmosDB muda feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Lote e servir camadas
Uma vez que os novos dados são carregados no Azure Cosmos DB (onde o feed de mudança está a ser usado para a camada de velocidade), é aqui que reside o conjunto de **dados principal** (um conjunto imutável, apenas apêndice de dados brutos). A partir deste ponto, utilize o HDInsight (Apache Spark) para executar as funções pré-computadas desde a camada de **lote** até à camada de **serviço,** como mostra a seguinte imagem:

![Diagrama destacando a camada do lote e a camada de serviço da arquitetura lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

O que é importante nestas camadas:

 1. Todos os **dados** são empurrados apenas para o Azure Cosmos DB (para evitar problemas multi-elenco).
 2. A camada de **lote** tem um conjunto de dados principal (imutável, apenas apêndice de dados brutos) armazenado em Azure Cosmos DB. Utilizando o HDI Spark, pode pré-calcular as suas agregações para serem armazenadas nas vistas do lote computado.
 3. A **camada de serviço** é uma base de dados Azure Cosmos com coleções para o conjunto de dados principal e vista computorizada do lote.
 4. A **camada de velocidade** é discutida mais tarde neste artigo.
 5. Todas as consultas podem ser respondidas através da fusão de resultados a partir das vistas do lote e vistas em tempo real, ou pingando-as individualmente.

### <a name="code-example-pre-computing-batch-views"></a>Exemplo de código: Vistas de lote pré-computação
Para mostrar como executar vistas pré-calculadas contra o seu conjunto de **dados master** de Apache Spark para Azure Cosmos DB, use os seguintes códigos dos cadernos [Lambda Architecture Rearchitected - Batch Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) e [Lambda Architecture Rearchitected - Batch to Serving Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). Neste cenário, utilize os dados do Twitter armazenados no Azure Cosmos DB.

Vamos começar por criar a ligação de configuração aos dados do Twitter dentro do Azure Cosmos DB usando o código PySpark abaixo.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Em seguida, vamos executar a seguinte declaração Spark SQL para determinar as 10 hashtags top 10 do conjunto de tweets. Para esta consulta Spark SQL, estamos executando isto em um caderno Jupyter sem o gráfico de barras de saída diretamente seguindo este código snippet.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Gráfico mostrando o número de tweets por hashtag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Agora que tem a sua consulta, vamos guardá-la de volta para uma coleção usando o Spark Connector para guardar os dados de saída numa coleção diferente.  Neste exemplo, utilize o Scala para mostrar a ligação. Semelhante ao exemplo anterior, crie a ligação de configuração para salvar o Apache Spark DataFrame para um recipiente Azure Cosmos diferente.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Depois de `SaveMode` especificar o (indicando se `Overwrite` ou `Append` documentos), crie um `tweets_bytags` DataFrame semelhante à consulta Spark SQL no exemplo anterior.  Com `tweets_bytags` o DataFrame criado, pode `write` guardá-lo utilizando `writeConfig`o método utilizando o anteriormente especificado .

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Esta última declaração agora guardou o seu Spark DataFrame num novo contentor Azure Cosmos; do ponto de vista da arquitetura lambda, esta é a sua vista de **lote** dentro da camada de **serviço.**
 
#### <a name="resources"></a>Recursos

Para obter amostras completas de código, consulte [azure-cosmosdb-spark/lambda/samples,](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) incluindo:
* Arquitetura Lambda Rearchitected - Camada de Lote [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Arquitetura Lambda Rearchitected - Lote para servir camada [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Camada de velocidade
Como anteriormente notado, a utilização da Biblioteca de Feed de Mudança de DB Do Azure Cosmos permite-lhe simplificar as operações entre o lote e as camadas de velocidade. Nesta arquitetura, utilize a Apache Spark (via HDInsight) para realizar as consultas *de streaming estruturadas* contra os dados. Também pode querer persistir temporariamente os resultados das suas consultas de streaming estruturadas para que outros sistemas possam aceder a estes dados.

![Diagrama destacando a camada de velocidade da arquitetura lambda](./media/lambda-architecture/lambda-architecture-speed.png)

Para isso, crie um recipiente Azure Cosmos separado para guardar os resultados das suas consultas de streaming estruturadas.  Isto permite-lhe ter outros sistemas de acesso a esta informação e não apenas ao Apache Spark. Além da funcionalidade Cosmos DB Time-to-Live (TTL), pode configurar os seus documentos para serem automaticamente eliminados após uma duração definida.  Para obter mais informações sobre a funcionalidade Azure Cosmos DB TTL, consulte [os dados expirados em contentores Azure Cosmos automaticamente com tempo para viver](time-to-live.md)

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Arquitetura Lambda: Rearchitected
Como se nota nas secções anteriores, pode simplificar a arquitetura original da lambda utilizando os seguintes componentes:
* Azure Cosmos DB
* A Biblioteca de Feed de Mudança de DB Azure Cosmos para evitar a necessidade de lançar multi-elenco os seus dados entre o lote e as camadas de velocidade
* Faísca Apache no HDInsight
* O Conector de Faíscas para Azure Cosmos DB

![Diagrama mostrando a rearquitetura da arquitetura lambda usando Azure Cosmos DB, Spark, e o Azure Cosmos DB Change Feed API](./media/lambda-architecture/lambda-architecture-re-architected.png)

Com este design, só precisa de dois serviços geridos, Azure Cosmos DB e HDInsight. Juntos, abordam o lote, servindo e as camadas de velocidade da arquitetura lambda. Isto simplifica não só as operações, mas também o fluxo de dados. 
 1. Todos os dados são empurrados para o Azure Cosmos DB para processamento
 2. A camada de lote tem um conjunto de dados principal (imutável, apenas apêndice conjunto de dados brutos) e pré-computa as vistas do lote
 3. A camada de serviço tem vista para o lote de dados para consultas rápidas.
 4. A camada de velocidade compensa o tempo de processamento (para a camada de serviço) e lida apenas com dados recentes.
 5. Todas as consultas podem ser respondidas através da fusão de resultados a partir de vistas de lote e vistas em tempo real.

### <a name="resources"></a>Recursos

* **Novos dados**: O [feed de transmissão do Twitter para cosmosDB,](https://github.com/tknandu/TwitterCosmosDBFeed)que é o mecanismo para empurrar novos dados para o Azure Cosmos DB.
* **Camada de lote:** A camada de lote é composta pelo conjunto de *dados principal* (um conjunto imutável, apenas apêndice de dados brutos) e a capacidade de pré-calcular as vistas do lote dos dados que são empurrados para a camada de **serviço**.
   * O **Lambda Architecture Rearchitected - Batch Layer** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) consulta o conjunto de *dados principal* de vistas de lote.
* **Camada de serviço:** A camada de **serviço** é composta por dados pré-calculados que resultam em vistas de lote (por exemplo, agregações, cortadores específicos, etc.) para consultas rápidas.
  * O **Lambda Architecture Rearchitected - Lote para servir o** portátil [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) empurra os dados do lote para a camada de serviço; ou seja, a Spark consulta uma coleção de tweets, processa-a e armazena-a noutra coleção (um lote computorizado).
    * **Camada de velocidade:** A **camada de velocidade** é composta por Spark utilizando o feed de mudança de Db Azure Cosmos para ler e agir imediatamente. Os dados também podem ser guardados para *RT computada* para que outros sistemas possam consultar os dados processados em tempo real em vez de executar em tempo real uma consulta em tempo real.
  * O streaming [de consulta do cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala executa uma consulta de streaming do feed de mudança de Db Azure Cosmos para calcular uma contagem de intervalos a partir da casca de faísca.
  * O streaming [de tags consulta do cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala script executa uma consulta de streaming do feed de mudança de Db Azure Cosmos para calcular uma contagem de intervalos de tags da casca de faísca.
  
## <a name="next-steps"></a>Passos seguintes
Se ainda não o fez, descarregue o conector Spark to Azure Cosmos DB do repositório GitHub de [faísca azure-cosmosdb](https://github.com/Azure/azure-cosmosdb-spark) e explore os recursos adicionais no repo:
* [Arquitetura de lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Exemplos de agregações distribuídas](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Exemplo de scripts e cadernos](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Demonstrações de streaming estruturadas](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Alterar demonstrações de alimentação](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Mudanças de processamento de fluxo usando Azure Cosmos DB Change Feed e Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Também pode querer rever o [Apache Spark SQL, DataFrames e Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) e o Apache Spark no artigo [Azure HDInsight.](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
