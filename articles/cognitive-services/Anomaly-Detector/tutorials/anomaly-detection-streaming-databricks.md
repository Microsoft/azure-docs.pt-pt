---
title: 'Tutorial: detecção de anomalias em dados de streaming usando Azure Databricks'
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API do detector de anomalias e Azure Databricks para monitorar anomalias em seus dados.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 93ee5df4327aa396573665cd0c2cbd8222015cce
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448904"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Tutorial: detecção de anomalias em dados de streaming usando Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) é um serviço de análise baseado em Apache Spark rápido, fácil e colaborativo. A API do detector de anomalias, parte dos serviços cognitivas do Azure, fornece uma maneira de monitorar seus dados de série temporal. Use este tutorial para executar a detecção de anomalias em um fluxo de dados quase em tempo real usando Azure Databricks. Você ingerirá dados do Twitter usando os hubs de eventos do Azure e os importará para Azure Databricks usando o conector de hubs de eventos do Spark. Posteriormente, você usará a API para detectar anomalias nos dados transmitidos. 

A ilustração seguinte mostra o fluxo da aplicação:

![Azure Databricks com hubs de eventos e serviços cognitivas](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks com hubs de eventos e serviços cognitivas")

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma área de trabalho do Azure Databricks
> * Criar um cluster do Spark no Azure Databricks
> * Criar uma aplicação no Twitter para aceder a dados de transmissão
> * Criar blocos de notas no Azure Databricks
> * Anexar bibliotecas aos Hubs de Eventos e à API do Twitter
> * Criar um recurso de detector de anomalias e recuperar a chave de acesso
> * Enviar tweets para os Hubs de Eventos
> * Ler tweets dos Hubs de Eventos
> * Executar detecção de anomalias em tweets

> [!Note]
> Este tutorial apresenta uma abordagem para implementar a [arquitetura de solução](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) recomendada para a API do detector de anomalias.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!Note]
> Este tutorial não pode ser concluído com uma chave de avaliação gratuita para a API do detector de anomalias. Para utilizar uma conta gratuita para criar o cluster do Azure Databricks, antes de criar o cluster, aceda ao seu perfil e altere a subscrição para **pay as you go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Um [namespace dos hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) e o Hub de eventos.

- A [cadeia de conexão](../../../event-hubs/event-hubs-get-connection-string.md) para acessar o namespace de hubs de eventos. A cadeia de conexão deve ter um formato semelhante para:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- O nome da política de acesso compartilhado e a chave de política para os hubs de eventos.

Consulte o guia de [início rápido](../../../event-hubs/event-hubs-create.md) dos hubs de eventos do Azure para obter informações sobre como criar um namespace e um hub de eventos.

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Nesta seção, você cria um espaço de trabalho Azure Databricks usando o [portal do Azure](https://portal.azure.com/).

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](../media/tutorials/azure-databricks-on-portal.png "Databricks no portal do Azure")

3. Em **Azure Databricks serviço**, forneça os seguintes valores para criar um espaço de trabalho do databricks:


    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks        |
    |**Subscrição**     | Na lista pendente, selecione a sua subscrição do Azure.        |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../../../azure-resource-manager/management/overview.md). |
    |**Localização**     | Selecione **leste dos EUA 2** ou uma das outras regiões disponíveis. Consulte [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/) para disponibilidade de região.        |
    |**Escalão de Preço**     |  Escolha entre **Standard** ou **Premium**. Não escolha **avaliação**. Para obter mais informações sobre estes escalões, veja [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Selecione **Criar**.

4. A criação da área de trabalho demora alguns minutos. 

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

1. No portal do Azure, aceda à área de trabalho do Databricks que criou e, em seguida, selecione **Iniciar Área de Trabalho**.

2. Você será redirecionado para o portal de Azure Databricks. No portal, selecione **novo cluster**.

    ![Databricks no Azure](../media/tutorials/databricks-on-azure.png "Databricks no Azure")

3. Na página **novo cluster** , forneça os valores para criar um cluster.

    ![Criar um cluster Spark do databricks no Azure](../media/tutorials/create-databricks-spark-cluster.png "Criar um cluster Spark do databricks no Azure")

    Aceite todos os outros valores predefinidos que não sejam os seguintes:

   * Introduza um nome para o cluster.
   * Para este artigo, crie um cluster com o tempo de execução **5,2** . Não selecione o tempo de execução **5,3** .
   * Verifique se a caixa de seleção **terminar depois de \_\_ minutos de inatividade** está selecionada. Forneça uma duração (em minutos) para encerrar o cluster, se o cluster não estiver sendo usado.

     Selecione **Criar cluster**. 
4. A criação do cluster leva vários minutos. Depois de o cluster estar em execução, pode anexar blocos de notas ao cluster e executar tarefas do Spark.

## <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter

Para receber um fluxo de tweets, tem de criar uma aplicação no Twitter. Siga os passos para criar uma aplicação no Twitter e registe os valores que precisa para concluir este tutorial.

1. A partir de um browser, aceda a [Gestão de Aplicações do Twitter](https://apps.twitter.com/) e selecione **Criar Nova Aplicação**.

    ![Criar aplicativo do Twitter](../media/tutorials/databricks-create-twitter-app.png "Criar aplicativo do Twitter")

2. Na página **Criar uma aplicação**, forneça os detalhes da nova aplicação e, em seguida, selecione **Criar a sua aplicação do Twitter**.

    ![Detalhes do aplicativo do Twitter](../media/tutorials/databricks-provide-twitter-app-details.png "Detalhes do aplicativo do Twitter")

3. Na página da aplicação, selecione o separador **Chaves e Tokens de Acesso** e copie os valores de **Chave de Consumidor** e **Segredo de Consumidor**. Além disso, selecione **Criar o meu token de acesso** para gerar os tokens de acesso. Copie os valores de **Token de Acesso** e **Segredo de Token de Acesso**.

    ![Detalhes do aplicativo do Twitter](../media/tutorials/twitter-app-key-secret.png "Detalhes do aplicativo do Twitter")

Guarde os valores que obteve da aplicação do Twitter. Vai precisar dos valores mais tarde no tutorial.

## <a name="attach-libraries-to-spark-cluster"></a>Anexar bibliotecas ao cluster do Spark

Neste tutorial, vai utilizar as APIs do Twitter para enviar tweets para os Hubs de Eventos. Também pode utilizar o [Conector de Hubs de eventos do Spark](https://github.com/Azure/azure-event-hubs-spark) para ler e escrever dados em Hubs de Eventos do Azure. Para utilizar estas APIs como parte do cluster, adicione-as como bibliotecas ao Azure Databricks e, em seguida, associe-as ao cluster do Spark. As instruções a seguir mostram como adicionar as bibliotecas à pasta **compartilhada** em seu espaço de trabalho.

1. Na área de trabalho do Azure Databricks, selecione **Área de Trabalho** e, em seguida, clique com botão direito do rato em **Partilhados**. No menu de contexto, selecione **Criar** > **Biblioteca**.

   ![Caixa de diálogo Adicionar biblioteca](../media/tutorials/databricks-add-library-option.png "Caixa de diálogo Adicionar biblioteca")

2. Na página nova biblioteca, para **origem** , selecione **Maven**. Para **coordenadas**, insira a coordenada para o pacote que você deseja adicionar. Eis as coordenadas do Maven para as bibliotecas utilizadas neste tutorial:

   * Conector de Hubs de Eventos do Spark – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * API do Twitter – `org.twitter4j:twitter4j-core:4.0.7`

     ![Fornecer coordenadas do Maven](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Fornecer coordenadas do Maven")

3. Selecione **Criar**.

4. Selecione a pasta onde adicionou a biblioteca e, em seguida, selecione o nome da biblioteca.

    ![Selecionar biblioteca para adicionar](../media/tutorials/select-library.png "Selecionar biblioteca para adicionar")

5. Se não houver nenhum cluster na página biblioteca, selecione **clusters** e execute o cluster que você criou. Aguarde até que o estado mostre "Running" e, em seguida, volte para a página da biblioteca.
Na página biblioteca, selecione o cluster no qual você deseja usar a biblioteca e, em seguida, selecione **instalar**. Depois que a biblioteca for associada com êxito ao cluster, o status será alterado imediatamente para **instalado**.

    ![Instalar biblioteca no cluster](../media/tutorials/databricks-library-attached.png "Instalar biblioteca no cluster")

6. Repita estes passos para o pacote do Twitter, `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Obter uma chave de acesso aos Serviços Cognitivos

Neste tutorial, você usa as [APIs do detector de anomalias dos serviços cognitivas do Azure](../overview.md) para executar a detecção de anomalias em um fluxo de tweets quase em tempo real. Antes de usar as APIs, você deve criar um recurso de detector de anomalias no Azure e recuperar uma chave de acesso para usar as APIs do detector de anomalias.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione **+ Criar um recurso**.

3. Em Azure Marketplace, selecione **ia + Machine Learning** > **ver todos os** > **Serviços cognitivas – Detector de** **anomalias**mais > . Ou você pode usar [esse link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) para ir diretamente para a caixa de diálogo **criar** .

    ![Criar recurso de detector de anomalias](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Criar recurso de detector de anomalias")

4. Na caixa de diálogo **Criar**, forneça os valores seguintes:

    |Valor |Descrição  |
    |---------|---------|
    |Nome     | Um nome para o recurso de detector de anomalias.        |
    |Subscrição     | A assinatura do Azure ao qual o recurso será associado.        |
    |Localização     | Um local do Azure.        |
    |Escalão de preço     | Um tipo de preço para o serviço. Para obter mais informações sobre os preços do detector de anomalias, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Grupo de recursos     | Especifique se pretende criar um novo grupo de recursos ou selecione um existente.        |


     Selecione **Criar**.

5. Depois que o recurso for criado, na guia **visão geral** , copie e salve a URL do **ponto de extremidade** , conforme mostrado na captura de tela. Em seguida, selecione **Mostrar chaves de acesso**.

    ![Mostrar chaves de acesso](../media/tutorials/cognitive-services-get-access-keys.png "Mostrar chaves de acesso")

6. Em **chaves**, selecione o ícone de cópia em relação à chave que você deseja usar. Salve a chave de acesso.

    ![Copiar chaves de acesso](../media/tutorials/cognitive-services-copy-access-keys.png "Copiar chaves de acesso")

## <a name="create-notebooks-in-databricks"></a>Criar blocos de notas no Databricks

Nesta secção, vai criar dois blocos de notas na área de trabalho do Databricks, com os seguintes nomes

- **SendTweetsToEventHub** – Um bloco de notas de produtor que utiliza para obter tweets do Twitter e transmiti-los para os Hubs de Eventos.
- **AnalyzeTweetsFromEventHub** -um notebook de consumidor que você usa para ler os tweets dos hubs de eventos e executar a detecção de anomalias.

1. No espaço de trabalho Azure Databricks, selecione **espaço de trabalho** no painel esquerdo. No menu pendente **Área de Trabalho**, selecione **Criar** e, em seguida, selecione **Bloco de Notas**.

    ![Criar bloco de anotações no databricks](../media/tutorials/databricks-create-notebook.png "Criar bloco de anotações no databricks")

2. Na caixa de diálogo **criar bloco de anotações** , digite **SendTweetsToEventHub** como nome, selecione **escalabilidade** como o idioma e selecione o cluster Spark que você criou anteriormente.

    ![Criar bloco de anotações no databricks](../media/tutorials/databricks-notebook-details.png "Criar bloco de anotações no databricks")

    Selecione **Criar**.

3. Repita os passos para criar o bloco de notas **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Enviar tweets para os Hubs de Eventos

Na **SendTweetsToEventHub** bloco de notas, cole o seguinte código e substitua o marcador de posição pelos valores de espaço de nomes de Hubs de eventos e a aplicação do Twitter que criou anteriormente. Este notebook extrai o tempo de criação e o número de "como" dos tweets com a palavra-chave "Azure" e transmite esses como eventos nos hubs de eventos em tempo real.

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

Para executar o bloco de notas, prima **SHIFT + ENTER**. Verá um resultado conforme mostrado no seguinte fragmento. Cada evento na saída é uma combinação de carimbo de data/hora e número de "como" s ingeridos nos hubs de eventos.

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

## <a name="read-tweets-from-event-hubs"></a>Ler tweets dos Hubs de Eventos

No notebook **AnalyzeTweetsFromEventHub** , Cole o código a seguir e substitua o espaço reservado por valores para o recurso do detector de anomalias que você criou anteriormente. Este bloco de notas lê os tweets que transmitiu anteriormente para os Hubs de Eventos através do bloco de notas **SendTweetsToEventHub**.

Primeiro, escreva um cliente para chamar o detector de anomalias. 
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

Em seguida, prepare uma função de agregação para uso futuro.
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

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

Em seguida, carregue os dados do hub de eventos para detecção de anomalias. Substitua o espaço reservado por valores para os hubs de eventos do Azure que você criou anteriormente.

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

A saída agora é semelhante à imagem a seguir. Observe que sua data na tabela pode ser diferente da data neste tutorial, pois os dados são em tempo real.
![Carregar dados do hub de eventos](../media/tutorials/load-data-from-eventhub.png "Carregar dados do hub de eventos")

Agora transmitiu os dados dos Hubs de eventos do Azure para o Azure Databricks em tempo real com o conector de Hubs de eventos para o Apache Spark. Para obter mais informações sobre como utilizar o conector Hubs de Eventos para Spark, veja a [documentação do conector](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Executar detecção de anomalias em tweets

Nesta seção, você executa a detecção de anomalias nos tweets recebidos usando a API do detector de anomalias. Nesta secção, vai adicionar os fragmentos de código ao mesmo bloco de notas **AnalyzeTweetsFromEventHub**.

Para fazer a detecção de anomalias, primeiro, você precisa agregar sua contagem de métricas por hora.
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
A saída agora é semelhante aos trechos de código a seguir.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Em seguida, obtenha o resultado da saída agregada para Delta. Como a detecção de anomalias requer uma janela de histórico mais longa, estamos usando o Delta para manter os dados do histórico para o ponto que você deseja detectar. Substitua o "[PlaceHolder: nome da tabela]" por um nome de tabela Delta qualificado a ser criado (por exemplo, "tweets"). Substitua "[PlaceHolder: nome da pasta para pontos de verificação]" por um valor de cadeia de caracteres que seja exclusivo cada vez que você executar esse código (por exemplo, "ETL-from-eventhub-20190605").
Para saber mais sobre o Delta Lake em Azure Databricks, consulte o [Guia do Delta Lake](https://docs.azuredatabricks.net/delta/index.html)


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

Substitua o "[PlaceHolder: nome da tabela]" pelo mesmo nome de tabela Delta que você selecionou acima.
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
A saída é a seguinte: 
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

Agora os dados agregados da série temporal são ingeridos continuamente no Delta. Em seguida, você pode agendar um trabalho por hora para detectar a anomalia do último ponto. Substitua o "[PlaceHolder: nome da tabela]" pelo mesmo nome de tabela Delta que você selecionou acima.

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

Já está! Usando Azure Databricks, você transmitiu com êxito os dados para os hubs de eventos do Azure, consumiu os dados de fluxo usando o conector de hubs de eventos e, em seguida, executou a detecção de anomalias no streaming de dados quase em tempo real.
Embora, neste tutorial, a granularidade seja por hora, você sempre pode alterar a granularidade para atender às suas necessidades. 

## <a name="clean-up-resources"></a>Limpar recursos

Depois de executar o tutorial, pode terminar o cluster. Para fazer isso, no espaço de trabalho Azure Databricks, selecione **clusters** no painel esquerdo. Para o cluster que você deseja encerrar, mova o cursor sobre as reticências na coluna **ações** e selecione o ícone **terminar** e, em seguida, selecione **confirmar**.

![Parar um cluster do databricks](../media/tutorials/terminate-databricks-cluster.png "Parar um cluster do databricks")

Se você não encerrar manualmente o cluster, ele será interrompido automaticamente, desde que você tenha selecionado a caixa de seleção **terminar depois de \_\_ minutos de inatividade** ao criar o cluster. Nesse caso, o cluster para automaticamente se tiver estado inativo durante o período de tempo especificado.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar o Azure Databricks para transmitir dados em fluxo aos Hubs de Eventos do Azure e, em seguida, ler os dados de transmissão em fluxo dos Hubs de Eventos em tempo real. Avance para o próximo tutorial para saber como chamar a API do detector de anomalias e Visualizar anomalias usando Power BI área de trabalho. 

> [!div class="nextstepaction"]
>[Detecção de anomalias em lote com Power BI desktop](batch-anomaly-detection-powerbi.md)
