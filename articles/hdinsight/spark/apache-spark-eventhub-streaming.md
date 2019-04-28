---
title: 'Tutorial: Processar dados de Hubs de eventos do Azure com o Apache Spark no Azure HDInsight '
description: Ligue o Apache Spark no HDInsight do Azure para os Hubs de eventos do Azure e processar os dados de transmissão em fluxo.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: conceptual
ms.date: 12/28/2018
ms.openlocfilehash: 02f7bbca127ba33fcfdd15d6f00d1660bf72970c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124361"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Tutorial: Processar tweets com os Hubs de eventos do Azure e do Apache Spark no HDInsight

Neste tutorial, irá aprender a criar uma [Apache Spark](https://spark.apache.org/) aplicação para enviar tweets para um hub de eventos do Azure e criar outra aplicação para ler os tweets do hub de eventos de transmissão em fluxo. Para obter uma explicação detalhada do Spark streaming, consulte [descrição geral de transmissão em fluxo do Apache Spark](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight oferece as mesmas funcionalidades de transmissão em fluxo para um cluster do Spark no Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Enviar mensagens para o Hub de eventos do Azure
> * Ler as mensagens do Hub de eventos do Azure

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Concluir o artigo [Tutorial: Carregar dados e executar consultas num cluster do Apache Spark no Azure HDInsight](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter

Para receber um fluxo de tweets, crie uma aplicação no Twitter. Siga as instruções criar um aplicativo do Twitter e anote os valores que precisa para concluir este tutorial.

1. Navegue até [gestão de aplicações do Twitter](https://apps.twitter.com/).

1. Selecione **criar nova aplicação**.

1. Forneça os seguintes valores:

    - Nome: forneça o nome da aplicação. O valor utilizado para este tutorial é **HDISparkStreamApp0423**. Este nome tem de ser um nome exclusivo.
    - Descrição: forneça uma breve descrição da aplicação. O valor utilizado para este tutorial é **uma transmissão em fluxo aplicativo simples de Spark do HDInsight**.
    - Web site: forneça o site da aplicação. Não tem de ser um site válido.  O valor utilizado para este tutorial é **http://www.contoso.com**.
    - URL de chamada de retorno: pode deixá-lo em branco.

1. Selecione **Sim, eu li e aceito o contrato de desenvolvedor do Twitter**e, em seguida, selecione **criar a sua aplicação do Twitter**.

1. Selecione o **chaves e Tokens de acesso** separador.

1. Selecione **criar meu token de acesso** no final da página.

1. Anote os seguintes valores da página.  Vai precisar destes valores mais tarde no tutorial:

    - **Chave de consumidor (chave de API)**    
    - **Segredo de consumidor (segredo de API)**  
    - **Token de acesso**
    - **Segredo de Token de acesso**   

## <a name="create-an-azure-event-hubs-namespace"></a>Criar um espaço de nomes de Hubs de eventos do Azure

Utilize este hub de eventos para armazenar os tweets.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. No menu à esquerda, selecione **todos os serviços**.  

1. Sob **INTERNET das COISAS**, selecione **dos Hubs de eventos**. 

    ![Criar hub de eventos para o exemplo de transmissão em fluxo do Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "criar hub de eventos para o exemplo de transmissão em fluxo do Spark")

4. Selecione **+ Adicionar**.
5. Introduza os seguintes valores para o novo espaço de nomes de Hubs de eventos:

    - **Nome**: Introduza um nome para o hub de eventos.  O valor utilizado para este tutorial é **myeventhubns20180403**.

    - **Escalão de preço**: Selecione **padrão**.

    - **Subscrição**: Selecione a subscrição adequada.

    - **Grupo de recursos**: Selecione um grupo de recursos existente na lista pendente ou selecione **criar novo** para criar um novo grupo de recursos.

    - **Localização**: Selecione o mesmo **localização** como o seu cluster do Apache Spark no HDInsight para reduzir os custos e latência.

    - **Ativar ampliação automática**: (Opcional)  Ampliação automática dimensiona-se automaticamente o número de unidades de débito atribuído ao seu espaço de nomes de Hubs de eventos quando o tráfego excede a capacidade das unidades de débito atribuídos à mesma.  

    - **A ampliação automática unidades de débito máximas**: (Opcional)  Este controlo de deslize só será apresentado se verificar **ativar ampliação automática**.  

      ![Forneça um nome de hub de eventos para o exemplo de transmissão em fluxo do Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "fornecer um nome de hub de eventos para o exemplo de transmissão em fluxo do Spark")
6. Selecione **criar** para criar o espaço de nomes.  A implementação será concluído dentro de alguns minutos.

## <a name="create-an-azure-event-hub"></a>Criar um hub de eventos do Azure
Crie um hub de eventos depois de implementar o espaço de nomes de Hubs de eventos.  No portal:

1. No menu à esquerda, selecione **todos os serviços**.  

1. Sob **INTERNET das COISAS**, selecione **dos Hubs de eventos**.  

1. Selecione o seu espaço de nomes de Hubs de eventos da lista.  

1. Partir do **espaço de nomes de Hubs de eventos** página, selecione **+ Hub de eventos**.  
1. Introduza os seguintes valores no **criar Hub de eventos** página:

    - **Nome**: Dê um nome para o seu Hub de eventos. 
 
    - **Contagem de partição**: 10.  

    - **Retenção de mensagens**: 1.   
   
      ![Forneça os detalhes do hub de eventos para o exemplo de transmissão em fluxo do Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "fornecer os detalhes do hub de eventos para o exemplo de transmissão em fluxo do Spark")

1. Selecione **Criar**.  A implementação deve ser concluído em alguns segundos e será encaminhado de volta para a página de espaço de nomes de Hubs de eventos.

1. Sob **configurações**, selecione **políticas de acesso partilhado**.

1. Selecione **RootManageSharedAccessKey**.
    
     ![Definir políticas do Hub de eventos para o exemplo de transmissão em fluxo de Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "políticas de definir o Hub de eventos para o exemplo de transmissão em fluxo de Spark")

1. Guarde os valores da **chave primária** e **ligação chave primária da cadeia de caracteres** para utilizar mais tarde no tutorial.

     ![Ver chaves de política do Hub de eventos para o exemplo de transmissão em fluxo de Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "chaves de política do Hub de eventos do modo de exibição para o exemplo de transmissão em fluxo do Spark")


## <a name="send-tweets-to-the-event-hub"></a>Enviar tweets para o hub de eventos

Criar um bloco de notas do Jupyter e designe- **SendTweetsToEventHub**. 

1. Execute o seguinte código para adicionar as bibliotecas externas do Apache Maven:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Execute o seguinte código para enviar tweets para o hub de eventos:

    ```
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

3. Abra o hub de eventos no portal do Azure.  No **descrição geral**, deverá ver alguns gráficos que mostra as mensagens enviadas para o hub de eventos.

## <a name="read-tweets-from-the-event-hub"></a>Ler tweets do hub de eventos

Criar outro bloco de notas do Jupyter e designe- **ReadTweetsFromEventHub**. 

1. Execute o seguinte código para adicionar uma biblioteca externa do Apache Maven:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Execute o seguinte código para ler tweets a partir do seu hub de eventos:

    ```
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

Com o HDInsight, os dados são armazenados no armazenamento do Azure ou o armazenamento do Azure Data Lake, pelo que pode eliminar em segurança um cluster quando não está em utilização. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Se planeja a trabalhar imediatamente no próximo tutorial, pode querer manter o cluster, caso contrário, vamos continuar e eliminar o cluster.

Abra o cluster no portal do Azure e, em seguida, selecione **Eliminar**.

![Eliminar o cluster do HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Eliminar o cluster do HDInsight")

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, está a eliminar o cluster do Spark no HDInsight e a conta de armazenamento predefinida.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

* Leia a mensagem de um hub de eventos.
Avance para o artigo seguinte para ver o que pode criar uma aplicação de machine learning. 

> [!div class="nextstepaction"]
> [Criar uma aplicação de machine learning](./apache-spark-ipython-notebook-machine-learning.md)


