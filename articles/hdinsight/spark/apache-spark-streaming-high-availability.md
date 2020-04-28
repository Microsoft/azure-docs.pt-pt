---
title: Empregos altamente disponíveis em Spark Streaming em YARN - Azure HDInsight
description: Como configurar o Apache Spark Streaming para um cenário de alta disponibilidade no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: ac51b77e1ffc2b476b0a73dac9b6917552a86ce4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74807158"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Criar trabalhos de streaming apache de alta disponibilidade com ARN

[Faísca Apache](https://spark.apache.org/) O streaming permite-lhe implementar aplicações escaláveis, de alta adesão e tolerantes a falhas para o processamento de fluxos de dados. Pode ligar aplicações spark streaming num cluster HDInsight Spark a diferentes tipos de fontes de dados, tais como Hubs de Eventos Azure, Hub Azure IoT, [Apache Kafka,](https://kafka.apache.org/) [Apache Flume,](https://flume.apache.org/)Twitter, [ZeroMQ,](http://zeromq.org/)tomadas TCP cruas ou monitorizando o sistema de ficheiros [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) para alterações. O Spark Streaming suporta a tolerância à falha com a garantia de que qualquer evento é processado exatamente uma vez, mesmo com uma falha no nó.

O Spark Streaming cria trabalhos de longa duração durante os quais é supreende as transformações nos dados e, em seguida, empurra os resultados para sistemas de ficheiros, bases de dados, dashboards e a consola. O Spark Streaming processa micro-lotes de dados, recolhendo primeiro um lote de eventos num intervalo de tempo definido. Em seguida, o lote é enviado para processamento e saída. Os intervalos de tempo do lote são tipicamente definidos em frações de segundo.

![Streaming de faíscas](./media/apache-spark-streaming-high-availability/apache-spark-streaming.png)

## <a name="dstreams"></a>DStreams

O Spark Streaming representa um fluxo contínuo de dados utilizando um *fluxo discreto* (DStream). Este DStream pode ser criado a partir de fontes de entrada como Event Hubs ou Kafka, ou aplicando transformações em outro DStream. Quando um evento chega à sua aplicação Spark Streaming, o evento é armazenado de forma fiável. Ou seja, os dados do evento são replicados para que vários nós tenham uma cópia. Isto garante que o fracasso de qualquer nó não resultará na perda do seu evento.

O núcleo spark utiliza conjuntos de *dados distribuídos resilientes* (RDDs). Os RDDs distribuem dados por vários nós do cluster, onde cada nó geralmente mantém os seus dados completamente em memória para um melhor desempenho. Cada RDD representa eventos recolhidos ao longo de um intervalo de lote. Quando o intervalo do lote decorrido, o Spark Streaming produz um novo RDD contendo todos os dados nesse intervalo. Este conjunto contínuo de RDDs é recolhido num DStream. Uma aplicação Spark Streaming processa os dados armazenados no RDD de cada lote.

![DStream de faísca](./media/apache-spark-streaming-high-availability/apache-spark-dstream.png)

## <a name="spark-structured-streaming-jobs"></a>Trabalhos de streaming estruturados de faíscas

O Spark Structured Streaming foi introduzido em Spark 2.0 como um motor analítico para utilização em dados estruturados em streaming. O Spark Structured Streaming utiliza as APIs do motor de lotação SparkSQL. Tal como acontece com o Spark Streaming, o Spark Structured Streaming executa as suas computações sobre microlotes de dados que chegam continuamente. O Spark Structured Streaming representa um fluxo de dados como uma Tabela de Entrada com linhas ilimitadas. Ou seja, a Tabela de Entrada continua a crescer à medida que novos dados chegam. Esta tabela de entrada é continuamente processada por uma consulta de longa duração, e os resultados são escritos para uma Tabela de Saída.

![Fluxo estruturado de faíscas](./media/apache-spark-streaming-high-availability/structured-streaming.png)

No Streaming Estruturado, os dados chegam ao sistema e são imediatamente ingeridos na Tabela de Entrada. Escreve consultas que realizam operações contra esta tabela de entrada. A saída de consulta dá outra tabela, chamada Tabela de Resultados. A Tabela de Resultados contém resultados da sua consulta, a partir da qual extrai dados para enviar para uma datastore externa tal base de dados relacional. O intervalo do *gatilho* define o tempo para quando os dados são processados a partir da Tabela de Entrada. Por padrão, o Streaming Estruturado processa os dados assim que chega. No entanto, também pode configurar o gatilho para ser executado num intervalo mais longo, para que os dados de streaming sejam processados em lotes baseados no tempo. Os dados constantes da Tabela de Resultados podem ser atualizados cada vez que houver novos dados para que incluam todos os dados de saída desde o início da consulta de streaming (*modo completo),* ou só podem conter apenas os dados que são novos desde a última vez que a consulta foi processada *(modo apêndice).*

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Criar empregos de streaming de faíscas tolerantes a falhas

Para criar um ambiente altamente disponível para os seus trabalhos de Streaming spark, comece por codificar os seus empregos individuais para recuperação em caso de falha. Tais trabalhos de autorrecuperação são tolerantes a falhas.

Os RDDs têm várias propriedades que ajudam a trabalhar altamente disponível e tolerante ao Spark Streaming:

* Os lotes de dados de entrada armazenados em RDDs como DStream são automaticamente replicados na memória para a tolerância a falhas.
* Os dados perdidos devido à insuficiência dos trabalhadores podem ser recalculados a partir de dados de entrada replicados em diferentes trabalhadores, desde que esses nós dos trabalhadores estejam disponíveis.
* A recuperação de falhas rápidas pode ocorrer dentro de um segundo, uma vez que a recuperação de falhas/vagabundos acontece através da computação na memória.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Exatamente uma vez semântica com Spark Streaming

Para criar uma aplicação que processa cada evento uma vez (e apenas uma vez), considere como todos os pontos de falha do sistema recomeçam depois de ter um problema, e como pode evitar a perda de dados. Exatamente uma vez que a semântica exija que nenhum dado seja perdido em nenhum momento, e que o processamento de mensagens é reiniciado, independentemente do local onde a falha ocorra. Consulte criar trabalhos de streaming de [faíscas com exatamente uma vez processamento](apache-spark-streaming-exactly-once.md)de eventos .

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark Streaming e Apache Hadoop YARN

No HDInsight, o trabalho de cluster é coordenado por *Outro Negociador* de Recursos (Arn). Projetar alta disponibilidade para spark streaming inclui técnicas para spark streaming, e também para componentes YARN.  Uma configuração de exemplo usando O ARN é mostrada abaixo.

![Arquitetura YARN](./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png)

As seguintes secções descrevem considerações de design para esta configuração.

### <a name="plan-for-failures"></a>Plano para falhas

Para criar uma configuração de ARN para alta disponibilidade, deve planear uma possível falha de executor ou condutor. Alguns trabalhos de Streaming de Faíscas também incluem requisitos de garantia de dados que precisam de configuração e configuração adicionais. Por exemplo, uma aplicação de streaming pode ter um requisito de negócio para uma garantia de perda de dados zero, apesar de qualquer erro que ocorra no sistema de streaming de hospedagem ou no cluster HDInsight.

Se um **executor** falhar, as suas tarefas e recetores são reiniciados automaticamente pela Spark, pelo que não é necessária qualquer alteração de configuração.

No entanto, se um **condutor** falhar, todos os seus executores associados falham, e todos os blocos recebidos e os resultados da computação são perdidos. Para recuperar de uma falha do condutor, utilize *o checkpoint DStream* conforme descrito em [Create Spark Streaming com exatamente uma vez o processamento](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers)de eventos . O checkpoint DStream guarda periodicamente o *gráfico acíclico direcionado* (DAG) de DStreams para armazenamento tolerante a falhas, como o Armazenamento Azure.  O checkpoint permite que o Spark Structured Streaming reinicie o controlador falhado a partir da informação do checkpoint.  Este reinício do condutor lança novos executores e também reinicia os recetores.

Para recuperar os condutores com controlo de controlo DStream:

* Configure o reinício automático do `yarn.resourcemanager.am.max-attempts`controlador no ARN com a definição de configuração .
* Defina um diretório de checkpoint num sistema `streamingContext.checkpoint(hdfsDirectory)`de ficheirocompatível com HDFS com .
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

* Configure a recuperação de dados perdidos, permitindo o registo de escrita (WAL) `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`com `StorageLevel.MEMORY_AND_DISK_SER`, e desative a replicação na memória para DStreams de entrada com .

Resumindo, utilizando o checkpoint + WAL + recetores fiáveis, poderá fornecer a recuperação de dados "pelo menos uma vez":

* Exatamente uma vez, desde que os dados recebidos não sejam perdidos e as saídas sejam idempotentes ou transacionais.
* Exatamente uma vez, com a nova abordagem Kafka Direct, que usa Kafka como um registo replicado, em vez de usar recetores ou WALs.

### <a name="typical-concerns-for-high-availability"></a>Preocupações típicas para alta disponibilidade

* É mais difícil monitorizar trabalhos de streaming do que trabalhos em lotes. Os trabalhos de streaming de faíscas são normalmente longos, e o YARN não agrega registos até que um trabalho termine.  Os pontos de verificação de faíscas perdem-se durante a aplicação ou atualizações da Spark, e terá de limpar o diretório de checkpoint durante uma atualização.

* Configure o seu modo de cluster YARN para executar os condutores mesmo que um cliente falhe. Para configurar o reinício automático para os condutores:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* A Spark e a Spark Streaming UI têm um sistema de métricas configuráveis. Também pode utilizar bibliotecas adicionais, como a Graphite/Grafana para descarregar métricas do painel de instrumentos, tais como "registos num processados", "utilização de memória/GC no condutor & executores", "atraso total", "utilização do cluster", etc. Na versão 2.1 ou superior do `StreamingQueryListener` Streaming Estruturado, pode utilizar para recolher métricas adicionais.

* Devias segmentar empregos de longa duração.  Quando uma aplicação Spark Streaming é submetida ao cluster, a fila YARN onde o trabalho é executado deve ser definida. Você pode usar um [Programador](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) de Capacidade de ARN para submeter trabalhos de longa duração para separar filas.

* Desligue a sua aplicação de streaming graciosamente. Se as suas compensações forem conhecidas e todo o estado de aplicação for armazenado externamente, então pode parar programáticamente a sua aplicação de streaming no local apropriado. Uma técnica é usar "ganchos de fio" em Spark, verificando se há uma bandeira externa a cada *n* segundos. Também pode utilizar um *ficheiro marcador* que é criado no HDFS ao iniciar a aplicação e, em seguida, removido quando pretende parar. Para uma abordagem de ficheiro de marcação, utilize um fio separado na sua aplicação Spark que chame código semelhante ao seguinte:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do streaming de faíscas Apache](apache-spark-streaming-overview.md)
* [Crie empregos de Streaming de Faíscas Apache com exatamente uma vez processamento de evento](apache-spark-streaming-exactly-once.md)
* [Trabalhos de streaming de faíscas Apache de longa duração no YARN](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Streaming estruturado: Semântica Tolerante a Falhas](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Fluxos desdimensionados: um modelo tolerante a falhas para processamento de fluxo escalável](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
