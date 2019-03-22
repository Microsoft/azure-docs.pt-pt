---
title: 'Tutorial: A transmissão em fluxo estruturada com o Apache Kafka - Azure HDInsight do Apache Spark'
description: Saiba como utilizar a transmissão em fluxo do Apache Spark para introduzir ou extrair dados do Apache Kafka. Neste tutorial, vai transmitir dados através de um bloco de notas Jupyter do Spark no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e470f9d6f33e8b534be22132881f4806bed85f5d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078368"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Tutorial: Utilizar o Apache Spark estruturado de transmissão em fluxo com o Apache Kafka no HDInsight

Este tutorial demonstra como usar [transmissão estruturada do Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) ler e escrever dados com [Apache Kafka](https://kafka.apache.org/) no Azure HDInsight.

A transmissão em fluxo estruturada do Spark é um motor de processamento de fluxos incorporado no SQL do Spark. Permite-lhe expressar computações de transmissão em fluxo, tal como a computação em lotes o faz em dados estáticos.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Transmissão em Fluxo Estruturada com o Kafka
> * Criar clusters do Kafka e do Spark
> * Carregar o bloco de notas para o Spark
> * Utilizar o bloco de notas
> * Limpar recursos

Quando tiver concluído os passos neste documento, elimine os clusters para evitar encargos em excesso.

## <a name="prerequisites"></a>Pré-requisitos

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

| Batch | Transmissão em Fluxo |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

A operação de transmissão em fluxo também utiliza `awaitTermination(30000)`, que para a transmissão em fluxo após 30000 ms. 

Para utilizar a Transmissão em Fluxo Estruturada com o Kafka, o projeto tem de ter uma dependência no pacote `org.apache.spark : spark-sql-kafka-0-10_2.11`. A versão deste pacote deve corresponder à versão do Spark no HDInsight. Para o Spark 2.2.0 (disponível no HDInsight 3.6), pode encontrar as informações de dependência para diferentes tipos de projeto em [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Para o Jupyter Notebook fornecido com este tutorial, a célula seguinte carrega esta dependência de pacote:

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
    
    O modelo do Azure Resource Manager está localizado em **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

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

## <a name="upload-the-notebook"></a>Carregar o bloco de notas

Para carregar o bloco de notas do projeto para o cluster Spark no HDInsight, siga os passos seguintes:

1. Transfira o projeto a partir de [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

1. No seu browser, ligue ao bloco de notas Jupyter no cluster do Spark. No seguinte URL, substitua `CLUSTERNAME` pelo nome do seu cluster do __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando lhe for pedido, introduza o início de sessão do cluster (admin) e a palavra-passe utilizada quando criou o cluster.

2. No lado direito superior da página, utilize o botão __Carregar__ para carregar o ficheiro __spark-structured-streaming-kafka.ipynb__ para o cluster. Selecione __Abrir__ para iniciar o carregamento.

    ![Utilizar o botão de carregamento para selecionar e carregar um bloco de notas](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Selecionar o ficheiro KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Localize a entrada __spark-structured-streaming-kafka.ipynb__ na lista de blocos de notas e selecione o botão __Carregar__ junto à mesma.

    ![Para carregar o bloco de notas, utilizar o botão de carregamento para a entrada KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Utilizar o bloco de notas

Depois de os ficheiros serem carregados, selecione a entrada __spark-structured-streaming-kafka.ipynb__ para abrir o bloco de notas. Para saber como utilizar a transmissão em fluxo estruturada do Spark com o Kafka no HDInsight, siga as instruções no bloco de notas.

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
