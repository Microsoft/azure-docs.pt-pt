---
title: 'Tutorial: A transmissão em fluxo estruturada com o Apache Kafka - Azure HDInsight do Apache Spark'
description: Saiba como utilizar a transmissão em fluxo do Apache Spark para introduzir ou extrair dados do Apache Kafka. Neste tutorial, vai transmitir dados através de um bloco de notas Jupyter do Spark no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: hrasheed
ms.openlocfilehash: 51f84234ac35be5f60d1aaa5dac661ad9ce5e0c2
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257905"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Tutorial: Utilizar o Apache Spark estruturado de transmissão em fluxo com o Apache Kafka no HDInsight

Este tutorial demonstra como usar [transmissão estruturada do Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) ler e escrever dados com [Apache Kafka](https://kafka.apache.org/) no Azure HDInsight.

A transmissão estruturada do Spark é um motor de processamento de fluxos incorporado no Spark SQL. Permite-lhe expressar computações de transmissão em fluxo, tal como a computação em lotes o faz em dados estáticos.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar um modelo Azure Resource Manager para criar clusters
> * Utilizar o Spark estruturado de transmissão em fluxo com Kafka

Quando tiver concluído os passos neste documento, elimine os clusters para evitar encargos em excesso.

## <a name="prerequisites"></a>Pré-requisitos

* jq, um processador JSON da linha de comandos.  Ver [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

* Familiaridade com o uso [blocos de notas do Jupyter](https://jupyter.org/) com o Spark no HDInsight. Para obter mais informações, consulte a [carregar dados e executar consultas com o Apache Spark no HDInsight](spark/apache-spark-load-data-run-query.md) documento.

* Familiaridade com a linguagem de programação [Scala](https://www.scala-lang.org/). O código utilizado neste tutorial está escrito em Scala.

* Familiaridade com a criação de tópicos do Kafka. Para obter mais informações, consulte a [Apache Kafka no HDInsight guia de introdução](kafka/apache-kafka-get-started.md) documento.

> [!IMPORTANT]  
> Os passos neste documento requerem um grupo de recursos do Azure que contém um cluster do Spark no HDInsight e um cluster do Kafka no HDInsight. Estes dois clusters estão localizados numa Rede Virtual do Azure, o que permite que o cluster do Spark comunique diretamente com o cluster do Kafka.
> 
> Para sua comodidade, este documento tem uma ligação para o modelo que pode criar todos os recursos do Azure necessários. 
>
> Para obter mais informações sobre como utilizar o HDInsight numa rede virtual, veja o documento [Utilizar uma rede virtual para expandir o HDInsight](hdinsight-extend-hadoop-virtual-network.md).

## <a name="structured-streaming-with-apache-kafka"></a>Transmissão em fluxo com o Apache Kafka estruturada

A transmissão em Fluxo Estruturada do Spark é um motor de processamento de fluxos incorporado no motor SQL do Spark. Quando utilizar a Transmissão em Fluxo Estruturada, pode escrever consultas de transmissão em fluxo da mesma forma que escreve consultas em lote.

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

| Lote | Transmissão em fluxo |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

A operação de transmissão em fluxo também utiliza `awaitTermination(30000)`, que interrompe a transmissão após 30 000 ms. 

Para utilizar a Transmissão em Fluxo Estruturada com o Kafka, o projeto tem de ter uma dependência no pacote `org.apache.spark : spark-sql-kafka-0-10_2.11`. A versão deste pacote deve corresponder à versão do Spark no HDInsight. Para o Spark 2.2.0 (disponível no HDInsight 3.6), pode encontrar as informações de dependência para diferentes tipos de projeto em [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Para o bloco de notas Jupyter utilizado com este tutorial, a célula seguinte carrega esta dependência de pacote:

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

O Apache Kafka no HDInsight não fornece acesso aos mediadores Kafka através da Internet pública. Tudo o que utilize Kafka tem de estar na mesma rede virtual do Azure. Neste tutorial, os clusters do Kafka e do Spark estão localizados na mesma rede virtual do Azure. 

O diagrama seguinte mostra como a comunicação flui entre o Spark e o Kafka:

![Diagrama de clusters do Spark e Kafka numa rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> O serviço Kafka está limitado à comunicação na rede virtual. Outros serviços em cluster, como SSH e Ambari, podem ser acedidos através da Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, veja [Portas e URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Para criar uma Rede Virtual do Azure e, em seguida, criar os clusters do Kafka e do Spark na mesma, utilize os passos abaixo:

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>

    O modelo do Azure Resource Manager está localizado em **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** .

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

3. Leia os **Termos e Condições** e selecione **Aceito os temos e as condições apresentados acima**

4. Por fim, marque **Afixar ao dashboard** e, em seguida, selecione **Compra**. 

> [!NOTE]  
> A criação dos clusters pode demorar até 20 minutos.

## <a name="use-spark-structured-streaming"></a>Utilizar o Spark transmissão em fluxo estruturada

Este exemplo demonstra como utilizar o Spark transmissão em fluxo estruturada com o Kafka no HDInsight. Ele usa dados em viagens de táxis, que é fornecido por cidade de nova York.  O conjunto de dados utilizado por este bloco de notas é de [dados de viagens de táxis de verde 2016](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb).

1. Recolha informações do anfitrião. Utilize o curl e [jq](https://stedolan.github.io/jq/) comandos abaixo para obter sua ZooKeeper Kafka e Mediador informações dos anfitriões. Os comandos foram concebidos para um prompt de comando do Windows, pequenas variações serão necessários para outros ambientes. Substitua `KafkaCluster` com o nome do cluster do Kafka, e `KafkaPassword` com a palavra-passe de início de sessão do cluster. Substitua, também, `C:\HDI\jq-win64.exe` pelo caminho real para a instalação de jq. Introduza os comandos numa linha de comandos do Windows e guarde a saída para utilização em passos posteriores.

    ```cmd
    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

2. No seu browser, ligue ao bloco de notas Jupyter no cluster do Spark. No seguinte URL, substitua `CLUSTERNAME` pelo nome do seu cluster do __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando lhe for pedido, introduza o início de sessão do cluster (admin) e a palavra-passe utilizada quando criou o cluster.

3. Selecione **New > Spark** para criar um bloco de notas.

4. Carregar pacotes utilizados pelo bloco de notas, introduzindo as seguintes informações numa célula de bloco de notas. Execute o comando utilizando **CTRL + ENTER**.

    ```
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
        }
    }
    ```

5. Crie o tópico do Kafka. Editar o comando abaixo, substituindo `YOUR_ZOOKEEPER_HOSTS` com o Zookeeper alojar informações extraídas no primeiro passo. Introduza o comando editado em seu bloco de notas do Jupyter para criar o `tripdata` tópico.

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

6. Obter dados em viagens de táxis. Introduza o comando na próxima célula para carregar dados de viagens de táxis de nova York. Os dados são carregados para um dataframe e, em seguida, o pacote de dados é apresentado como o resultado da célula.

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

7. Defina as informações de anfitriões de Mediador Kafka. Substitua `YOUR_KAFKA_BROKER_HOSTS` com as informações de anfitriões de Mediador extraiu no passo 1.  Introduza o comando editado na próxima célula de bloco de notas do Jupyter.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"
    
    println("Finished setting Kafka broker and topic configuration.")
    ```

8. Envie os dados para o Kafka. No comando seguinte, o `vendorid` campo é utilizado como o valor da chave para a mensagem do Kafka. A chave é utilizada pelo Kafka quando a criação de partições de dados. Todos os campos são armazenados na mensagem de Kafka como um valor de cadeia de caracteres do JSON. Introduza o seguinte comando no Jupyter para guardar os dados ao Kafka usando uma consulta de batch.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

9. Declare um esquema. O comando seguinte demonstra como utilizar um esquema durante a leitura de dados JSON do kafka. Introduza o comando na sua próxima célula do Jupyter.

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

10. Selecione os dados e iniciar o fluxo. O comando seguinte demonstra como recuperar dados do kafka usando uma consulta de lote e, em seguida, escrever os resultados ao HDFS no cluster do Spark. Neste exemplo, o `select` obtém a mensagem (campo de valor) do Kafka e aplica-se o esquema ao mesmo. Os dados, em seguida, são escritos no HDFS (WASB ou ADL) em formato parquet. Introduza o comando na sua próxima célula do Jupyter.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()
    
    println("Wrote data to file")
    ```

11. Pode verificar que os ficheiros foram criados por introduzir o comando na sua próxima célula do Jupyter. Ele lista os ficheiros no `/example/batchtripdata` diretório.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

12. Embora o exemplo anterior utilizou uma consulta do batch, o comando seguinte demonstra como fazer a mesma coisa usando uma consulta de transmissão em fluxo. Introduza o comando na sua próxima célula do Jupyter.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

13. Execute a seguinte célula para verificar que os ficheiros foram escritos pela consulta transmissão em fluxo.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina o cluster do HDInsight associado e quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos através do Portal do Azure:

1. No Portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e, em seguida, escolha __Grupos de Recursos__, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e, em seguida, clique com o botão direito do rato em __Mais__ (...) no lado direito da lista.
3. Selecione __Eliminar grupo de recursos__ e, em seguida, confirme.

> [!WARNING]  
> A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado.
> 
> Eliminar um cluster do Kafka no HDInsight elimina quaisquer dados armazenados no Kafka.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a usar [transmissão estruturada do Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) de escrever e ler dados a partir de [Apache Kafka](https://kafka.apache.org/) no HDInsight. Utilize a seguinte ligação para saber como usar [Apache Storm](https://storm.apache.org/) com o Kafka.

> [!div class="nextstepaction"]
> [Utilizar o Apache Storm com o Apache Kafka](hdinsight-apache-storm-with-kafka.md)
