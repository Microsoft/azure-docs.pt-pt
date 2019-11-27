---
title: Streaming do Spark no Azure HDInsight
description: Como usar Apache Spark aplicativos de streaming em clusters Spark do HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: 521d72642a27995d096402a4ca0e4af632b0788c
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406269"
---
# <a name="overview-of-apache-spark-streaming"></a>Visão geral de Apache Spark streaming

[Apache Spark](https://spark.apache.org/) O streaming fornece processamento de fluxo de dados em clusters do HDInsight Spark, com uma garantia de que qualquer evento de entrada seja processado exatamente uma vez, mesmo se ocorrer uma falha de nó. Um fluxo do Spark é um trabalho de execução longa que recebe dados de entrada de uma ampla variedade de fontes, incluindo hubs de eventos do Azure, um hub IoT do Azure, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), soquetes TCP brutos ou monitoramento Apache Hadoop sistemas de dados [yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) . Ao contrário de um processo exclusivamente controlado por eventos, um fluxo do Spark gera dados de entrada em janelas de tempo, como uma fatia de 2 segundos e, em seguida, transforma cada lote de dados usando operações de mapear, reduzir, unir e extrair. Em seguida, o fluxo do Spark grava os dados transformados em sistemas de dados, bancos de dados, painéis e no console.

![Processamento de fluxo com o HDInsight e o streaming do Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Os aplicativos de streaming do Spark devem aguardar uma fração de um segundo para coletar cada *micro lote* de eventos antes de enviar esse lote para processamento. Por outro lado, um aplicativo orientado por eventos processa cada evento imediatamente. A latência de streaming do Spark normalmente está abaixo de alguns segundos. Os benefícios da abordagem de micro batch são processamento de dados mais eficiente e cálculos de agregação mais simples.

## <a name="introducing-the-dstream"></a>Apresentando o DStream

O streaming do Spark representa um fluxo contínuo de dados de entrada usando um *fluxo de discretizado* chamado DStream. Um DStream pode ser criado de fontes de entrada, como hubs de eventos ou Kafka, ou aplicando transformações em outro DStream.

Um DStream fornece uma camada de abstração sobre os dados de evento brutos.

Comece com um único evento, digamos uma leitura de temperatura de um termostato conectado. Quando esse evento chega ao seu aplicativo Spark streaming, o evento é armazenado de forma confiável, onde é replicado em vários nós. Essa tolerância a falhas garante que a falha de um único nó não resultará na perda do evento. O núcleo do Spark usa uma estrutura de dados que distribui dados em vários nós no cluster, em que cada nó geralmente mantém seus próprios dados na memória para obter o melhor desempenho. Essa estrutura de dados é chamada de um conjunto de RDD ( *resiliente distribuído* ).

Cada RDD representa os eventos coletados em um período de tempo definido pelo usuário, chamado *intervalo de lote*. À medida que cada intervalo de lote expira, é produzido um novo RDD que contém todos os dados desse intervalo. O conjunto contínuo de RDDs é coletado em um DStream. Por exemplo, se o intervalo de lote for um segundo, o DStream emitirá um lote a cada segundo contendo um RDD que contém todos os dados ingeridos durante esse segundo. Ao processar o DStream, o evento de temperatura aparece em um desses lotes. Um aplicativo Spark streaming processa os lotes que contêm os eventos e, por fim, atua nos dados armazenados em cada RDD.

![Exemplo de DStream com eventos de temperatura](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Estrutura de um aplicativo Spark streaming

Um aplicativo Spark streaming é um aplicativo de execução longa que recebe dados de fontes de ingestão, aplica transformações para processar os dados e, em seguida, envia os dados para um ou mais destinos. A estrutura de um aplicativo do Spark streaming tem uma parte estática e uma parte dinâmica. A parte estática define de onde vêm os dados, o processamento a ser feito nos dados e onde os resultados devem ir. A parte dinâmica está executando o aplicativo indefinidamente, aguardando um sinal de parada.

Por exemplo, o aplicativo simples a seguir recebe uma linha de texto em um soquete TCP e conta o número de vezes que cada palavra é exibida.

### <a name="define-the-application"></a>Definir o aplicativo

A definição da lógica do aplicativo tem quatro etapas:

1. Crie um StreamingContext.
2. Crie um DStream a partir do StreamingContext.
3. Aplicar transformações ao DStream.
4. Gere os resultados.

Essa definição é estática e nenhum dado é processado até que você execute o aplicativo.

#### <a name="create-a-streamingcontext"></a>Criar um StreamingContext

Crie um StreamingContext do SparkContext que aponte para o cluster. Ao criar um StreamingContext, você especifica o tamanho do lote em segundos, por exemplo:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Criar um DStream

Com a instância StreamingContext, crie um DStream de entrada para sua fonte de entrada. Nesse caso, o aplicativo está observando a aparência dos novos arquivos no armazenamento padrão anexado ao cluster HDInsight.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Aplicar transformações

Você implementa o processamento aplicando transformações no DStream. Esse aplicativo recebe uma linha de texto por vez do arquivo, divide cada linha em palavras e, em seguida, usa um padrão de redução de mapa para contar o número de vezes que cada palavra é exibida.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Resultados da saída

Envie por push os resultados da transformação para os sistemas de destino aplicando operações de saída. Nesse caso, o resultado de cada execução por meio do cálculo é impresso na saída do console.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Executar a aplicação

Inicie o aplicativo de streaming e execute até que um sinal de encerramento seja recebido.

```
ssc.start()
ssc.awaitTermination()
```

Para obter detalhes sobre a API de fluxo do Spark, juntamente com as fontes de evento, transformações e operações de saída às quais ele dá suporte, consulte [Guia de programação de Apache Spark streaming](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

O aplicativo de exemplo a seguir é independente, portanto, você pode executá-lo dentro de um [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md). Este exemplo cria uma fonte de dados fictícia na classe fictício, que gera o valor de um contador e a hora atual em milissegundos a cada cinco segundos. Um novo objeto StreamingContext tem um intervalo de lote de 30 segundos. Toda vez que um lote é criado, o aplicativo de streaming examina o RDD produzido, converte o RDD em um dataframe do Spark e cria uma tabela temporária sobre o dataframe.

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

Aguarde cerca de 30 segundos depois de iniciar o aplicativo acima.  Em seguida, você pode consultar o dataframe periodicamente para ver o conjunto atual de valores presentes no lote, por exemplo, usando esta consulta SQL:

```sql
%%sql
SELECT * FROM demo_numbers
```

A saída resultante é semelhante ao seguinte:

| valor | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Há seis valores, pois o fictício cria um valor a cada 5 segundos e o aplicativo emite um lote a cada 30 segundos.

## <a name="sliding-windows"></a>Janelas deslizantes

Para executar cálculos agregados em seu DStream durante um período de tempo, por exemplo, para obter uma temperatura média nos últimos dois segundos, você pode usar as operações de *janela deslizante* incluídas no Spark streaming. Uma janela deslizante tem uma duração (o comprimento da janela) e o intervalo durante o qual o conteúdo da janela é avaliado (o intervalo de slides).

As janelas deslizantes podem se sobrepor, por exemplo, você pode definir uma janela com um comprimento de dois segundos, que deslize os slides a cada um segundo. Isso significa que sempre que você executar um cálculo de agregação, a janela incluirá dados do último segundo da janela anterior, bem como quaisquer novos dados no próximo segundo.

![Exemplo de janela inicial com eventos de temperatura](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Janela de exemplo com eventos de temperatura após deslizar](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

O exemplo a seguir atualiza o código que usa o fictício, para coletar os lotes em uma janela com uma duração de um minuto e um slide de um minuto.

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

Após o primeiro minuto, há 12 entradas-seis entradas de cada um dos dois lotes coletados na janela.

| valor | time |
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

As funções de janela deslizante disponíveis na API de streaming do Spark incluem Window, countByWindow, reduceByWindow e countByValueAndWindow. Para obter detalhes sobre essas funções, consulte [transformações em DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Ponto de verificação

Para fornecer resiliência e tolerância a falhas, o Spark streaming depende do ponto de verificação para garantir que o processamento de fluxo possa continuar sem interrupções, mesmo diante de falhas de nó. No HDInsight, o Spark cria pontos de verificação para o armazenamento durável (armazenamento do Azure ou Data Lake Storage). Esses pontos de verificação armazenam os metadados sobre o aplicativo de streaming, como a configuração, as operações definidas pelo aplicativo e todos os lotes que foram enfileirados, mas ainda não foram processados. Em alguns casos, os pontos de verificação também incluirão salvar os dados no RDDs para reconstruir mais rapidamente o estado dos dados a partir do que está presente no RDDs gerenciado pelo Spark.

## <a name="deploying-spark-streaming-applications"></a>Implantando aplicativos Spark streaming

Normalmente, você cria um aplicativo Spark streaming localmente em um arquivo JAR e, em seguida, implanta-o no Spark no HDInsight copiando o arquivo JAR para o armazenamento padrão anexado ao seu cluster HDInsight. Você pode iniciar seu aplicativo com as APIs REST do LIVY disponíveis no cluster usando uma operação POST. O corpo da POSTAgem inclui um documento JSON que fornece o caminho para o JAR, o nome da classe cujo método principal define e executa o aplicativo de streaming e, opcionalmente, os requisitos de recurso do trabalho (como o número de executores, memória e núcleos) e quaisquer definições de configuração que o código do aplicativo exigir.

![Implantando um aplicativo Spark streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

O status de todos os aplicativos também pode ser verificado com uma solicitação GET em relação a um ponto de extremidade LIVY. Por fim, você pode encerrar um aplicativo em execução emitindo uma solicitação de exclusão no ponto de extremidade LIVY. Para obter detalhes sobre a API do LIVY, consulte [trabalhos remotos com o Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar um cluster Apache Spark no HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Guia de programação de Apache Spark streaming](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Iniciar Apache Spark trabalhos remotamente com o Apache LIVY](apache-spark-livy-rest-interface.md)
