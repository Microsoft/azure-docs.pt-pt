---
title: 'Tutorial: Apache Spark Streaming & Apache Kafka - Azure HDInsight'
description: Saiba como utilizar a transmissão em fluxo do Apache Spark para introduzir ou extrair dados do Apache Kafka. Neste tutorial, vai transmitir dados através de um bloco de notas Jupyter do Spark no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,seodec18,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 8aa7401a2ee7a0d87736e6b18fc814f983e2afa0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184202"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Tutorial: Utilizar a Transmissão em Fluxo Estruturada do Apache Spark com o Apache Kafka no HDInsight

Este tutorial demonstra como usar o [Apache Spark Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) para ler e escrever dados com Apache [Kafka](./kafka/apache-kafka-introduction.md) no Azure HDInsight.

Spark Structured Streaming é um motor de processamento de fluxo construído em Spark SQL. Permite-lhe expressar computações de transmissão em fluxo, tal como a computação em lotes o faz em dados estáticos.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Use um modelo de Gestor de Recursos Azure para criar clusters
> * Use spark structured streaming com Kafka

Quando terminar os passos deste documento, lembre-se de apagar os clusters para evitar excessos de encargos.

## <a name="prerequisites"></a>Pré-requisitos

* jq, um processador JSON de linha de comando.  Vê. [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)

* Familiaridade com a utilização de [Cadernos Jupyter](https://jupyter.org/) com Faísca no HDInsight. Para mais informações, consulte os [dados da Carga e execute consultas com a Apache Spark no documento HDInsight.](spark/apache-spark-load-data-run-query.md)

* Familiaridade com a linguagem de programação Scala. O código utilizado neste tutorial está escrito em Scala.

* Familiaridade com a criação de tópicos do Kafka. Para mais informações, consulte o Apache Kafka no documento [de arranque rápido da HDInsight.](kafka/apache-kafka-get-started.md)

> [!IMPORTANT]  
> Os passos neste documento requerem um grupo de recursos do Azure que contém um cluster do Spark no HDInsight e um cluster do Kafka no HDInsight. Estes dois clusters estão localizados numa Rede Virtual do Azure, o que permite que o cluster do Spark comunique diretamente com o cluster do Kafka.
>
> Para sua comodidade, este documento tem uma ligação para o modelo que pode criar todos os recursos do Azure necessários.
>
> Para obter mais informações sobre a utilização do HDInsight numa rede virtual, consulte o Plano de uma rede virtual para o documento [HDInsight.](hdinsight-plan-virtual-network-deployment.md)

## <a name="structured-streaming-with-apache-kafka"></a>Streaming estruturado com Apache Kafka

A transmissão em Fluxo Estruturada do Spark é um motor de processamento de fluxos incorporado no motor SQL do Spark. Ao utilizar o Streaming Estruturado, pode escrever consultas de streaming da mesma forma que escreve consultas de lote.

Os fragmentos de código seguintes demonstram a leitura a partir do Kafka e o armazenamento num ficheiro. A primeira é uma operação em lote, enquanto que a segunda é uma operação de transmissão em fluxo:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

Em ambos os fragmentos, os dados são lidos a partir do Kafka e escritos num ficheiro. As diferenças entre os exemplos são:

| Batch | Transmissão em Fluxo |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

A operação de `awaitTermination(30000)`streaming também utiliza , que para o fluxo após 30.000 ms.

Para utilizar a Transmissão em Fluxo Estruturada com o Kafka, o projeto tem de ter uma dependência no pacote `org.apache.spark : spark-sql-kafka-0-10_2.11`. A versão deste pacote deve corresponder à versão do Spark no HDInsight. Para o Spark 2.2.0 (disponível no HDInsight 3.6), pode [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar)encontrar a informação de dependência para diferentes tipos de projeto em .

Para o Caderno Jupyter usado com este tutorial, a seguinte célula carrega esta dependência do pacote:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não dá acesso aos corretores Kafka através da internet pública. Tudo o que utilize Kafka tem de estar na mesma rede virtual do Azure. Neste tutorial, os clusters do Kafka e do Spark estão localizados na mesma rede virtual do Azure.

O diagrama seguinte mostra como a comunicação flui entre o Spark e o Kafka:

![Diagrama de clusters do Spark e Kafka numa rede virtual do Azure](./media/hdinsight-apache-kafka-spark-structured-streaming/apache-spark-kafka-vnet.png)

> [!NOTE]  
> O serviço Kafka está limitado à comunicação na rede virtual. Outros serviços em cluster, como SSH e Ambari, podem ser acedidos através da Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Para criar uma Rede Virtual do Azure e, em seguida, criar os clusters do Kafka e do Spark na mesma, utilize os passos abaixo:

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-kafka-spark-structured-streaming/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    O modelo do Gestor de **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**Recursos Azure está localizado em .

    Este modelo cria os seguintes recursos:

   * Um cluster do Kafka no HDInsight 3.6.
   * Um cluster do Spark 2.2.0 no HDInsight 3.6.
   * Uma Rede Virtual do Azure, que contém os clusters do HDInsight.

     > [!IMPORTANT]  
     > O bloco de notas de transmissão em fluxo estruturada utilizado neste tutorial requer o Spark 2.2.0 no HDInsight 3.6. Se utilizar uma versão anterior do Spark no HDInsight, irá receber mensagens de erro ao utilizar o bloco de notas.

2. Utilize as seguintes informações para preencher as entradas da secção **Modelo personalizado**:

    | Definição | Valor |
    | --- | --- |
    | Subscrição | A sua subscrição do Azure |
    | Grupo de recursos | O grupo de recursos que contém os recursos. |
    | Localização | A região do Azure na qual os recursos são criados. |
    | Nome de Cluster do Spark | O nome do cluster do Spark. Os primeiros seis carateres devem ser diferentes do nome do cluster do Kafka. |
    | Nome do Cluster do Kafka | O nome do cluster do Kafka. Os primeiros seis carateres devem ser diferentes do nome do cluster do Spark. |
    | Nome de Utilizador de Início de Sessão do Cluster | O nome de utilizador administrador para os clusters. |
    | Palavra-passe de Início de Sessão do Cluster | A palavra-passe de utilizador administrador para os clusters. |
    | Nome de Utilizador SSH | O nome de utilizador SSH para os clusters. |
    | Palavra-passe do SSH | A palavra-passe do utilizador SSH. |

    ![Captura de ecrã do modelo personalizado](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Leia os **Termos e Condições**, em seguida, selecione **Concordo com os termos e condições acima indicados**.

4. Selecione **Comprar**.

> [!NOTE]  
> A criação dos clusters pode demorar até 20 minutos.

## <a name="use-spark-structured-streaming"></a>Utilizar o streaming estruturado de faíscas

Este exemplo demonstra como usar o Spark Structured Streaming com kafka no HDInsight. Utiliza dados sobre viagens de táxi, que são fornecidas pela cidade de Nova Iorque.  O conjunto de dados utilizado por este caderno é de Dados de Viagem de Táxi Verde de [2016.](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb)

1. Reúna informações do anfitrião. Use os comandos de caracóis e [jq](https://stedolan.github.io/jq/) abaixo para obter o seu ZooKeeper Kafka e o corretor acolhe informações. Os comandos foram concebidos para um pedido de comando windows, pequenas variações serão necessárias para outros ambientes. Substitua-o `KafkaCluster` pelo nome do `KafkaPassword` seu cluster Kafka e pela senha de login do cluster. Além `C:\HDI\jq-win64.exe` disso, substitua-o pelo caminho real para a sua instalação jq. Introduza os comandos num pedido de comando windows e guarde a saída para utilização em etapas posteriores.

    ```cmd
    REM Enter cluster name in lowercase

    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

1. De um navegador web, navegue até, `https://CLUSTERNAME.azurehdinsight.net/jupyter`onde `CLUSTERNAME` está o nome do seu cluster. Quando lhe for pedido, introduza o início de sessão do cluster (admin) e a palavra-passe utilizada quando criou o cluster.

1. Selecione **New > Spark** para criar um caderno.

1. O streaming de faíscas tem microbatching, o que significa que os dados vêm como lotes e executores executam nos lotes de dados. Se o executor tiver um tempo inativo inferior ao tempo necessário para processar o lote, então os executores serão constantemente adicionados e removidos. Se o tempo limite dos executores for maior do que a duração do lote, o executor nunca é removido. Por **isso, recomendamos que desative a atribuição dinâmica definindo spark.dynamicAllocation.habilitado a falsear ao executar aplicações** de streaming.

    Carregue os pacotes utilizados pelo Caderno, inserindo as seguintes informações numa célula do Caderno. Executar o comando utilizando **CTRL + ENTER**.

    ```configuration
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11",
            "spark.dynamicAllocation.enabled": false
        }
    }
    ```

1. Crie o tópico kafka. Editar o comando abaixo `YOUR_ZOOKEEPER_HOSTS` substituindo-se pela informação do hospedeiro zookeeper extraída no primeiro passo. Insira o comando editado no seu `tripdata` Caderno Jupyter para criar o tópico.

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

1. Recolha dados sobre viagens de táxi. Insira o comando na próxima célula para carregar dados em viagens de táxi em Nova Iorque. Os dados são carregados num quadro de dados e, em seguida, o dataframe é apresentado como a saída da célula.

    ```scala
    import spark.implicits._

    // Load the data from the New York City Taxi data REST API for 2016 Green Taxi Trip Data
    val url="https://data.cityofnewyork.us/resource/pqfs-mqru.json"
    val result = scala.io.Source.fromURL(url).mkString

    // Create a dataframe from the JSON data
    val taxiDF = spark.read.json(Seq(result).toDS)

    // Display the dataframe containing trip data
    taxiDF.show()
    ```

1. Despinde o corretor Kafka recebe informações. Substitua-a `YOUR_KAFKA_BROKER_HOSTS` pelo corretor que acolhe informações extraídas no passo 1.  Introduza o comando editado na próxima célula jupyter Notebook.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"

    println("Finished setting Kafka broker and topic configuration.")
    ```

1. Envie os dados para Kafka. No comando seguinte, `vendorid` o campo é usado como o valor chave para a mensagem Kafka. A chave é usada por Kafka ao dividir dados. Todos os campos estão guardados na mensagem Kafka como um valor de corda JSON. Insira o seguinte comando em Jupyter para guardar os dados para Kafka usando uma consulta de lote.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

1. Declare um esquema. O seguinte comando demonstra como usar um esquema ao ler dados da JSON de kafka. Entra no comando na tua próxima célula Jupyter.

    ```scala
    // Import bits useed for declaring schemas and working with JSON data
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Define a schema for the data
    val schema = (new StructType).add("dropoff_latitude", StringType).add("dropoff_longitude", StringType).add("extra", StringType).add("fare_amount", StringType).add("improvement_surcharge", StringType).add("lpep_dropoff_datetime", StringType).add("lpep_pickup_datetime", StringType).add("mta_tax", StringType).add("passenger_count", StringType).add("payment_type", StringType).add("pickup_latitude", StringType).add("pickup_longitude", StringType).add("ratecodeid", StringType).add("store_and_fwd_flag", StringType).add("tip_amount", StringType).add("tolls_amount", StringType).add("total_amount", StringType).add("trip_distance", StringType).add("trip_type", StringType).add("vendorid", StringType)
    // Reproduced here for readability
    //val schema = (new StructType)
    //   .add("dropoff_latitude", StringType)
    //   .add("dropoff_longitude", StringType)
    //   .add("extra", StringType)
    //   .add("fare_amount", StringType)
    //   .add("improvement_surcharge", StringType)
    //   .add("lpep_dropoff_datetime", StringType)
    //   .add("lpep_pickup_datetime", StringType)
    //   .add("mta_tax", StringType)
    //   .add("passenger_count", StringType)
    //   .add("payment_type", StringType)
    //   .add("pickup_latitude", StringType)
    //   .add("pickup_longitude", StringType)
    //   .add("ratecodeid", StringType)
    //   .add("store_and_fwd_flag", StringType)
    //   .add("tip_amount", StringType)
    //   .add("tolls_amount", StringType)
    //   .add("total_amount", StringType)
    //   .add("trip_distance", StringType)
    //   .add("trip_type", StringType)
    //   .add("vendorid", StringType)

    println("Schema declared")
    ```

1. Selecione dados e inicie o fluxo. O seguinte comando demonstra como recuperar dados de Kafka usando uma consulta de lote. E depois escreva os resultados para o HDFS no cluster Spark. Neste exemplo, `select` a mensagem (campo de valor) de Kafka e aplica-lhe o esquema. Os dados são então escritos para HDFS (WASB ou ADL) em formato parquet. Entra no comando na tua próxima célula Jupyter.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()

    println("Wrote data to file")
    ```

1. Pode verificar se os ficheiros foram criados entrando no comando na sua próxima célula Jupyter. Lista os ficheiros `/example/batchtripdata` no diretório.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

1. Enquanto o exemplo anterior usava uma consulta de lote, o seguinte comando demonstra como fazer a mesma coisa usando uma consulta de streaming. Entra no comando na tua próxima célula Jupyter.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

1. Verifique a célula seguinte para verificar se os ficheiros foram escritos pela consulta de streaming.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, pode eliminar o grupo de recursos. A eliminação do grupo de recursos também elimina o cluster HDInsight associado. E quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No [portal Azure,](https://portal.azure.com/)expanda o menu do lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos__ de Recursos para exibir a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

> [!WARNING]  
> A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado.
>
> Eliminar um cluster do Kafka no HDInsight elimina quaisquer dados armazenados no Kafka.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o Apache Spark Structured Streaming. Para escrever e ler dados de Apache Kafka no HDInsight. Use o seguinte link para aprender a usar a Tempestade Apache com Kafka.

> [!div class="nextstepaction"]
> [Use apache tempestade com Apache Kafka](hdinsight-apache-storm-with-kafka.md)
