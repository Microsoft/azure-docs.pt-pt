---
title: Criar trabalhos de streaming do Spark altamente disponíveis no YARN – Azure HDInsight
description: Como configurar o streaming de Apache Spark para um cenário de alta disponibilidade no Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/26/2018
ms.openlocfilehash: dcd9095a1e5010a3d0dd5ea7ad884e36e24c7c1d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814007"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Criar trabalhos de streaming de alta disponibilidade Apache Spark com o YARN

[Apache Spark](https://spark.apache.org/) O streaming permite que você implemente aplicativos escalonáveis, de alta taxa de transferência e tolerante a falhas para processamento de fluxos de dados. Você pode conectar aplicativos do Spark streaming em um cluster HDInsight Spark a uma variedade de fontes de dados, como hubs de eventos do Azure, Hub IoT do Azure, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), soquetes TCP brutos ou monitorando o [ Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) o sistema de arquivos HDFS para alterações. O streaming do Spark dá suporte à tolerância a falhas com a garantia de que qualquer evento determinado é processado exatamente uma vez, mesmo com uma falha de nó.

O streaming do Spark cria trabalhos de longa execução durante os quais você pode aplicar transformações aos dados e, em seguida, enviar por push os resultados para sistemas de sistema, bancos de dados, painéis e o console. O streaming do Spark processa micro lotes de dados, coletando primeiro um lote de eventos em um intervalo de tempo definido. Em seguida, esse lote é enviado para processamento e saída. Os intervalos de tempo de lote geralmente são definidos em frações de um segundo.

![Streaming do Spark](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>DStreams

O streaming do Spark representa um fluxo contínuo de dados usando um *fluxo de discretizado* (DStream). Esse DStream pode ser criado a partir de fontes de entrada, como hubs de eventos ou Kafka, ou pela aplicação de transformações em outro DStream. Quando um evento chega ao seu aplicativo Spark streaming, o evento é armazenado de forma confiável. Ou seja, os dados do evento são replicados para que vários nós tenham uma cópia dele. Isso garante que a falha de um único nó não resultará na perda do evento.

O núcleo do Spark usa RDDs (conjuntos de fonte *distribuídos resilientes* ). O RDDs distribui dados em vários nós no cluster, em que cada nó geralmente mantém seus dados completamente na memória para obter o melhor desempenho. Cada RDD representa eventos coletados em um intervalo de lote. Quando o intervalo de lote expira, o Spark streaming produz um novo RDD contendo todos os dados nesse intervalo. Esse conjunto contínuo de RDDs é coletado em um DStream. Um aplicativo de streaming do Spark processa os dados armazenados em cada RDD do lote.

![DStream Spark](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>Trabalhos de streaming estruturado do Spark

O streaming estruturado do Spark foi introduzido no Spark 2,0 como um mecanismo analítico para uso em streaming de dados estruturados. O streaming estruturado do Spark usa as APIs do mecanismo de envio em lote do SparkSQL. Assim como acontece com o streaming do Spark, o Spark Structured streaming executa seus cálculos por meio de micro lotes de dados que chegam continuamente. O streaming estruturado do Spark representa um fluxo de dados como uma tabela de entrada com linhas ilimitadas. Ou seja, a tabela de entrada continua crescendo conforme novos dados chegam. Essa tabela de entrada é processada continuamente por uma consulta de execução longa e os resultados são gravados em uma tabela de saída.

![Streaming estruturado do Spark](./media/apache-spark-streaming-high-availability/structured-streaming.png)

No streaming estruturado, os dados chegam ao sistema e são imediatamente incluídos na tabela de entrada. Você escreve consultas que executam operações nessa tabela de entrada. A saída da consulta produz outra tabela, chamada tabela de resultados. A tabela de resultados contém resultados de sua consulta, a partir da qual você desenha dados para enviar para um datastore externo, como um banco de dado relacional. O *intervalo de gatilho* define o tempo para quando os dados são processados da tabela de entrada. Por padrão, o streaming estruturado processa os dados assim que eles chegam. No entanto, você também pode configurar o gatilho para ser executado em um intervalo mais longo, de modo que os dados de streaming sejam processados em lotes baseados em tempo. Os dados na tabela de resultados podem ser completamente atualizados sempre que houver novos dados, de modo que eles incluam todos os dados de saída desde que a consulta de streaming começou (*modo completo*) ou que pode conter apenas os dados que são novos desde a última vez que a consulta foi proc ssed (*modo de acréscimo*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Criar trabalhos de streaming do Spark com tolerância a falhas

Para criar um ambiente altamente disponível para seus trabalhos de streaming do Spark, comece codificando seus trabalhos individuais para recuperação em caso de falha. Esses trabalhos de recuperação automática são tolerantes a falhas.

RDDs têm várias propriedades que auxiliam trabalhos de streaming do Spark altamente disponíveis e tolerantes a falhas:

* Os lotes de dados de entrada armazenados em RDDs como um DStream são replicados automaticamente na memória para tolerância a falhas.
* Os dados perdidos devido a uma falha de trabalho podem ser recalculados a partir de dados de entrada replicados em diferentes trabalhadores, desde que esses nós de trabalho estejam disponíveis.
* A recuperação rápida de falhas pode ocorrer em um segundo, pois a recuperação de falhas/Stragglers ocorre por meio de computação na memória.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Semântica de exatamente uma vez com o streaming do Spark

Para criar um aplicativo que processa cada evento uma vez (e apenas uma vez), considere como todos os pontos do sistema de falha são reiniciados depois de ter um problema e como você pode evitar a perda de dados. Semânticas exatamente uma vez exigem que nenhum dado seja perdido em qualquer ponto e que o processamento de mensagens seja reinicializável, independentemente de onde a falha ocorra. Consulte [criar trabalhos de streaming do Spark com processamento de eventos exatamente uma vez](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Streaming do Spark e Apache Hadoop YARN

No HDInsight, o trabalho do cluster é coordenado por *outro negociador de recursos* (Yarn). A criação de alta disponibilidade para o Spark streaming inclui técnicas para streaming do Spark e também para componentes YARN.  Uma configuração de exemplo usando YARN é mostrada abaixo. 

![Arquitetura do YARN](./media/apache-spark-streaming-high-availability/yarn-arch.png)

As seções a seguir descrevem as considerações de design para essa configuração.

### <a name="plan-for-failures"></a>Planejar falhas

Para criar uma configuração de YARN para alta disponibilidade, você deve planejar um possível executor ou falha de driver. Alguns trabalhos de streaming do Spark também incluem requisitos de garantia de dados que precisam de configuração adicional e configuração. Por exemplo, um aplicativo de streaming pode ter um requisito de negócios para uma garantia de perda de dados zero, apesar de qualquer erro que ocorra no sistema de streaming de hospedagem ou no cluster HDInsight.

Se um **executor** falhar, suas tarefas e receptores serão reiniciados automaticamente pelo Spark, portanto, não há nenhuma alteração de configuração necessária.

No entanto, se um **Driver** falhar, todos os executores associados falharão e todos os blocos recebidos e os resultados de computação serão perdidos. Para se recuperar de uma falha de driver, use o *ponto de verificação DStream* , conforme descrito em [criar trabalhos de streaming do Spark com processamento de eventos exatamente uma vez](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). O ponto de verificação DStream salva periodicamente o DAG ( *grafo direcionado acíclico* ) de DStreams para o armazenamento tolerante a falhas, como o armazenamento do Azure.  O ponto de verificação permite o streaming estruturado do Spark para reiniciar o driver com falha a partir das informações do ponto de verificação.  Essa reinicialização de driver inicia novos executores e também reinicia os receptores.

Para recuperar drivers com o ponto de verificação DStream:

* Configure a reinicialização automática de driver em YARN `yarn.resourcemanager.am.max-attempts`com a definição de configuração.
* Defina um diretório de ponto de verificação em um sistema de arquivos `streamingContext.checkpoint(hdfsDirectory)`compatível com HDFS com.
* Reestruture o código-fonte para usar pontos de verificação para recuperação, por exemplo:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Configure a recuperação de dados perdidos habilitando o log write-ahead (Wal `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`) com e desabilite a replicação na memória para a `StorageLevel.MEMORY_AND_DISK_SER`entrada DStreams com.

Para resumir, usando o ponto de verificação + WAL + destinatários confiáveis, você poderá fornecer a recuperação de dados "pelo menos uma vez":

* Exatamente uma vez, desde que os dados recebidos não sejam perdidos e as saídas sejam idempotentes ou transacionais.
* Exatamente uma vez, com a nova abordagem direta do Kafka que usa Kafka como um log replicado, em vez de usar receptores ou WALs.

### <a name="typical-concerns-for-high-availability"></a>Preocupações típicas para alta disponibilidade

* É mais difícil monitorar trabalhos de streaming do que trabalhos em lotes. Os trabalhos de streaming do Spark normalmente são de execução longa e YARN não agrega logs até que um trabalho seja concluído.  Os pontos de verificação do Spark são perdidos durante as atualizações do aplicativo ou do Spark e você precisará limpar o diretório do ponto de verificação durante uma atualização.

* Configure o modo de cluster YARN para executar drivers, mesmo se um cliente falhar. Para configurar a reinicialização automática para drivers:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* O Spark e a interface do usuário de streaming do Spark têm um sistema de métricas configurável. Você também pode usar bibliotecas adicionais, como Graphite/Grafana para baixar métricas do painel, como ' número de registros processados ', ' uso da memória/GC no driver & executores ', ' atraso total ', ' utilização do cluster ' e assim por diante. Na versão de streaming estruturada 2,1 ou superior, você `StreamingQueryListener` pode usar o para coletar métricas adicionais.

* Você deve segmentar trabalhos de execução longa.  Quando um aplicativo de streaming do Spark é enviado ao cluster, a fila YARN em que o trabalho é executado deve ser definida. Você pode usar um [Agendador de capacidade yarn](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) para enviar trabalhos de longa execução para separar filas.

* Desligue seu aplicativo de streaming normalmente. Se os deslocamentos forem conhecidos e todo o estado do aplicativo for armazenado externamente, você poderá interromper programaticamente o aplicativo de streaming no local apropriado. Uma técnica é usar "ganchos de thread" no Spark, verificando um sinalizador externo a cada *n* segundos. Você também pode usar um *arquivo de marcador* que é criado no HDFS ao iniciar o aplicativo e, em seguida, removido quando você deseja parar. Para uma abordagem de arquivo de marcador, use um thread separado em seu aplicativo Spark que chama um código semelhante a este:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Passos seguintes

* [Visão geral de streaming de Apache Spark](apache-spark-streaming-overview.md)
* [Criar Apache Spark trabalhos de streaming com processamento de eventos exatamente uma vez](apache-spark-streaming-exactly-once.md)
* [Trabalhos de streaming de Apache Spark de execução longa em YARN](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) 
* [Streaming estruturado: Semântica tolerante a falhas](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Fluxos de discretizado: Um modelo tolerante a falhas para processamento de fluxo escalonável](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
