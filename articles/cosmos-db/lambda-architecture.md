---
title: Arquitetura de lambda com o Azure Cosmos DB e o HDInsight (Apache Spark)
description: Este artigo descreve como implementar uma arquitetura de lambda com o Azure Cosmos DB, o HDInsight e o Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 6902b1a26d02efbf1a31fe9a3a25253a6b5a5604
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100348"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>o Azure Cosmos DB: Implementar uma arquitetura de lambda na plataforma do Azure 

Arquiteturas de lambda ativar o processamento eficiente de dados de grandes conjuntos de dados. Arquiteturas de lambda utilizam processamento em lote, processamento de fluxo e uma camada de entrega para minimizar a latência envolvidos na consulta de macrodados. 

Implementar uma arquitetura de lambda no Azure, pode combinar as seguintes tecnologias para acelerar a análise de macrodados em tempo real:
* [O Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), primeiro serviço de base de dados com múltiplos modelos distribuída globalmente, do setor. 
* [Apache Spark para Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), uma arquitetura de processamento que executa aplicações de análise de dados em grande escala
* O Azure Cosmos DB [feed de alterações](change-feed.md), que transmite os dados novos para a camada de lotes para HDInsight processar
* O [Spark para o conector do Azure Cosmos DB](spark-connector.md)

Este artigo descreve os conceitos básicos de uma arquitetura de lambda com base no design de camada multi original e os benefícios de uma arquitetura de lambda "interoperabilidadde" que simplifica a operações.  

## <a name="what-is-a-lambda-architecture"></a>O que é uma arquitetura de lambda?
Uma arquitetura de lambda é um genérico, dimensionável, e dados tolerante a falhas de arquitetura para o endereço de processamento do batch e aceleram os cenários de latência, conforme descrito pelo [Nathan Marz](https://twitter.com/nathanmarz).

![Diagrama que mostra uma arquitetura de lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Origem: http://lambda-architecture.net/

Os princípios básicos de uma arquitetura de lambda são descritos no diagrama anterior, de acordo [ http://lambda-architecture.net ](http://lambda-architecture.net/).

 1. Todos os **dados** emitidos via push para *ambos* o *camada de lotes* e *camada de velocidade*.
 2. O **camada de lotes** tem um conjunto de dados mestre (imutável, só de acréscimo conjunto de dados não processados) e computa previamente as exibições do batch.
 3. O **camada de entrega** tem vistas de batch para consultas rápidas. 
 4. O **camada de velocidade** compensa o tempo de processamento (para a camada de entrega) e lida com apenas dados recentes.
 5. Todas as consultas podem ser respondidas pelos resultados mesclagem de vistas de batch e exibições em tempo real ou ping-los individualmente.

Após a leitura complementar, será capazes de implementar esta arquitetura com apenas o seguinte:

* Coleções do Azure Cosmos DB
* Cluster do HDInsight (Apache Spark 2.1)
* Conector do spark [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Camada de velocidade

Da perspectiva de operações, manter dois fluxos de dados, garantindo o estado correto dos dados pode ser uma tarefa complicada. Para simplificar as operações, utilizar o [suporte de feed de alterações do Azure Cosmos DB](change-feed.md) para manter o estado para o *camada de lotes* ao revelando o registo de alterações do Azure Cosmos DB através do *alterar Feed API* para a sua *camada de velocidade*.  
![Diagrama de realce a novos dados, a camada de velocidade e a parte de um conjunto de dados mestre da arquitetura de lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

O que é importante para essas camadas:

 1. Todos os **dados** são emitidos via push *apenas* para o Azure Cosmos DB, assim, pode evitar problemas de multicasting.
 2. O **camada de lotes** tem um conjunto de dados mestre (imutável, só de acréscimo conjunto de dados não processados) e computa previamente as exibições do batch.
 3. O **camada de entrega** é abordado na secção seguinte.
 4. O **camada de velocidade** utiliza o HDInsight (Apache Spark) para ler o feed de alterações do Azure Cosmos DB. Isto permite-lhe manter os seus dados, bem como para consultar e processá-lo em simultâneo.
 5. Todas as consultas podem ser respondidas pelos resultados mesclagem de vistas de batch e exibições em tempo real ou ping-los individualmente.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Exemplo de código: Spark structured streaming para um feed de alterações do Azure Cosmos DB
Para executar um protótipo rápido da alteração do Azure Cosmos DB feed como parte do **camada de velocidade**, poderá testá-la usando dados do Twitter como parte do [as alterações de processamento do Stream com o Feed do Azure Cosmos DB alterar e do Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)exemplo. Para iniciar a saída do Twitter, consulte o código de exemplo na [Stream feed do Twitter ao Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). O exemplo anterior, está carregando dados do Twitter para o Azure Cosmos DB e, em seguida, pode configurar o cluster do HDInsight (Apache Spark) para ligar ao feed de alterações. Para obter mais informações sobre como configurar esta configuração, consulte [Apache Spark para o programa de configuração do conector do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

O fragmento de código seguinte mostra como configurar `spark-shell` para executar uma estruturados tarefa transmissão em fluxo para ligar a um feed, de alterações do Azure Cosmos DB que analisa o fluxo de dados no Twitter em tempo real, para efetuar uma contagem contínua de intervalo.

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

Para exemplos de código completo, consulte [azure-cosmos DB-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), incluindo:
* [Consulta de transmissão em fluxo de Feed.scala de alteração do Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Transmissão em fluxo de etiquetas de consulta do Cosmos DB alteração Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

O resultado disso é um `spark-shell` console, que é continuamente executada uma tarefa de transmissão em fluxo estruturada que realiza uma contagem do intervalo em relação as dados do Twitter a partir do feed de alterações do Azure Cosmos DB. A imagem seguinte mostra a saída da tarefa do stream e o intervalo de contagens.

![Transmissão em fluxo de saída que mostra a contagem de intervalo com os dados do Twitter do feed de alterações do Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Para obter mais informações sobre o Azure Cosmos DB feed de alterações, consulte:

* [Trabalhar com a alteração de suporte de feed no Azure Cosmos DB](change-feed.md)
* [Apresentando a alteração do cosmos DB do Azure do biblioteca processador do Feed](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Stream processamento de alterações: Azure cosmos DB feed de alterações + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch e a servir de camadas
Uma vez que os novos dados são carregados no Azure Cosmos DB (em que o feed de alterações está a ser utilizado para a camada de velocidade), isso é o local onde o **conjunto de dados mestra** (um imutável só de acréscimo conjunto de dados não processados) reside. A partir deste ponto em diante, utilizar o HDInsight (Apache Spark) para realizar as funções de pré-computação de a partir da **camada de lotes** ao **camada de entrega**, conforme mostrado na imagem seguinte:

![Diagrama de destaque para a camada de lotes e a camada de entrega da arquitetura de lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

O que é importante para essas camadas:

 1. Todos os **dados** são emitidos via push apenas para o Azure Cosmos DB (para evitar problemas de multicast).
 2. O **camada de lotes** tem um principal conjunto de dados (imutável, só de acréscimo conjunto de dados não processados) armazenado no Azure Cosmos DB. Utilizar HDI Spark, poderá computar previamente as suas agregações sejam armazenados em seus modos de exibição do batch calculada.
 3. O **camada de entrega** é uma base de dados do Azure Cosmos DB com coleções para o conjunto de dados mestra e calculada de vista de lotes.
 4. O **camada de velocidade** será discutido neste artigo.
 5. Todas as consultas podem ser respondidas através da intercalação resultados entre as exibições de batch e exibições em tempo real, ou ping-los individualmente.

### <a name="code-example-pre-computing-batch-views"></a>Exemplo de código: Pré-vistas de batch de computação
Para demonstrar como executar previamente calculadas exibições em relação a seus **conjunto de dados mestre** a partir do Apache Spark para Azure Cosmos DB, utilize os seguintes fragmentos de código a partir de blocos de notas [Lambda arquitetura Rearquitetado - camada de lotes ](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) e [Rearquitetado de arquitetura de Lambda - Batch para que serve a camada](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). Neste cenário, utilize os dados armazenados no Azure Cosmos DB do Twitter.

Vamos começar por criar a ligação de configuração para os dados do Twitter na Azure Cosmos DB com o código de PySpark abaixo.

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

Em seguida, vamos executar a seguinte instrução de SQL do Spark para determinar as hashtags 10 principais do conjunto de tweets. Para esta consulta do Spark SQL, estamos a executar num bloco de notas do Jupyter sem o gráfico de barras de saída diretamente a seguir a este fragmento de código.

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

![Gráfico que mostra o número de tweets por hashtag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Agora que tem a sua consulta, vamos guardá-los novamente uma coleção, utilizando o conector do Spark para guardar os dados de saída numa coleção diferente.  Neste exemplo, utilize o Scala para demonstrar a ligação. É semelhante ao exemplo anterior, crie a ligação de configuração para guardar o pacote de dados do Apache Spark para uma coleção diferente do Azure Cosmos DB.

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

Depois de especificar o `SaveMode` (que indica se deve `Overwrite` ou `Append` documentos), criar um `tweets_bytags` DataFrame semelhante à consulta de Spark SQL no exemplo anterior.  Com o `tweets_bytags` DataFrame criado, pode salvá-lo a utilizar o `write` método usando especificado anteriormente `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Esta última instrução agora salvou o DataFrame do Spark numa nova coleção do Azure Cosmos DB; de uma perspectiva da arquitetura de lambda, esta é sua **vista de lotes** dentro do **camada de entrega**.
 
#### <a name="resources"></a>Recursos

Para exemplos de código completo, consulte [azure-cosmos DB-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) incluindo:
* Rearchitected de arquitetura de lambda - camada de lotes [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Rearchitected de arquitetura de lambda - Batch para que serve camada [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Camada de velocidade
Conforme anteriormente mencionado, utilizando a biblioteca de Feed de alterações do Azure Cosmos DB permite-lhe simplificar as operações entre as camadas de batch e a velocidade. Nesta arquitetura, usar o Apache Spark (por meio do HDInsight) para executar o *transmissão em fluxo estruturada* consultas nos dados. Também poderá manter temporariamente os resultados das suas consultas de transmissão em fluxo estruturadas, para que outros sistemas podem aceder a estes dados.

![Diagrama de realce a camada de velocidade da arquitetura de lambda](./media/lambda-architecture/lambda-architecture-speed.png)

Para fazer isso, crie uma coleção separada do Azure Cosmos DB para guardar os resultados das suas consultas de transmissão em fluxo estruturadas.  Isso permite que tenha acesso de outros sistemas estas informações não apenas do Apache Spark. Também com a funcionalidade Time-to-Live (TTL) do Cosmos DB, pode configurar os documentos para serem eliminados automaticamente após um período de tempo definido.  Para obter mais informações sobre a funcionalidade de valor de TTL do Azure Cosmos DB, consulte [expirar os dados nas coleções do Azure Cosmos DB automaticamente com o tempo de duração](time-to-live.md)

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

## <a name="lambda-architecture-rearchitected"></a>Arquitetura de lambda: Revistos
Conforme indicado nas secções anteriores, pode simplificar a arquitetura de lambda original ao utilizar os seguintes componentes:
* Azure Cosmos DB
* A biblioteca de Feed de alterações do Azure Cosmos DB evitar a necessidade de multicast seus dados entre as camadas de batch e velocidade
* Apache Spark no HDInsight
* O conector do Spark para o Azure Cosmos DB

![Diagrama que mostra a reestruturação da arquitetura lambda com o Azure Cosmos DB, Spark e a API de Feed de alterações do Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-re-architected.png)

Com esta estrutura, só precisa de dois serviços geridos do Azure Cosmos DB e HDInsight. Juntos, eles abordam o batch, entrega e as camadas de velocidade da arquitetura de lambda. Isso simplifica não apenas as operações, mas também o fluxo de dados. 
 1. Todos os dados são direcionados para o Azure Cosmos DB para processamento
 2. A camada de lotes tem um conjunto de dados mestre (imutável, só de acréscimo conjunto de dados não processados) e computa previamente as exibições de batch
 3. A camada de entrega tem vistas de lote de dados para consultas rápidas.
 4. A camada de velocidade compensa o tempo de processamento (para a camada de entrega) e lida com apenas dados recentes.
 5. Todas as consultas podem ser respondidas, mesclando resultados dos modos de exibição do batch e exibições em tempo real.

### <a name="resources"></a>Recursos

* **Novos dados**: O [stream feed do Twitter para CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), que é o mecanismo para enviar dados novos para o Azure Cosmos DB.
* **Camada de lotes:** A camada de lotes é composta do *conjunto de dados mestre* (um imutável só de acréscimo conjunto de dados não processados) e a capacidade de computar previamente os modos de exibição de lotes dos dados que são emitidos via push para o **camada de entrega**.
   * O **Lambda arquitetura Rearquitetado - camada de lotes** bloco de notas [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) consultas o *conjunto de dados mestra* conjunto de vistas do batch.
* **Camada de entrega:** O **camada de entrega** é composto por dados pré-calculadas, resultando em vistas de lote (por exemplo, agregações, segmentações de dados específicas, etc.) para consultas rápidas.
  * O **Lambda arquitetura Rearquitetado - Batch para que serve camada** bloco de notas [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) envia os dados de lote para a camada de entrega; ou seja, do Spark consulta uma coleção de batch de tweets, processa e armazena-os em outra coleção (um lote calculada).
    * **Camada de velocidade:** O **camada de velocidade** é composto por Spark que utiliza o feed para ler e agir sobre imediatamente de alterações do Azure Cosmos DB. Os dados também podem ser guardados *calculada RT* , para que outros sistemas podem consultar os dados processados em tempo real em vez de em execução um em tempo real de consulta propriamente ditas.
  * O [consulta de transmissão em fluxo do Cosmos DB alterar Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala script executa uma consulta de transmissão em fluxo com a mudança do Azure Cosmos DB feed para computar uma contagem do intervalo do shell do spark.
  * O [consulta de etiquetas de transmissão em fluxo do Cosmos DB alterar Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala script executa uma consulta de transmissão em fluxo com a mudança do Azure Cosmos DB feed para computar uma contagem do intervalo das etiquetas da shell do spark.
  
## <a name="next-steps"></a>Passos Seguintes
Se ainda não o fez, transfira o Spark para o conector do Azure Cosmos DB a partir da [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark) repositório do GitHub e explorar os recursos adicionais no repositório:
* [Arquitetura de lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Exemplos de agregações distribuída](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Scripts de exemplo e blocos de notas](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Demonstrações de transmissão em fluxo estruturada](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Demonstrações de feed de alterações](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Stream processamento de alterações com Feed de alterações do Azure Cosmos DB e do Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Pode também querer rever os [guia de conjuntos de dados, Apache Spark SQL e DataFrames](https://spark.apache.org/docs/latest/sql-programming-guide.html) e o [Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artigo.
