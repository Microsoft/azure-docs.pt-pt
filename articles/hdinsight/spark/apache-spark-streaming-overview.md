---
title: Transmissão em fluxo no Azure HDInsight Spark
description: Como utilizar aplicações de transmissão em fluxo do Spark nos clusters do Spark do HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 3ecabd683ed4303a7ff54780299ed0e83aa14c26
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57892084"
---
# <a name="overview-of-apache-spark-streaming"></a>Descrição geral do Apache Spark de transmissão em fluxo

[Apache Spark](https://spark.apache.org/) a transmissão em fluxo fornece processamento nos clusters do HDInsight Spark, com uma garantia de que qualquer evento de entrada de fluxo de dados é processado exatamente uma vez, mesmo se ocorrer uma falha de nó. Um Stream Spark é um trabalho de longa execução que recebe dados de entrada de uma grande variedade de origens, incluindo os Hubs de eventos do Azure, um IoT Hub do Azure, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ ](http://zeromq.org/), Soquetes TCP bruto, ou a partir de monitorização [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) sistemas de ficheiros. Ao contrário de um processo unicamente condicionada por eventos, um Stream Spark dados de entrada dos lotes em intervalos de tempo, por exemplo, um setor de 2 segundos e, em seguida, transforma cada lote de dados através do mapa, reduzir, Junte-se e extrair as operações. O Spark Stream, em seguida, escreve os dados transformados para sistemas de ficheiros, bases de dados, dashboards e a consola.

![Processar com o HDInsight e o Spark Streaming do Stream](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Aplicações de transmissão em fluxo do Spark tem de aguardar uma fração de segundo para recolher cada *micro-lotes* de eventos antes de enviar esse lote para processamento. Por outro lado, um aplicativo controlado por evento processa cada evento imediatamente. Latência de transmissão em fluxo do Spark é, normalmente, em alguns segundos. Os benefícios da abordagem de micro-lotes são cálculos de agregação mais simples e de processamento de dados mais eficiente.

## <a name="introducing-the-dstream"></a>Apresentando o DStream

Transmissão em fluxo do Spark representa um fluxo contínuo de entrada de dados com um *stream discretizado* chamado um DStream. Um DStream pode ser criado a partir de origens de entrada, como os Hubs de eventos ou Kafka ou ao aplicar transformações em outro DStream.

Um DStream fornece uma camada de abstração para além dos dados de eventos não processados. 

Começar com um único evento, digamos que uma temperatura ler a partir de um termóstato ligado. Quando este evento chegar ao seu aplicativo de transmissão em fluxo do Spark, o evento é armazenado de forma confiável, onde são replicado em vários nós. Este tolerância a falhas garante que a falha de qualquer nó único não resultará na perda de seu evento. O núcleo do Spark utiliza uma estrutura de dados que distribui os dados em vários nós do cluster, onde cada nó mantém seus próprios dados na memória para um melhor desempenho geral. Essa estrutura de dados é chamada um *conjunto de dados distribuído resiliente* (RDD).

Cada RDD representa os eventos recolhidos ao longo de um período de tempo definido pelo utilizador chamado os *intervalo de lotes*. Como cada intervalo de lotes decorrido, é produzido um novo RDD que contém todos os dados a partir desse intervalo. O conjunto de RDDs são recolhidas para um DStream. Por exemplo, se o intervalo de lotes é longo de um segundo, o seu DStream emite um lote cada segundo RDD de um contentor que contém todos os dados ingeridos durante esse segundo. Ao processar o DStream, é apresentado o evento de temperatura de uma destas lotes. Uma aplicação de transmissão em fluxo do Spark processa os lotes que contêm os eventos e, por fim, age em dados armazenados em cada RDD.

![Exemplo DStream com eventos de temperatura](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Estrutura de um aplicativo de transmissão em fluxo do Spark

Um aplicativo de transmissão em fluxo do Spark é um aplicativo de execução longa que recebe dados de origens de ingestão, aplica-se de transformações para processar os dados e, em seguida, envia os dados para um ou mais destinos. A estrutura de um aplicativo de transmissão em fluxo do Spark tem uma parte estática e dinâmica parte. A parte estática define onde vêm os dados, o processamento para fazer nos dados e onde os resultados devem ir. A parte dinâmica está a executar a aplicação indefinidamente, à espera de um sinal de paragem.

Por exemplo, a seguinte aplicação simple recebe uma linha de texto através de um soquete TCP e conta o número de vezes que cada palavra aparece.

### <a name="define-the-application"></a>Definir a aplicação

A definição da aplicação lógica tem quatro etapas:

1. Crie um StreamingContext.
2. Crie um DStream a partir do StreamingContext.
3. Aplica transformações ao DStream.
4. Os resultados de saída.

Esta definição é estática e nenhum dado é processado até que o aplicativo é executado.

#### <a name="create-a-streamingcontext"></a>Criar um StreamingContext

Crie um StreamingContext partir o SparkContext que aponta para o cluster. Ao criar um StreamingContext, especifica o tamanho do lote em segundos, por exemplo:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Criar um DStream

Com a instância de StreamingContext, crie uma entrada DStream para a sua origem de entrada. Neste caso, a aplicação está a observar para a aparência dos novos ficheiros no armazenamento padrão ligado ao cluster do HDInsight.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Aplicar transformações

Implementar o processamento ao aplicar transformações no DStream. Esse aplicativo recebe uma linha de texto em vez do ficheiro, divide cada linha em palavras e, em seguida, utilize um padrão de map-reduce contar o número de vezes que cada palavra aparece.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Resultados de saída

Emita os resultados de transformação para os sistemas de destino através da aplicação de operações de saída. Neste caso, o resultado de cada execução através de computação é impresso na saída da consola.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Executar a aplicação

Inicie a aplicação de transmissão em fluxo e execute até que é recebido um sinal de terminação.

```
ssc.start()
ssc.awaitTermination()
```

Para obter detalhes sobre a API do Stream Spark, juntamente com as origens de eventos, transformações e as operações de saída suporta, consulte [Apache Spark Streaming guia de programação](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

O aplicativo de exemplo seguinte é autónomo, para que pode executá-lo dentro de um [bloco de notas do Jupyter](apache-spark-jupyter-notebook-kernels.md). Este exemplo cria uma origem de dados fictícios na classe DummySource que produz o valor de um contador e a hora atual em milissegundos a cada cinco segundos. Um novo objeto de StreamingContext tem um intervalo de lotes de 30 segundos. Sempre que é criado um lote, o aplicativo de transmissão em fluxo examina o RDD produzido, converte o RDD para um DataFrame do Spark e cria uma tabela temporária sobre o pacote de dados.

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

Aguarde cerca de 30 segundos depois de iniciar o aplicativo acima.  Em seguida, pode consultar o pacote de dados periodicamente para ver o conjunto atual de valores presentes no lote, por exemplo, utilizando esta consulta SQL:

```sql
%%sql
SELECT * FROM demo_numbers
```

A saída resultante é semelhante ao seguinte:

| valor | hora |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Existem seis valores, uma vez que o DummySource cria um valor de 5 em 5 segundos e o aplicativo emite um lote a cada 30 segundos.

## <a name="sliding-windows"></a>Windows deslizante

Para realizar cálculos de agregação com sua DStream durante um período de tempo, por exemplo, para obter uma temperatura média ao longo do último dois segundos, pode utilizar o *janela deslizante* operações incluídas com o Spark Streaming. Uma janela deslizante tem uma duração (o comprimento de janela) e o intervalo durante o qual o conteúdo da janela é avaliadas (o intervalo de slide).

Deslizante windows pode sobrepor-se, por exemplo, pode definir uma janela com um comprimento de dois segundos, o que sai de cada um segundo. Isso significa que sempre que executar um cálculo de agregação, a janela incluirá dados a partir de um último segundo da janela anterior, bem como quaisquer novos dados em próxima de um segundo.

![Janela inicial de exemplo com eventos de temperatura](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Janela de exemplo com eventos de temperatura após deslizante](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

O exemplo seguinte atualiza o código que usa o DummySource, para recolher os lotes para uma janela com uma duração de um minuto e um slide de um minuto.

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

Após o primeiro minuto, existem 12 entradas - seis entradas de cada um dos dois lotes recolhidos na janela.

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

As funções de janela deslizante disponíveis na API de transmissão em fluxo do Spark incluem a janela, countByWindow, reduceByWindow e countByValueAndWindow. Para obter detalhes sobre estas funções, consulte [transformações em DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Ponto de verificação

Para fornecer resiliência e tolerância a falhas, a transmissão em fluxo do Spark baseia-se no ponto de verificação para garantir que o processamento de fluxo pode continuar sem interrupções, mesmo diante de falhas de nó. No HDInsight, Spark cria pontos de verificação para o armazenamento durável (armazenamento do Azure ou armazenamento do Data Lake). Estes pontos de verificação armazenam os metadados sobre a aplicação de transmissão em fluxo, como a configuração, as operações definidas pela aplicação e qualquer lotes que foram colocados em fila, mas ainda não processados. Em alguns casos, os pontos de verificação também irão incluir a guardar os dados dos RDDs para reconstruir mais rapidamente o estado dos dados do que está presente no RDDs geridos pelo Spark.

## <a name="deploying-spark-streaming-applications"></a>Implementação de aplicações de transmissão em fluxo do Spark

Normalmente, criar uma aplicação de transmissão em fluxo do Spark de mensagens em fila localmente num ficheiro JAR e, em seguida, implementá-la ao Spark no HDInsight ao copiar o ficheiro JAR para o armazenamento de predefinido ligado ao seu cluster do HDInsight. Pode começar a sua aplicação com as APIs de REST do LIVY disponíveis do seu cluster através de uma operação POST. O corpo da POSTAGEM inclui um documento JSON que fornece o caminho para o JAR, o nome da classe cujo principal método define e executa a aplicação de transmissão em fluxo e, opcionalmente, os requisitos de recursos da tarefa (por exemplo, o número de executor, memória e núcleos) , e as definições de configuração requer que o código da aplicação.

![Implantando um aplicativo de transmissão em fluxo do Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Também pode ser verificado o estado de todos os aplicativos com um pedido GET relativamente um ponto de extremidade do LIVY. Por fim, pode terminar um aplicativo em execução ao emitir um pedido de eliminação relativamente ao ponto de final do LIVY. Para obter detalhes sobre a API do LIVY, consulte [tarefas remotas com Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Passos Seguintes

* [Criar um cluster do Apache Spark no HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Guia de programação de transmissão em fluxo do Apache Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Iniciar tarefas do Apache Spark remotamente com o Apache LIVY](apache-spark-livy-rest-interface.md)
