---
title: 'Tutorial: Deteção de anomalias em dados de streaming utilizando Azure Databricks'
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar o Detetor de Anomalias API e a Azure Databricks para monitorizar anomalias nos seus dados.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0982f89d59f2ef9a282a46a93b98801b9df00a40
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368717"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Tutorial: Deteção de anomalias em dados de streaming utilizando Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) é um serviço de análise rápido, fácil e colaborativo baseado em Apache Spark. A API do Detetor de Anomalias, parte dos Serviços Cognitivos do Azure, fornece uma forma de monitorizar os dados das suas séries temporizadas. Utilize este tutorial para executar a deteção de anomalias num fluxo de dados em quase tempo real usando Azure Databricks. Você vai ingerir dados do Twitter usando Azure Event Hubs, e importá-los em Azure Databricks usando o conector Spark Event Hubs. Em seguida, você usará a API para detetar anomalias nos dados transmitidos.

A ilustração seguinte mostra o fluxo da aplicação:

![Azure Databricks com Centros de Eventos e Serviços Cognitivos](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks com Centros de Eventos e Serviços Cognitivos")

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma área de trabalho do Azure Databricks
> * Criar um cluster do Spark no Azure Databricks
> * Criar uma aplicação no Twitter para aceder a dados de transmissão
> * Criar blocos de notas no Azure Databricks
> * Anexar bibliotecas aos Hubs de Eventos e à API do Twitter
> * Crie um recurso de detetor de anomalias e recupere a chave de acesso
> * Enviar tweets para os Hubs de Eventos
> * Ler tweets dos Hubs de Eventos
> * Executar deteção de anomalias em tweets

> [!Note]
> * Este tutorial introduz uma abordagem para implementar a arquitetura de [solução](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) recomendada para a API do Detetor de Anomalias.
> * Este tutorial não pode ser preenchido com uma subscrição gratuita `F0` para o Detetor de AnomaliaS API ou Azure Databricks.

Crie uma [subscrição Azure](https://azure.microsoft.com/free/cognitive-services) se não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

- Um centro de nomes e centro de [eventos Azure Event Hubs.](../../../event-hubs/event-hubs-create.md)

- A [cadeia de ligação](../../../event-hubs/event-hubs-get-connection-string.md) para aceder ao espaço de nomes do Event Hubs. A cadeia de ligação deve ter um formato semelhante a:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.

- O nome da política de acesso partilhado e a chave de política para os Centros de Eventos.

Consulte o Azure Event Hubs [para](../../../event-hubs/event-hubs-create.md) obter informações sobre a criação de um espaço de nome e centro de eventos.

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Nesta secção, cria-se um espaço de trabalho Azure Databricks utilizando o [portal Azure](https://portal.azure.com/).

1. No portal Azure, **selecione Criar um recurso**  >  **Analytics**  >  **Azure Databricks**.

    ![Azure Databricks no portal](../media/tutorials/azure-databricks-on-portal.png "Databricks no portal Azure")

3. No serviço **Azure Databricks,** forneça os seguintes valores para criar um espaço de trabalho databricks:


    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks        |
    |**Subscrição**     | Na lista pendente, selecione a sua subscrição do Azure.        |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que detém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../../../azure-resource-manager/management/overview.md). |
    |**Localização**     | Selecione **East US 2** ou uma das outras regiões disponíveis. Consulte [os serviços Azure disponíveis por região](https://azure.microsoft.com/regions/services/) para disponibilidade da região.        |
    |**Escalão de Preço**     |  Escolha entre **Standard** ou **Premium**. NÃO escolha **o Trial**. Para obter mais informações sobre estes escalões, veja [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Selecione **Criar**.

4. A criação da área de trabalho demora alguns minutos.

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

1. No portal do Azure, aceda à área de trabalho do Databricks que criou e, em seguida, selecione **Iniciar Área de Trabalho**.

2. Está redirecionado para o portal Azure Databricks. A partir do portal, selecione **Novo Cluster**.

    ![Databricks em Azure](../media/tutorials/databricks-on-azure.png "Databricks em Azure")

3. Na página **New Cluster,** forneça os valores para criar um cluster.

    ![Criar conjunto de faíscas de dados no Azure](../media/tutorials/create-databricks-spark-cluster.png "Criar conjunto de faíscas de dados no Azure")

    Aceite todos os outros valores predefinidos que não sejam os seguintes:

   * Introduza um nome para o cluster.
   * Para este artigo, crie um cluster com **5.2** tempo de execução. NÃO selecione **5.3** tempo de execução.
   * Certifique-se de que **a caixa de verificação Terminate after minutes \_ \_ of inactiving** é selecionada. Forneça uma duração (em minutos) para terminar o cluster, se o cluster não estiver a ser utilizado.

     Selecione **Criar cluster**.
4. A criação do cluster leva vários minutos. Depois de o cluster estar em execução, pode anexar blocos de notas ao cluster e executar tarefas do Spark.

## <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter

Para receber um fluxo de tweets, tem de criar uma aplicação no Twitter. Siga os passos para criar uma aplicação no Twitter e registe os valores que precisa para concluir este tutorial.

1. A partir de um browser, aceda a [Gestão de Aplicações do Twitter](https://apps.twitter.com/) e selecione **Criar Nova Aplicação**.

    ![Criar aplicação twitter](../media/tutorials/databricks-create-twitter-app.png "Criar aplicação twitter")

2. Na página **Criar uma aplicação** , forneça os detalhes da nova aplicação e, em seguida, selecione **Criar a sua aplicação do Twitter**.

    ![Detalhes da aplicação do Twitter](../media/tutorials/databricks-provide-twitter-app-details.png "Detalhes da aplicação do Twitter")

3. Na página da aplicação, selecione o separador **Chaves e Tokens de Acesso** e copie os valores de **Chave de Consumidor** e **Segredo de Consumidor**. Além disso, selecione **Criar o meu token de acesso** para gerar os tokens de acesso. Copie os valores de **Token de Acesso** e **Segredo de Token de Acesso**.

    ![Detalhes da aplicação do Twitter 2](../media/tutorials/twitter-app-key-secret.png "Detalhes da aplicação do Twitter")

Guarde os valores que obteve da aplicação do Twitter. Vai precisar dos valores mais tarde no tutorial.

## <a name="attach-libraries-to-spark-cluster"></a>Anexar bibliotecas ao cluster do Spark

Neste tutorial, vai utilizar as APIs do Twitter para enviar tweets para os Hubs de Eventos. Também pode utilizar o [Conector de Hubs de eventos do Spark](https://github.com/Azure/azure-event-hubs-spark) para ler e escrever dados em Hubs de Eventos do Azure. Para utilizar estas APIs como parte do cluster, adicione-as como bibliotecas ao Azure Databricks e, em seguida, associe-as ao cluster do Spark. As seguintes instruções mostram como adicionar as bibliotecas à pasta **Partilhada** no seu espaço de trabalho.

1. Na área de trabalho do Azure Databricks, selecione **Área de Trabalho** e, em seguida, clique com botão direito do rato em **Partilhados**. A partir do **Create** menu de contexto, selecione  >  **Criar Biblioteca.**

   ![Adicione caixa de diálogo de biblioteca](../media/tutorials/databricks-add-library-option.png "Adicione caixa de diálogo de biblioteca")

2. Na página Nova Biblioteca, para **Obter** selecione **Maven**. Para **coordenadas,** introduza a coordenada para o pacote que pretende adicionar. Eis as coordenadas do Maven para as bibliotecas utilizadas neste tutorial:

   * Conector de Hubs de Eventos do Spark – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * API do Twitter – `org.twitter4j:twitter4j-core:4.0.7`

     ![Fornecer coordenadas Maven](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Fornecer coordenadas Maven")

3. Selecione **Criar**.

4. Selecione a pasta onde adicionou a biblioteca e, em seguida, selecione o nome da biblioteca.

    ![Selecione biblioteca para adicionar](../media/tutorials/select-library.png "Selecione biblioteca para adicionar")

5. Se não houver cluster na página da biblioteca, selecione **Clusters** e execute o cluster que criou. Espere até que o estado mostre "Running" e depois volte para a página da biblioteca.
Na página da biblioteca, selecione o cluster onde pretende utilizar a biblioteca e, em seguida, **selecione Instalar**. Uma vez que a biblioteca esteja associada com sucesso ao cluster, o estado muda imediatamente para **Instalado**.

    ![Instalar biblioteca para cluster](../media/tutorials/databricks-library-attached.png "Instalar biblioteca para cluster")

6. Repita estes passos para o pacote do Twitter, `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Obter uma chave de acesso aos Serviços Cognitivos

Neste tutorial, você usa as APIs do [Detetor de Anomalias de Serviços Cognitivos Azure](../overview.md) para executar a deteção de anomalias num fluxo de tweets em tempo real. Antes de utilizar as APIs, tem de criar um recurso de detetor de anomalias no Azure e recuperar uma chave de acesso para utilizar as APIs do Detetor de Anomalias.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione **+ Criar um recurso**.

3. No mercado Azure, selecione **AI + Machine Learning**  >  **Ver todos os**  >  **Serviços Cognitivos - Mais** Detetor de  >  **Anomalias**. Ou pode usar [este link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) para ir diretamente à caixa de diálogo **Create.**

    ![Criar recurso de detetor de anomalias](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Criar recurso de detetor de anomalias")

4. Na caixa de diálogo **Criar** , forneça os valores seguintes:

    |Valor |Descrição  |
    |---------|---------|
    |Nome     | Um nome para o recurso do Detetor de Anomalias.        |
    |Subscrição     | A subscrição do Azure a que o recurso será associado.        |
    |Localização     | Uma localização Azure.        |
    |Escalão de preço     | Um nível de preços para o serviço. Para obter mais informações sobre os preços do Detetor de Anomalias, consulte [a página de preços](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Grupo de recursos     | Especifique se pretende criar um novo grupo de recursos ou selecionar um existente.        |


     Selecione **Criar**.

5. Após a criação do recurso, a partir do **separador Visão Geral,** copie e guarde o URL **endpoint,** como mostrado na imagem. Em seguida, **selecione Mostrar as teclas de acesso**.

    ![Mostrar teclas de acesso](../media/tutorials/cognitive-services-get-access-keys.png "Mostrar teclas de acesso")

6. Em **Teclas,** selecione o ícone de cópia contra a chave que pretende utilizar. Guarde a chave de acesso.

    ![Copiar chaves de acesso](../media/tutorials/cognitive-services-copy-access-keys.png "Copiar chaves de acesso")

## <a name="create-notebooks-in-databricks"></a>Criar blocos de notas no Databricks

Nesta secção, vai criar dois blocos de notas na área de trabalho do Databricks, com os seguintes nomes

- **SendTweetsToEventHub** – Um bloco de notas de produtor que utiliza para obter tweets do Twitter e transmiti-los para os Hubs de Eventos.
- **AnalyzeTweetsFromEventHub** - Um caderno de consumo que usa para ler os tweets dos Event Hubs e executar a deteção de anomalias.

1. No espaço de trabalho Azure Databricks, selecione **Workspace** a partir do painel esquerdo. No menu pendente **Área de Trabalho** , selecione **Criar** e, em seguida, selecione **Bloco de Notas**.

    ![Criar caderno em Databricks](../media/tutorials/databricks-create-notebook.png "Criar caderno em Databricks")

2. Na caixa de diálogo **Create Notebook,** **insira SendTweetsToEventHub** como nome, selecione **Scala** como idioma e selecione o cluster Spark que criou anteriormente.

    ![Detalhes do caderno](../media/tutorials/databricks-notebook-details.png "Criar caderno em Databricks")

    Selecione **Criar**.

3. Repita os passos para criar o bloco de notas **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Enviar tweets para os Hubs de Eventos

No caderno **SendTweetsToEventHub,** cole o seguinte código e substitua o espaço reservado por valores para o seu espaço de nomes de Event Hubs e aplicação twitter que criou anteriormente. Este caderno extrai o tempo de criação e o número de "Likes" de tweets com a palavra-chave "Azure" e transmite-os como eventos para os Centros de Eventos em tempo real.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }

  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

Para executar o bloco de notas, prima **SHIFT + ENTER**. Verá um resultado conforme mostrado no seguinte fragmento. Cada evento na saída é uma combinação de timetamp e número de "Like" ingerido nos Centros de Eventos.

```output
    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...
```

## <a name="read-tweets-from-event-hubs"></a>Ler tweets dos Hubs de Eventos

No portátil **DoEventHub da AnalyzeTweets,** cole o seguinte código e substitua o espaço reservado por valores para o seu recurso Detetor de Anomalia que criou anteriormente. Este bloco de notas lê os tweets que transmitiu anteriormente para os Hubs de Eventos através do bloco de notas **SendTweetsToEventHub**.

Primeiro, escreva um cliente para ligar para o detetor de anomalias.
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

Para executar o bloco de notas, prima **SHIFT + ENTER**. Verá um resultado conforme mostrado no seguinte fragmento.

```scala
import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection
defined class Point
defined class Series
defined class AnomalySingleResponse
defined class AnomalyBatchResponse
defined object AnomalyDetector
```

Em seguida, prepare uma função de agregação para utilização futura.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)

  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))

  override def dataType: DataType = BooleanType

  override def deterministic: Boolean = false

  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }

  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }

  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }

  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }


      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)

      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }

    return None
  }
}

```

Para executar o bloco de notas, prima **SHIFT + ENTER**. Verá um resultado conforme mostrado no seguinte fragmento.

```scala
import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap
defined class AnomalyDetectorAggregationFunction
```

Em seguida, carregue os dados do centro de eventos para deteção de anomalias. Substitua o espaço reservado por valores para os seus Azure Event Hubs que criou anteriormente.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

A saída assemelha-se agora à seguinte imagem. Note que a sua data na tabela pode ser diferente da data neste tutorial, uma vez que os dados são em tempo real.
![Dados de carga do centro de eventos](../media/tutorials/load-data-from-eventhub.png "Dados de carga do centro de eventos")

Já transmitiu dados do Azure Event Hubs para Azure Databricks em quase tempo real usando o conector Event Hubs para Apache Spark. Para obter mais informações sobre como utilizar o conector Hubs de Eventos para Spark, veja a [documentação do conector](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Executar deteção de anomalias em tweets

Nesta secção, você executou a deteção de anomalias nos tweets recebidos usando o detetor de anomaliaS API. Nesta secção, vai adicionar os fragmentos de código ao mesmo bloco de notas **AnalyzeTweetsFromEventHub**.

Para fazer a deteção de anomalias, primeiro, é preciso agregar a sua contagem métrica por hora.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window.
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
A saída assemelha-se agora aos seguintes cortes.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Em seguida, obter o resultado de saída agregado para a Delta. Como a deteção de anomalias requer uma janela de história mais longa, estamos a usar a Delta para manter os dados de história para o ponto que queres detetar.
Substitua o "[Espaço reservado: nome de mesa]" por um nome de mesa Delta qualificado a criar (por exemplo, "tweets"). Substitua "[Espaço reservado: nome de pasta para pontos de verificação]" por um valor de corda único cada vez que executar este código (por exemplo, "etl-from-eventhub-20190605").
Para saber mais sobre o Lago Delta em Azure Databricks, consulte o [Delta Lake Guide](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

Substitua o "[Espaço reservado: nome de mesa]" pelo mesmo nome de mesa Delta que selecionou acima.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
A saída abaixo:
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

Agora, os dados agregados da série de tempo são continuamente ingeridos no Delta. Então pode agendar um trabalho de hora a hora para detetar a anomalia do último ponto.
Substitua o "[Espaço reservado: nome de mesa]" pelo mesmo nome de mesa Delta que selecionou acima.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Resultado como abaixo:

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

Já está! Utilizando a Azure Databricks, transmitiu dados com sucesso para a Azure Event Hubs, consumiu os dados de fluxo usando o conector Event Hubs e, em seguida, executou a deteção de anomalias em dados de streaming em tempo real.
Embora neste tutorial, a granularidade seja de hora a hora, pode sempre mudar a granularidade para satisfazer as suas necessidades.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de executar o tutorial, pode terminar o cluster. Para tal, no espaço de trabalho Azure Databricks, selecione **Clusters** a partir do painel esquerdo. Para o cluster que pretende terminar, mova o cursor sobre a elipse sob a coluna **Actions** e selecione o ícone **Terminate** e, em seguida, selecione **Confirmar**.

![Pare um cluster Databricks](../media/tutorials/terminate-databricks-cluster.png "Pare um cluster Databricks")

Se não encerrar manualmente o cluster, este para automaticamente, desde que tenha selecionado a **caixa de verificação Terminate after \_ \_ minutes of inactivity** enquanto cria o cluster. Nesse caso, o cluster para automaticamente se tiver estado inativo durante o período de tempo especificado.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar o Azure Databricks para transmitir dados em fluxo aos Hubs de Eventos do Azure e, em seguida, ler os dados de transmissão em fluxo dos Hubs de Eventos em tempo real. Avance para o próximo tutorial para aprender a chamar a API do Detetor de Anomalias e visualizar anomalias usando o ambiente de trabalho Power BI.

> [!div class="nextstepaction"]
>[Deteção de anomalias de lote com desktop Power BI](batch-anomaly-detection-powerbi.md)