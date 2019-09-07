---
title: 'Tutorial: Processar dados de hubs de eventos do Azure com Apache Spark no HDInsight'
description: Tutorial-conectar Apache Spark no Azure HDInsight aos hubs de eventos do Azure e processar os dados de streaming.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/24/2019
ms.openlocfilehash: 2483ba22d3d502479e87ae385bcc837ec87a103c
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735340"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Tutorial: Processar tweets usando hubs de eventos do Azure e Apache Spark no HDInsight

Neste tutorial, você aprenderá a criar um aplicativo de streaming [Apache Spark](https://spark.apache.org/) para enviar Tweets para um hub de eventos do Azure e criar outro aplicativo para ler os tweets do hub de eventos. Para obter uma explicação detalhada do streaming do Spark, confira [Apache Spark visão geral do streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). O HDInsight traz os mesmos recursos de streaming para um cluster Spark no Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Enviar mensagens para o Hub de eventos do Azure
> * Ler mensagens do hub de eventos do Azure

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Consulte [criar um cluster apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Familiaridade com a utilização de Jupyter Notebooks com o Spark no HDInsight. Para obter mais informações, consulte [carregar dados e executar consultas com Apache Spark no HDInsight](./apache-spark-load-data-run-query.md).

* Uma [conta do Twitter](https://twitter.com/i/flow/signup).

## <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter

Para receber um fluxo de tweets, crie uma aplicação no Twitter. Siga as instruções para criar um aplicativo do Twitter e anote os valores necessários para concluir este tutorial.

1. Navegue até [Gerenciamento de aplicativos do Twitter](https://apps.twitter.com/).

1. Selecione **criar novo aplicativo**.

1. Forneça os seguintes valores:

    |Propriedade |Value |
    |---|---|
    |Name|Forneça o nome do aplicativo. O valor usado para este tutorial é **HDISparkStreamApp0423**. Esse nome deve ser um nome exclusivo.|
    |Descrição|Forneça uma breve descrição do aplicativo. O valor usado para este tutorial é **um aplicativo simples de streaming do HDInsight Spark**.|
    |Web site|Forneça o site do aplicativo. Ele não precisa ser um site válido.  O valor usado para este tutorial é `http://www.contoso.com`.|
    |Url de chamada de retorno|Você pode deixá-lo em branco.|

1. Selecione **Sim, li e concordo com o contrato de desenvolvedor do Twitter**e, em seguida, selecione **criar seu aplicativo do Twitter**.

1. Selecione a guia **chaves e tokens de acesso** .

1. Selecione **criar meu token de acesso** no final da página.

1. Anote os valores a seguir na página.  Você precisará desses valores posteriormente no tutorial:

    - **Chave do consumidor (chave de API)**    
    - **Segredo do consumidor (segredo da API)**  
    - **Token de acesso**
    - **Segredo do token de acesso**   

## <a name="create-an-azure-event-hubs-namespace"></a>Criar um namespace de hubs de eventos do Azure

Você usa esse Hub de eventos para armazenar tweets.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. No menu à esquerda, selecione **todos os serviços**.  

3. Em **Internet das coisas**, selecione **hubs de eventos**. 

    ![Criar Hub de eventos para exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Criar Hub de eventos para exemplo de streaming do Spark")

4. Selecione **+ Adicionar**.

5. Insira os seguintes valores para o novo namespace de hubs de eventos:

    |Propriedade |Value |
    |---|---|
    |Name|Insira um nome para o Hub de eventos.  O valor usado para este tutorial é **myeventhubns20180403**.|
    |Escalão de preço|Selecione **padrão**.|
    |Subscription|Selecione sua assinatura apropriada.|
    |Resource group|Selecione um grupo de recursos existente na lista suspensa ou selecione **criar novo** para criar um novo grupo de recursos.|
    |Location|Selecione o mesmo **local** que o cluster apache Spark no HDInsight para reduzir a latência e os custos.|
    |Habilitar o inflar automaticamente (opcional) |O inflar automaticamente dimensiona o número de unidades de produtividade atribuídas ao seu namespace de hubs de eventos quando o tráfego excede a capacidade das unidades de produtividade atribuídas a ela.  |
    |Unidades de taxa de transferência máximas de inflação automática (opcional)|Esse controle deslizante será exibido somente se você marcar **habilitar inflar automaticamente**.  |

    ![Fornecer um nome do hub de eventos para o exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Fornecer um nome do hub de eventos para o exemplo de streaming do Spark")

6. Selecione **criar** para criar o namespace.  A implantação será concluída em alguns minutos.

## <a name="create-an-azure-event-hub"></a>Criar um hub de eventos do Azure
Crie um hub de eventos depois que o namespace dos hubs de eventos tiver sido implantado.  No Portal:

1. No menu à esquerda, selecione **todos os serviços**.  

1. Em **Internet das coisas**, selecione **hubs de eventos**.  

1. Selecione o namespace de hubs de eventos na lista.  

1. Na página **namespace de hubs de eventos** , selecione **+ Hub de eventos**.  
1. Insira os seguintes valores na página **criar Hub de eventos** :

    - **Nome**: Dê um nome para o Hub de eventos. 
 
    - **Contagem de partições**: 10.  

    - **Retenção de mensagem**: 1.   
   
      ![Fornecer detalhes do hub de eventos para o exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Fornecer detalhes do hub de eventos para o exemplo de streaming do Spark")

1. Selecione **Criar**.  A implantação deve ser concluída em alguns segundos e você será retornado para a página de namespace de hubs de eventos.

1. Em **configurações**, selecione **políticas de acesso compartilhado**.

1. Selecione **RootManageSharedAccessKey**.
    
     ![Definir políticas do hub de eventos para o exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Definir políticas do hub de eventos para o exemplo de streaming do Spark")

1. Salve os valores da **chave primária** e da **chave primária da cadeia de conexão** para usar posteriormente no tutorial.

     ![Exibir chaves de política do hub de eventos para o exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Exibir chaves de política do hub de eventos para o exemplo de streaming do Spark")


## <a name="send-tweets-to-the-event-hub"></a>Enviar Tweets para o Hub de eventos

Crie um notebook Jupyter e nomeie-o **SendTweetsToEventHub**. 

1. Execute o seguinte código para adicionar as bibliotecas externas do Apache Maven:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Edite o código a seguir `<Event hub name>`substituindo `<CONSUMER KEY>`, `<CONSUMER SECRET>` `<Event hub namespace connection string>`, `<ACCESS TOKEN>`,, `<TOKEN SECRET>` e pelos valores apropriados. Execute o código editado para enviar Tweets para o Hub de eventos:

    ```scala
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Abra o Hub de eventos na portal do Azure.  Em **visão geral**, você deverá ver alguns gráficos mostrando as mensagens enviadas para o Hub de eventos.

## <a name="read-tweets-from-the-event-hub"></a>Ler tweets do hub de eventos

Crie outro Notebook Jupyter e nomeie-o **ReadTweetsFromEventHub**. 

1. Execute o seguinte código para adicionar uma biblioteca externa do Apache Maven:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13"}}
    ```

2. Edite o código abaixo substituindo `<Event hub name>`e `<Event hub namespace connection string>` pelos valores apropriados. Execute o código editado para ler tweets do seu hub de eventos:

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Com o HDInsight, seus dados são armazenados no armazenamento do Azure ou Azure Data Lake Storage, para que você possa excluir um cluster com segurança quando ele não estiver em uso. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Se você planeja trabalhar no próximo tutorial imediatamente, talvez você queira manter o cluster, caso contrário, vá em frente e exclua o cluster.

Abra o cluster no portal do Azure e, em seguida, selecione **Eliminar**.

![Eliminar o cluster do HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Eliminar o cluster do HDInsight")

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, está a eliminar o cluster do Spark no HDInsight e a conta de armazenamento predefinida.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu a criar um aplicativo de streaming de Apache Spark para enviar Tweets para um hub de eventos do Azure e criou outro aplicativo para ler os tweets do hub de eventos.  Avance para o próximo artigo para ver você pode criar um aplicativo de aprendizado de máquina.

> [!div class="nextstepaction"]
> [Criar um aplicativo de Machine Learning](./apache-spark-ipython-notebook-machine-learning.md)
