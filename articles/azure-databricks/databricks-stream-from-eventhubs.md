---
title: 'Tutorial: Transmitir em fluxo dados para o Azure Databricks com os Hubs de Eventos '
description: Saiba como utilizar o Azure Databricks com os Hubs de Eventos para ingerir dados de transmissão a partir do Twitter e ler os dados em quase tempo real.
services: azure-databricks
author: lenadroid
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 06/21/2018
ms.author: alehall
ms.openlocfilehash: bc712885169730aa9cbbd8de35b96e645ff1cea2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087130"
---
# <a name="tutorial-stream-data-into-azure-databricks-using-event-hubs"></a>Tutorial: Transmitir em fluxo dados para o Azure Databricks com os Hubs de Eventos

Neste tutorial, vai ligar um sistema de ingestão de dados com o Azure Databricks para transmitir dados para um cluster do Apache Spark em quase tempo real. Vai configurar o sistema de ingestão de dados com os Hubs de Eventos do Azure e, em seguida, ligá-lo ao Azure Databricks para processar as mensagens recebidas. Para aceder a uma transmissão de dados, utilize as APIs do Twitter para ingerir os tweets nos Hubs de Eventos. Depois de ter os dados no Azure Databricks, pode executar tarefas analíticas para analisar os dados mais detalhadamente. 

No final deste tutorial, deverá ter transmitido tweets do Twitter em fluxo, (que incluem o termo "Azure" nos mesmos) e ler os tweets no Azure Databricks.

A ilustração seguinte mostra o fluxo da aplicação:

![Azure Databricks com Hubs de Eventos](./media/databricks-stream-from-eventhubs/databricks-eventhubs-tutorial.png "Azure Databricks com Hubs de Eventos")

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma área de trabalho do Azure Databricks
> * Criar um cluster do Spark no Azure Databricks
> * Criar uma aplicação no Twitter para aceder a dados de transmissão
> * Criar blocos de notas no Azure Databricks
> * Anexar bibliotecas aos Hubs de Eventos e à API do Twitter
> * Enviar tweets para os Hubs de Eventos
> * Ler tweets dos Hubs de Eventos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!Note]
> Este tutorial não pode ser efetuado através de **subscrição de avaliação gratuita do Azure**.
> Para utilizar uma conta gratuita para criar o cluster do Azure Databricks, antes de criar o cluster, aceda ao seu perfil e altere a subscrição para **pay as you go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, certifique-se de que cumpre os seguintes requisitos:
- Um espaço de nomes dos Hubs de Eventos do Azure.
- Um Hub de Eventos no espaço de nomes.
- Cadeia de ligação para aceder ao espaço de nomes dos Hubs de Eventos. A cadeia de ligação deve ter um formato semelhante a `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.
- O nome da política de acesso partilhado e a chave da política dos Hubs de Eventos.

Pode satisfazer estes requisitos, efetuando os passos no artigo, [Criar um espaço de nomes e um hub de eventos dos Hubs de Eventos do Azure](../event-hubs/event-hubs-create.md).

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Nesta secção, vai criar uma área de trabalho do Azure Databricks com o portal do Azure.

1. No Portal do Azure, selecione **Criar um recurso** > **Dados + Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](./media/databricks-stream-from-eventhubs/azure-databricks-on-portal.png "Databricks no portal do Azure")

3. Em **Serviço Azure Databricks**, forneça os valores para criar uma área de trabalho do Databricks.

    ![Criar uma área de trabalho do Azure Databricks](./media/databricks-stream-from-eventhubs/create-databricks-workspace.png "Criar uma área de trabalho do Azure Databricks")

    Forneça os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks        |
    |**Subscrição**     | Na lista pendente, selecione a sua subscrição do Azure.        |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Localização**     | Selecione **E.U.A. Leste 2**. Para outras regiões disponíveis, veja [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).        |
    |**Escalão de Preço**     |  Escolha entre **Standard** ou **Premium**. Para obter mais informações sobre estes escalões, veja [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Selecione **Afixar ao dashboard** e, em seguida, selecione **Criar**.

4. A criação da conta demora alguns minutos. Durante a criação da conta, o portal apresenta o mosaico **Submeter a implementação para o Azure Databricks** no lado direito. Pode ter de se deslocar para a direita no dashboard para ver o mosaico. É também apresentada uma barra de progresso junto à parte superior do ecrã. Pode ver qualquer área de progresso.

    ![Mosaico de implementação do Databricks](./media/databricks-stream-from-eventhubs/databricks-deployment-tile.png "Mosaico de implementação do Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

1. No portal do Azure, aceda à área de trabalho do Databricks que criou e, em seguida, selecione **Iniciar Área de Trabalho**.

2. Será redirecionado para o portal do Azure Databricks. No portal, selecione **Cluster**.

    ![Databricks no Azure](./media/databricks-stream-from-eventhubs/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar um cluster Databricks Spark no Azure](./media/databricks-stream-from-eventhubs/create-databricks-spark-cluster.png "Criar um cluster Databricks Spark no Azure")

    Aceite todos os outros valores predefinidos que não sejam os seguintes:

   * Introduza um nome para o cluster.
   * Para este artigo, crie um cluster com o runtime **4.0**.
   * Certifique-se de que seleciona a caixa de verificação **Terminar após \_\_ minutos de atividade**. Indique uma duração (em minutos) para terminar o cluster, caso não esteja a ser utilizado.

     Selecione **Criar cluster**. Depois de o cluster estar em execução, pode anexar blocos de notas ao cluster e executar tarefas do Spark.

## <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter

Para receber um fluxo de tweets, crie uma aplicação no Twitter. Siga as instruções para criar uma aplicação no Twitter e registe os valores que precisa para concluir este tutorial.

1. A partir de um browser, aceda a [Gestão de Aplicações do Twitter](https://apps.twitter.com/) e selecione **Criar Nova Aplicação**.

    ![Criar aplicação do Twitter](./media/databricks-stream-from-eventhubs/databricks-create-twitter-app.png "Criar aplicação do Twitter")

2. Na página **Criar uma aplicação**, forneça os detalhes da nova aplicação e, em seguida, selecione **Criar a sua aplicação do Twitter**.

    ![Detalhes da aplicação no Twitter](./media/databricks-stream-from-eventhubs/databricks-provide-twitter-app-details.png "Detalhes da aplicação no Twitter")

3. Na página da aplicação, selecione o separador **Chaves e Tokens de Acesso** e copie os valores de **Chave de Consumo** e **Segredo de Consumidor**. Além disso, selecione **Criar o meu token de acesso** para gerar os tokens de acesso. Copie os valores de **Token de Acesso** e **Segredo de Token de Acesso**.

    ![Detalhes da aplicação no Twitter](./media/databricks-stream-from-eventhubs/twitter-app-key-secret.png "Detalhes da aplicação no Twitter")

Guarde os valores que obteve da aplicação do Twitter. Vai precisar dos valores mais tarde no tutorial.

## <a name="attach-libraries-to-spark-cluster"></a>Anexar bibliotecas ao cluster do Spark

Neste tutorial, vai utilizar as APIs do Twitter para enviar tweets para os Hubs de Eventos. Também pode utilizar o [Conector de Hubs de eventos do Spark](https://github.com/Azure/azure-event-hubs-spark) para ler e escrever dados em Hubs de Eventos do Azure. Para utilizar estas APIs como parte do cluster, adicione-as como bibliotecas ao Azure Databricks e, em seguida, associe-as ao cluster do Spark. As instruções seguintes mostram como adicionar a biblioteca à pasta **Partilhados** da sua área de trabalho.

1. Na área de trabalho do Azure Databricks, selecione **Área de Trabalho** e, em seguida, clique com botão direito do rato em **Partilhados**. No menu de contexto, selecione **Criar** > **Biblioteca**.

   ![Caixa de diálogo Adicionar biblioteca](./media/databricks-stream-from-eventhubs/databricks-add-library-option.png "Caixa de diálogo Adicionar biblioteca")

2. Na página Nova Biblioteca, em **Origem**, selecione **Coordenada do Maven**. Em **Coordenada**, introduza a coordenada do pacote que quer adicionar. Eis as coordenadas do Maven para as bibliotecas utilizadas neste tutorial:

   * Conector de Hubs de Eventos do Spark – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.1`
   * API do Twitter – `org.twitter4j:twitter4j-core:4.0.6`

     ![Fornecer coordenadas do Maven](./media/databricks-stream-from-eventhubs/databricks-eventhub-specify-maven-coordinate.png "Fornecer coordenadas do Maven")

3. Selecione **Criar Biblioteca**.

4. Selecione a pasta onde adicionou a biblioteca e, em seguida, selecione o nome da biblioteca.

    ![Selecionar biblioteca a adicionar](./media/databricks-stream-from-eventhubs/select-library.png "Selecionar biblioteca a adicionar")

5. Na página da biblioteca, selecione o cluster em que quer utilizar a biblioteca. Depois de a biblioteca ser associada ao cluster com êxito, o estado muda imediatamente para **Anexada**.

    ![Anexar biblioteca ao cluster](./media/databricks-stream-from-eventhubs/databricks-library-attached.png "Anexar biblioteca ao cluster")

6. Repita estes passos para o pacote do Twitter, `twitter4j-core:4.0.6`.

## <a name="create-notebooks-in-databricks"></a>Criar blocos de notas no Databricks

Nesta secção, vai criar dois blocos de notas na área de trabalho do Databricks, com os seguintes nomes:

- **SendTweetsToEventHub** – Um bloco de notas de produtor que utiliza para obter tweets do Twitter e transmiti-los para os Hubs de Eventos.
- **ReadTweetsFromEventHub** – Um bloco de notas de consumidor que utiliza para ler os tweets dos Hubs de Eventos.

1. No painel esquerdo, selecione **Área de Trabalho**. No menu pendente **Área de Trabalho**, selecione **Criar** > **Bloco de Notas**.

    ![Criar um bloco de notas no Databricks](./media/databricks-stream-from-eventhubs/databricks-create-notebook.png "Criar um bloco de notas no Databricks")

2. Na caixa de diálogo **Criar Bloco de Notas**, introduza **SendTweetsToEventHub**, selecione **Scala** como linguagem e selecione o cluster do Spark que criou anteriormente.

    ![Criar um bloco de notas no Databricks](./media/databricks-stream-from-eventhubs/databricks-notebook-details.png "Criar um bloco de notas no Databricks")

    Selecione **Criar**.

3. Repita os passos para criar o bloco de notas **ReadTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Enviar tweets para os Hubs de Eventos

Na **SendTweetsToEventHub** bloco de notas, cole o seguinte código e substitua os marcadores de posição pelos valores de espaço de nomes de Hubs de eventos e a aplicação do Twitter que criou anteriormente. Este bloco de notas transmite os tweets com a palavra-chave "Azure" para os Hubs de Eventos em tempo real.

```scala
    import java.util._
    import scala.collection.JavaConverters._
    import com.microsoft.azure.eventhubs._
    import java.util.concurrent._

    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sendEvent(message: String) = {
      val messageData = EventData.create(message.getBytes("UTF-8"))
      eventHubClient.get().send(messageData)
      System.out.println("Sent event: " + message + "\n")
    }

    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter configuration!
    // Replace values below with yours

    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"

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

Para executar o bloco de notas, prima **SHIFT + ENTER**. Pode ver um resultado como o fragmento abaixo. Cada evento no resultado é um tweet que é ingerido nos Hubs de Eventos com o termo "Azure".

    Sent event: @Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence

    Sent event: Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure

    Sent event: 4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie

    Sent event: Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk

    Sent event: Top 10 Tricks to #Save Money with #Azure Virtual Machines https://t.co/F2wshBXdoz #Cloud

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Ler tweets dos Hubs de Eventos

No bloco de notas **ReadTweetsFromEventHub**, cole o seguinte código e substitua o marcador de posição pelos valores dos Hubs de Eventos do Azure que criou anteriormente. Este bloco de notas lê os tweets que transmitiu anteriormente para os Hubs de Eventos através do bloco de notas **SendTweetsToEventHub**.

```scala
    import org.apache.spark.eventhubs._

    // Build connection string with the above information
    val connectionString = ConnectionStringBuilder("<EVENT HUBS CONNECTION STRING>")
      .setEventHubName("<EVENT HUB NAME>")
      .build

    val customEventhubParameters =
      EventHubsConf(connectionString)
      .setMaxEventsPerTrigger(5)

    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

    incomingStream.printSchema

    // Sending the incoming stream into the console.
    // Data comes in batches!
    incomingStream.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Obtém o resultado seguinte:


    root
     |-- body: binary (nullable = true)
     |-- offset: long (nullable = true)
     |-- seqNumber: long (nullable = true)
     |-- enqueuedTime: long (nullable = true)
     |-- publisher: string (nullable = true)
     |-- partitionKey: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+------+--------------+---------------+---------+------------+
    |body  |offset|sequenceNumber|enqueuedTime   |publisher|partitionKey|
    +------+------+--------------+---------------+---------+------------+
    |[50 75 62 6C 69 63 20 70 72 65 76 69 65 77 20 6F 66 20 4A 61 76 61 20 6F 6E 20 41 70 70 20 53 65 72 76 69 63 65 2C 20 62 75 69 6C 74 2D 69 6E 20 73 75 70 70 6F 72 74 20 66 6F 72 20 54 6F 6D 63 61 74 20 61 6E 64 20 4F 70 65 6E 4A 44 4B 0A 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 37 76 73 37 63 4B 74 76 61 68 20 0A 23 63 6C 6F 75 64 63 6F 6D 70 75 74 69 6E 67 20 23 41 7A 75 72 65]                              |0     |0             |2018-03-09 05:49:08.86 |null     |null        |
    |[4D 69 67 72 61 74 65 20 79 6F 75 72 20 64 61 74 61 62 61 73 65 73 20 74 6F 20 61 20 66 75 6C 6C 79 20 6D 61 6E 61 67 65 64 20 73 65 72 76 69 63 65 20 77 69 74 68 20 41 7A 75 72 65 20 53 51 4C 20 44 61 74 61 62 61 73 65 20 4D 61 6E 61 67 65 64 20 49 6E 73 74 61 6E 63 65 20 7C 20 23 41 7A 75 72 65 20 7C 20 23 43 6C 6F 75 64 20 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 73 4A 48 58 4E 34 74 72 44 6B]            |168   |1             |2018-03-09 05:49:24.752|null     |null        |
    +------+------+--------------+---------------+---------+------------+

    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Dado que o resultado está num modo binário, utilize o seguinte fragmento para convertê-lo numa cadeia.

```scala
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so we cast it to string to see the actual content of the message
    val messages =
      incomingStream
      .withColumn("Offset", $"offset".cast(LongType))
      .withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType))
      .withColumn("Timestamp", $"enqueuedTime".cast(LongType))
      .withColumn("Body", $"body".cast(StringType))
      .select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

O resultado assemelha-se agora ao seguinte fragmento:

    root
     |-- Offset: long (nullable = true)
     |-- Time (readable): timestamp (nullable = true)
     |-- Timestamp: long (nullable = true)
     |-- Body: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+-----------------+----------+-------+
    |Offset|Time (readable)  |Timestamp |Body
    +------+-----------------+----------+-------+
    |0     |2018-03-09 05:49:08.86 |1520574548|Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure          |
    |168   |2018-03-09 05:49:24.752|1520574564|Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |
    |0     |2018-03-09 05:49:02.936|1520574542|@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|
    |176   |2018-03-09 05:49:20.801|1520574560|4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |
    +------+-----------------+----------+-------+
    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Já está! Ao utilizar o Azure Databricks, transmitiu com êxito os dados para os Hubs de Eventos do Azure em quase tempo real. Em seguida, consumiu os dados de transmissão através do conector dos Hubs de Eventos para o Apache Spark. Para obter mais informações sobre como utilizar o conector Hubs de Eventos para Spark, veja a [documentação do conector](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).

## <a name="clean-up-resources"></a>Limpar recursos

Depois de executar o tutorial, pode terminar o cluster. Para tal, na área de trabalho do Azure Databricks, no painel esquerdo, selecione **Clusters**. Para o cluster que quer terminar, mova o cursor sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**.

![Parar um cluster do Databricks](./media/databricks-stream-from-eventhubs/terminate-databricks-cluster.png "Parar um cluster do Databricks")

Se não terminar manualmente o cluster, este irá parar automaticamente, desde que tenha selecionado a **terminar após \_ \_ minutos de inatividade** caixa de verificação ao criar o cluster. Nesse caso, o cluster para automaticamente se tiver estado inativo durante o período de tempo especificado.

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma área de trabalho do Azure Databricks
> * Criar um cluster do Spark no Azure Databricks
> * Criar uma aplicação no Twitter para gerar dados de transmissão
> * Criar blocos de notas no Azure Databricks
> * Adicionar bibliotecas para a API do Twitter e dos Hubs de Eventos
> * Enviar tweets para os Hubs de Eventos
> * Ler tweets dos Hubs de Eventos

Avance para o próximo tutorial para saber como efetuar uma análise de sentimentos nos dados de transmissão através do Azure Databricks e da [API dos Serviços Cognitivos da Microsoft](../cognitive-services/text-analytics/overview.md).

> [!div class="nextstepaction"]
>[Análise de sentimentos na transmissão em fluxo de dados com o Azure Databricks](databricks-sentiment-analysis-cognitive-services.md)
