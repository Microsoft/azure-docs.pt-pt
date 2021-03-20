---
title: Empregos de streaming de faíscas altamente disponíveis em YARN - Azure HDInsight
description: Como configurar o Apache Spark Streaming para um cenário de alta disponibilidade em Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 3eb761a793c41c2e2cc2cb952e4fb9f241b41ab6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929698"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Criar empregos de streaming Apache Spark de alta disponibilidade com YARN

[Faísca Apache](https://spark.apache.org/) O streaming permite-lhe implementar aplicações escaláveis, de alta produção e tolerantes a falhas para o processamento de fluxos de dados. Pode ligar aplicações Spark Streaming num cluster HDInsight Spark a diferentes tipos de fontes de dados, tais como Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume,](https://flume.apache.org/)Twitter, [ZeroMQ,](http://zeromq.org/)tomadas TCP em bruto ou monitorizando o sistema de ficheiros [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) para alterações. O Spark Streaming suporta a tolerância à falha com a garantia de que qualquer evento é processado exatamente uma vez, mesmo com uma falha no nó.

O Spark Streaming cria trabalhos de longa duração durante os quais é capaz de aplicar transformações aos dados e, em seguida, empurrar os resultados para sistemas de ficheiros, bases de dados, dashboards e consola. O Spark Streaming processa micro-lotes de dados, recolhendo primeiro um lote de eventos ao longo de um intervalo de tempo definido. Em seguida, o lote é enviado para processamento e saída. Os intervalos de tempo do lote são tipicamente definidos em frações de segundo.

![Transmissão em fluxo do Spark](./media/apache-spark-streaming-high-availability/apache-spark-streaming.png)

## <a name="dstreams"></a>DStreams

O Streaming de Faíscas representa um fluxo contínuo de dados utilizando um *fluxo discreto* (DStream). Este DStream pode ser criado a partir de fontes de entrada como Event Hubs ou Kafka, ou aplicando transformações em outro DStream. Quando um evento chega à sua aplicação Spark Streaming, o evento é armazenado de forma fiável. Ou seja, os dados do evento são replicados para que vários nós tenham uma cópia do mesmo. Isto garante que a falha de qualquer nó não resultará na perda do seu evento.

O núcleo spark utiliza *conjuntos de dados distribuídos resilientes* (RDDs). Os RDDs distribuem dados por vários nós no cluster, onde cada nó geralmente mantém os seus dados completamente na memória para melhor desempenho. Cada RDD representa eventos recolhidos durante um intervalo de lote. Quando o intervalo do lote decorre, o Spark Streaming produz um novo RDD contendo todos os dados nesse intervalo. Este conjunto contínuo de RDDs é recolhido num DStream. Uma aplicação Spark Streaming processa os dados armazenados no RDD de cada lote.

![Faísca DStream](./media/apache-spark-streaming-high-availability/apache-spark-dstream.png)

## <a name="spark-structured-streaming-jobs"></a>Trabalhos de streaming estruturados de faíscas

O Spark Structured Streaming foi introduzido no Spark 2.0 como um motor analítico para utilização em dados estruturados de streaming. O Spark Structured Streaming utiliza as APIs do motor de lote SparkSQL. Tal como acontece com o Spark Streaming, o Spark Structured Streaming executa os seus cálculos ao longo de micro-lotes de dados que chegam continuamente. O Streaming Estruturado de Faísca representa um fluxo de dados como uma Tabela de Entrada com linhas ilimitadas. Ou seja, a Tabela de Entradas continua a crescer à medida que chegam novos dados. Esta Tabela de Entrada é continuamente processada por uma consulta de longa duração e os resultados são escritos para uma Tabela de Saída.

![Fluxo Estruturado de Faísca](./media/apache-spark-streaming-high-availability/structured-streaming.png)

No Streaming Estruturado, os dados chegam ao sistema e são imediatamente ingeridos na Tabela de Entrada. Escreve consultas que realizam operações contra esta Tabela de Entradas. A produção de consulta produz outra tabela, chamada tabela de resultados. A Tabela de Resultados contém resultados da sua consulta, a partir da qual extrai dados para enviar para uma loja de dados externa tal base de dados relacional. O *intervalo do gatilho* define o tempo para quando os dados são tratados a partir da Tabela de Entrada. Por predefinição, o Streaming Estruturado processa os dados assim que chega. No entanto, também pode configurar o gatilho para ser executado num intervalo mais longo, para que os dados de streaming são processados em lotes baseados no tempo. Os dados na Tabela de Resultados podem ser atualizados cada vez que há novos dados de modo a incluir todos os dados de saída desde o início da consulta de streaming (*modo completo),* ou pode apenas conter apenas os dados que são novos desde a última vez que a consulta foi processada *(modo apêndice).*

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Criar trabalhos de streaming de faíscas tolerantes a falhas

Para criar um ambiente altamente disponível para os seus trabalhos de Streaming Spark, comece por codificar os seus trabalhos individuais para recuperação em caso de insucesso. Tais trabalhos de autorrecuperação são tolerantes a falhas.

Os RDDs têm várias propriedades que ajudam trabalhos altamente disponíveis e tolerantes a spark streaming:

* Os lotes de dados de entrada armazenados em RDDs como DStream são automaticamente replicados na memória para tolerância a falhas.
* Os dados perdidos por falta de trabalhadores podem ser recompreensados a partir de dados de entrada replicados em diferentes trabalhadores, desde que esses nós operários estejam disponíveis.
* A recuperação rápida de falhas pode ocorrer dentro de um segundo, uma vez que a recuperação de falhas/vagabundos ocorre através da computação na memória.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Exatamente uma vez semântica com Spark Streaming

Para criar uma aplicação que processe cada evento uma vez (e apenas uma vez), considere como todos os pontos de falha do sistema reiniciam depois de ter um problema e como pode evitar a perda de dados. Exatamente uma vez que a semântica requer que nenhum dado seja perdido em qualquer ponto, e que o processamento de mensagens é reiniciável, independentemente do local onde a falha ocorre. Consulte [criar trabalhos de streaming de faíscas com um processamento de eventos exatamente uma vez.](apache-spark-streaming-exactly-once.md)

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark Streaming e Apache Hadoop YARN

No HDInsight, o trabalho de cluster é coordenado por *Mais Um Negociador de Recursos* (YARN). Projetar alta disponibilidade para spark streaming inclui técnicas para streaming de faíscas, e também para componentes DE YARN.  Uma configuração de exemplo utilizando o YARN é mostrada abaixo.

![Arquitetura YARN](./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png)

As secções seguintes descrevem considerações de design para esta configuração.

### <a name="plan-for-failures"></a>Plano para falhas

Para criar uma configuração YARN para alta disponibilidade, deverá planear uma possível falha do executor ou do controlador. Alguns trabalhos de Streaming spark também incluem requisitos de garantia de dados que precisam de configuração e configuração adicionais. Por exemplo, uma aplicação de streaming pode ter um requisito de negócio para uma garantia de perda de dados zero, apesar de qualquer erro que ocorra no sistema de streaming de hospedagem ou no cluster HDInsight.

Se um **executor** falhar, as suas tarefas e recetores são reiniciados automaticamente pela Spark, pelo que não é necessária qualquer alteração de configuração.

No entanto, se um **condutor** falhar, todos os seus executores associados falham e todos os blocos recebidos e resultados de cálculo são perdidos. Para recuperar de uma falha do condutor, utilize *o checkpoint DStream* como descrito no [Create Spark Streaming jobs com exatamente uma vez o processamento de eventos](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). O checkpointing DStream guarda periodicamente o *gráfico acíclico direcionado* (DAG) dos DStreams para o armazenamento tolerante a falhas, como o Azure Storage.  O checkpoint permite que o Streaming Estruturado de Faísca reinicie o controlador falhado a partir da informação do ponto de verificação.  Este controlador reinicia o lançamento de novos executores e também reinicia os recetores.

Para recuperar os condutores com verificação DStream:

* Configure o reinício automático do controlador automático no YARN com a definição de configuração `yarn.resourcemanager.am.max-attempts` .
* Desave um diretório de pontos de verificação num sistema de ficheiros compatíveis com HDFS com `streamingContext.checkpoint(hdfsDirectory)` .
* Reestruturar o código-fonte para utilizar postos de controlo para recuperação, por exemplo:

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

* Configure a recuperação de dados perdida, permitindo o registo de write-ahead (WAL) com `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")` , e desative a replicação na memória para a entrada DStreams com `StorageLevel.MEMORY_AND_DISK_SER` .

Para resumir, utilizando o checkpoint + RECETORES WAL + confiáveis, poderá fornecer "pelo menos uma vez" a recuperação de dados:

* Exatamente uma vez, desde que os dados recebidos não sejam perdidos e as saídas sejam idempotentes ou transacionais.
* Exatamente uma vez, com a nova abordagem Kafka Direct, que usa Kafka como um log replicado, em vez de usar recetores ou WALs.

### <a name="typical-concerns-for-high-availability"></a>Preocupações típicas com a alta disponibilidade

* É mais difícil monitorizar trabalhos de streaming do que trabalhos em lotes. Os trabalhos de streaming de faíscas são normalmente de longa duração, e o YARN não agrega registos até que um trabalho termine.  Os pontos de verificação de faíscas perdem-se durante a aplicação ou atualizações spark, e terás de limpar o diretório de controlo durante uma atualização.

* Configure o modo de cluster YARN para executar condutores mesmo que um cliente falhe. Para configurar o reinício automático para os condutores:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark e a Spark Streaming UI têm um sistema de métricas configuráveis. Também pode utilizar bibliotecas adicionais, como a Graphite/Grafana, para descarregar métricas do dashboard como "registos num processados", "utilização de memória/GC nos executores & do condutor", "atraso total", "utilização do cluster", etc. Na versão de Streaming Estruturado 2.1 ou superior, pode utilizar `StreamingQueryListener` para recolher métricas adicionais.

* Devias segmentar empregos de longa duração.  Quando um pedido de Streaming de Faísca é submetido ao cluster, a fila YARN onde o trabalho funciona deve ser definida. Você pode usar um [Programador de Capacidade YARN](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) para enviar trabalhos de longa duração para separar filas.

* Desligue graciosamente a sua aplicação de streaming. Se as suas compensações forem conhecidas e todo o estado da aplicação for armazenado externamente, então pode parar programáticamente a sua aplicação de streaming no local apropriado. Uma técnica é usar "ganchos de rosca" em Faísca, verificando uma bandeira externa a cada *segundo.* Também pode utilizar um *ficheiro marcador* que é criado no HDFS ao iniciar a aplicação e, em seguida, removido quando pretende parar. Para uma abordagem de ficheiro marcador, utilize um fio separado na sua aplicação Spark que chame código semelhante a este:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do fluxo de faíscas de Apache Spark](apache-spark-streaming-overview.md)
* [Crie empregos em streaming de Faíscas Apache Com processamento de eventos exatamente uma vez](apache-spark-streaming-exactly-once.md)
* [Empregos de streaming apache spark de longa duração em YARN](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Streaming Estruturado: Semântica Tolerante a Falhas](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Streams discretizados: um modelo Fault-Tolerant para processamento de fluxo escalável](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
