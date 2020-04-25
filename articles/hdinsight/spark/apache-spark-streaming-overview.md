---
title: Streaming de faíscas em Azure HDInsight
description: Como utilizar aplicações de Streaming de Faíscas Apache em clusters HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/23/2020
ms.openlocfilehash: 98e6aedd643eb5363a3f4f3fc54984e85830aa34
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133545"
---
# <a name="overview-of-apache-spark-streaming"></a>Visão geral do Apache Spark Streaming

[Faísca Apache](https://spark.apache.org/) O streaming fornece processamento de fluxo de dados em clusters HDInsight Spark. Com a garantia de que qualquer evento de entrada é processado exatamente uma vez, mesmo que ocorra uma falha no nó. A Spark Stream é um trabalho de longa data que recebe dados de entrada de uma grande variedade de fontes, incluindo hubs de eventos Azure. Também: Azure IoT Hub, Apache Kafka, `ZeroMQ`Apache Flume, Twitter, tomadas cruas de TCP, ou de monitorizar sistemas de ficheiros YARN Apache Hadoop. Ao contrário de um processo exclusivamente orientado para o evento, um Spark Stream batches input dados em janelas de tempo. Como uma fatia de 2 segundos, e depois transforma cada lote de dados usando o mapa, reduza, junte e extrata operações. O Spark Stream escreve então os dados transformados para sistemas de ficheiros, bases de dados, dashboards e a consola.

![Processamento de fluxo com HDInsight e Streaming de Faíscas](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

As aplicações spark streaming devem esperar uma `micro-batch` fração de segundo para recolher cada um dos eventos antes de enviar esse lote para processamento. Em contraste, uma aplicação orientada para eventos processa cada evento imediatamente. A latência do Spark Streaming é normalmente inferior a alguns segundos. Os benefícios da abordagem do microlote são o processamento de dados mais eficiente e os cálculos agregados mais simples.

## <a name="introducing-the-dstream"></a>Apresentando o DStream

O Spark Streaming representa um fluxo contínuo de dados de entrada utilizando um *fluxo discreto* chamado DStream. Um DStream pode ser criado a partir de fontes de entrada como Event Hubs ou Kafka. Ou aplicando transformações noutro DStream.

Um DStream fornece uma camada de abstração em cima dos dados do evento bruto.

Comece com um único evento, digamos, uma leitura de temperatura de um termóstato conectado. Quando este evento chega à sua aplicação Spark Streaming, o evento é armazenado de forma fiável, onde é replicado em vários nós. Esta tolerância à falha garante que a falha de qualquer nó não resultará na perda do seu evento. O núcleo de Faíscas utiliza uma estrutura de dados que distribui dados através de vários nós do cluster. Onde cada nó geralmente mantém os seus próprios dados em memória para melhor desempenho. Esta estrutura de dados é chamada de conjunto de *dados distribuído si (RDD) resiliente.*

Cada RDD representa eventos recolhidos num prazo definido pelo utilizador chamado intervalo do *lote*. À medida que cada intervalo de lote decorre, é produzido um novo DDr que contém todos os dados desse intervalo. O conjunto contínuo de RDDs é recolhido num DStream. Por exemplo, se o intervalo do lote for de um segundo de comprimento, o seu DStream emite um lote a cada segundo contendo um RDD que contém todos os dados ingeridos durante esse segundo. Ao processar o DStream, o evento de temperatura aparece num destes lotes. Uma aplicação Spark Streaming processa os lotes que contêm os eventos e, em última análise, atua nos dados armazenados em cada RDD.

![Exemplo DStream com eventos de temperatura](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Estrutura de uma aplicação Spark Streaming

Uma aplicação Spark Streaming é uma aplicação de longa duração que recebe dados de fontes ingerir. Aplica transformações para processar os dados e, em seguida, empurra os dados para um ou mais destinos. A estrutura de uma aplicação Spark Streaming tem uma parte estática e uma parte dinâmica. A parte estática define de onde vêm os dados, que tratamento a fazer nos dados. E para onde devem ir os resultados. A parte dinâmica está a executar a aplicação indefinidamente, à espera de um sinal de stop.

Por exemplo, a seguinte aplicação simples recebe uma linha de texto sobre uma tomada TCP e conta o número de vezes que cada palavra aparece.

### <a name="define-the-application"></a>Definir a aplicação

A definição lógica de aplicação tem quatro passos:

1. Criar um StreamingContext.
2. Crie um DStream a partir do StreamingContext.
3. Aplique transformações no DStream.
4. Produza os resultados.

Esta definição é estática, e nenhum dado é processado até executar a aplicação.

#### <a name="create-a-streamingcontext"></a>Criar um StreamingContext

Crie um StreamingContext a partir do SparkContext que aponta para o seu cluster. Ao criar um StreamingContext, especifice o tamanho do lote em segundos, por exemplo:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Criar um DStream

Com a instância StreamingContext, crie um DStream de entrada para a sua fonte de entrada. Neste caso, a aplicação está a tento ao aparecimento de novos ficheiros no armazenamento anexo predefinido.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Aplicar transformações

Implementa o processamento aplicando transformações no DStream. Esta aplicação recebe uma linha de texto de cada vez a partir do ficheiro, divide cada linha em palavras. E então usa um padrão de redução de mapas para contar o número de vezes que cada palavra aparece.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Resultados da produção

Empurre os resultados da transformação para os sistemas de destino aplicando operações de saída. Neste caso, o resultado de cada execução através da computação é impresso na saída da consola.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Executar a aplicação

Inicie a aplicação de streaming e corra até receber um sinal de rescisão.

```
ssc.start()
ssc.awaitTermination()
```

Para mais detalhes sobre a API Spark Stream, consulte o Guia de Programação de Streaming de [Faíscas Apache](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

A seguinte aplicação de amostra é autossuficiente, para que possa executá-la dentro de um [Caderno Jupyter](apache-spark-jupyter-notebook-kernels.md). Este exemplo cria uma fonte de dados falsa sacar na classe DummySource que produz o valor de um contador e o tempo atual em milissegundos a cada cinco segundos. Um novo objeto StreamingContext tem um intervalo de lote de 30 segundos. Sempre que um lote é criado, a aplicação de streaming examina o RDD produzido. Em seguida, converte o RDD para um Spark DataFrame, e cria uma tabela temporária sobre o DataFrame.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process RDDs in the batch
stream.foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Aguarde cerca de 30 segundos depois de iniciar a aplicação acima.  Em seguida, pode consultar periodicamente o DataFrame para ver o conjunto atual de valores presentes no lote, por exemplo, utilizando esta consulta SQL:

```sql
%%sql
SELECT * FROM demo_numbers
```

A saída resultante parece a seguinte saída:

| valor | hora |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Existem seis valores, uma vez que o DummySource cria um valor a cada 5 segundos e a aplicação emite um lote a cada 30 segundos.

## <a name="sliding-windows"></a>Janelas deslizantes

Para fazer cálculos agregados no seu DStream durante algum período de tempo, por `sliding window` exemplo, para obter uma temperatura média nos últimos dois segundos, utilize as operações incluídas no Spark Streaming. Uma janela deslizante tem uma duração (o comprimento da janela) e o intervalo durante o qual o conteúdo da janela é avaliado (o intervalo de diapositivos).

Janelas deslizantes podem sobrepor-se, por exemplo, pode definir uma janela com um comprimento de dois segundos, que desliza a cada segundo. Esta ação significa que cada vez que fizer um cálculo de agregação, a janela incluirá dados do último segundo da janela anterior. E quaisquer novos dados no próximo segundo.

![Exemplo janela inicial com eventos de temperatura](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Janela de exemplo com eventos de temperatura após deslizamento](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

O exemplo seguinte atualiza o código que utiliza o DummySource, para recolher os lotes numa janela com uma duração de um minuto e um slide de um minuto.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Após o primeiro minuto, há 12 entradas - seis entradas de cada um dos dois lotes recolhidos na janela.

| valor | hora |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

As funções de janela deslizantes disponíveis no Spark Streaming API incluem janela, contagemByWindow, reduçãoByWindow e contagemByValueAndWindow. Para mais detalhes sobre estas funções, consulte [Transformations on DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Ponto de verificação

Para fornecer resiliência e tolerância a falhas, o Spark Streaming baseia-se no checkpoint para garantir que o processamento de fluxo pode continuar ininterrupto, mesmo perante falhas no nó. A Spark cria postos de controlo para armazenamento duradouro (Armazenamento Azure ou Armazenamento de Lago sinuoso). Estes pontos de verificação armazenam metadados de aplicações de streaming, tais como a configuração, e as operações definidas pela aplicação. Além disso, quaisquer lotes que foram em fila, mas ainda não processados. Por vezes, os pontos de verificação também incluirão a poupança dos dados nos RDDs para reconstruir mais rapidamente o estado dos dados a partir do que está presente nos RDDs geridos pela Spark.

## <a name="deploying-spark-streaming-applications"></a>Implementação de aplicações de streaming de faíscas

Normalmente, constrói uma aplicação Spark Streaming localmente num ficheiro JAR. Em seguida, desloque-o para Spark on HDInsight copiando o ficheiro JAR para o armazenamento anexo predefinido. Pode iniciar a sua aplicação com as APIs LIVY REST disponíveis no seu cluster através de uma operação POST. O corpo do POST inclui um documento JSON que fornece o caminho para o seu JAR. E o nome da classe cujo método principal define e executa a aplicação de streaming, e opcionalmente os requisitos de recursos do trabalho (como o número de executores, memória e núcleos). Além disso, quaisquer configurações de configuração que o seu código de aplicação exija.

![Implementação de uma aplicação spark streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

O estado de todas as candidaturas também pode ser verificado com um pedido GET contra um ponto final LIVY. Por fim, pode terminar um pedido de execução emitindo um pedido DELETE contra o ponto final LIVY. Para mais detalhes sobre a API LIVY, consulte [trabalhos remotos com Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar um cluster Apache Spark no HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Guia de programação de streaming de faíscas Apache](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Visão geral do streaming estruturado de faíscas Apache](apache-spark-structured-streaming-overview.md)