---
title: Criar tarefas de transmissão em fluxo do Spark altamente disponíveis no YARN - Azure HDInsight
description: Como configurar a transmissão em fluxo do Spark para um cenário de elevada disponibilidade.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/26/2018
ms.openlocfilehash: 79a36ad39284dc66467ba7c500a363668f78b893
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098833"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Criar tarefas de Apache Spark Streaming de elevada disponibilidade com o YARN

[Apache Spark](https://spark.apache.org/) a transmissão em fluxo permite-lhe implementar aplicações dimensionáveis e de alto débito, tolerante a falhas para processamento de fluxos de dados. Pode ligar aplicações de transmissão em fluxo do Spark num cluster do Spark do HDInsight para uma variedade de origens de dados, como Hubs de eventos do Azure, IoT Hub do Azure, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ ZeroMQ](http://zeromq.org/), Soquetes TCP bruto, ou através da monitorização do [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) sistema de ficheiros para que as alterações. Transmissão em fluxo do Spark oferece suporte à tolerância a falhas com a garantia de que a qualquer evento específico é processado exatamente uma vez, mesmo com uma falha de nó.

Transmissão em fluxo do Spark cria tarefas de longa execução durante o qual é possível aplicar transformações aos dados e, em seguida, emitir os resultados para sistemas de ficheiros, bases de dados, dashboards e a consola. Transmissão em fluxo do Spark processa micro-lotes de dados, através da primeira recolha um lote de eventos ao longo de um intervalo de tempo definido. Em seguida, esse lote é enviada para processamento e de saída. Intervalos de tempo de lote, normalmente, são definidos em frações de segundo.

![Transmissão em fluxo do Spark](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>DStreams

Transmissão em fluxo do Spark representa um fluxo contínuo de dados com um *stream discretizado* (DStream). Este DStream pode ser criado a partir de origens de entrada, como os Hubs de eventos ou Kafka ou ao aplicar transformações em outro DStream. Quando um evento chegar ao seu aplicativo de transmissão em fluxo do Spark, o evento é armazenado de forma confiável. Ou seja, os dados do evento são replicados para que vários nós tenham uma cópia do mesmo. Isto garante que a falha de qualquer nó único não resultará na perda de seu evento.

O Spark core utiliza *conjuntos de dados de distribuídos resilientes* (RDDs). RDDs distribuam os dados em vários nós do cluster, onde cada nó geralmente mantém seus dados completamente na memória para um melhor desempenho. Cada RDD representa os eventos recolhidos ao longo de um intervalo de batch. Quando o intervalo de lotes decorrido, o Spark Streaming produz um novo RDD que contém todos os dados nesse intervalo. Este conjunto de RDDs é recolhido para um DStream. Uma aplicação de transmissão em fluxo do Spark processa os dados armazenados em RDD cada lote.

![Spark DStream](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>Tarefas de transmissão estruturada do Spark

A transmissão estruturada do Spark foi introduzido no Spark 2.0 como um mecanismo analítico para uso em dados estruturados de transmissão em fluxo. A transmissão estruturada do Spark utiliza SparkSQL motor de APIs de criação de batches. Tal como acontece com Spark Streaming, Spark Structured Streaming executa seus cálculos ao longo do que chegam continuamente micro-lotes de dados. A transmissão estruturada do Spark representa um fluxo de dados como uma tabela de entrada com linhas ilimitadas. Ou seja, a tabela de entrada continua a crescer à medida que chegam novos dados. Esta tabela de entrada continuamente é processada por uma consulta de execução longa, e os resultados são escritos para uma tabela de saída.

![Transmissão em fluxo estruturada do spark](./media/apache-spark-streaming-high-availability/structured-streaming.png)

Na transmissão em fluxo estruturada, os dados chegam o sistema e imediatamente são ingeridos para a tabela de entrada. Escrever consultas que realizam operações em relação a esta tabela de entrada. O resultado da consulta produz outra tabela, chamada a tabela de resultados. A tabela de resultados contém os resultados da sua consulta, a partir do qual desenha dados a enviar para um arquivo de dados externo essa base de dados relacional. O *intervalo de Acionador* define o período de tempo para quando os dados são processados da tabela de entrada. Por predefinição, transmissão em fluxo estruturada processa os dados assim que são recebidos. No entanto, também pode configurar o acionador para ser executado no intervalo, para que os dados de transmissão em fluxo são processados em lotes com base no tempo. Os dados na tabela de resultados podem ser completamente atualizados sempre que há novos dados, para que ele inclui todos os dados de saída, uma vez que começou a consulta de transmissão em fluxo (*modo de conclusão*), ou apenas pode conter apenas os dados que há de novos desde o último tempo que a consulta foi processada (*acrescentar modo*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Criar tarefas de transmissão em fluxo do Spark tolerante a falhas

Para criar um ambiente de elevada disponibilidade para as tarefas do Spark Streaming, começar por programar as tarefas individuais para recuperação em caso de falha. Essas tarefas de Self-recuperação são tolerante a falhas.

RDDs tem várias propriedades que ajudam a elevada disponibilidade e tolerante a falhas tarefas de transmissão em fluxo do Spark:

* Lotes de dados de entrada armazenados em RDDs como um DStream são replicados automaticamente na memória para a tolerância a falhas.
* Podem ser recalculados dados perdidos devido a falha da função de trabalho de dados de entrada replicados em diferentes funções de trabalho, desde que esses nós de trabalho estão disponíveis.
* Recuperação rápida de falhas ocorrem dentro de um segundo, como a recuperação de falhas/Retardatários acontece através de computação na memória.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Exatamente-uma vez semântica com transmissão em fluxo do Spark

Para criar uma aplicação que processa cada evento, uma vez (e apenas uma vez), considere como a todos os pontos de falha de sistema reiniciar depois de ter um problema e como pode evitar a perda de dados. Exatamente-assim que a semântica de exige que não se perdem a qualquer momento e esse processamento de mensagens é reinicializável, independentemente de onde a falha ocorre. Ver [criar Spark Streaming tarefas com exatamente-uma vez o evento de processamento](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Transmissão em fluxo do Spark e o Apache Hadoop YARN

No HDInsight, o trabalho de cluster é coordenado pelo *ainda outro recurso Negociador* (YARN). Projetar elevada disponibilidade para transmissão em fluxo do Spark inclui técnicas para transmissão em fluxo do Spark bem como para componentes do YARN.  Um exemplo de configuração com o YARN é mostrado abaixo. 

![Arquitetura YARN](./media/apache-spark-streaming-high-availability/yarn-arch.png)

As secções seguintes descrevem as considerações de design para esta configuração.

### <a name="plan-for-failures"></a>Plano para falhas

Para criar uma configuração de YARN para elevada disponibilidade, deve planejar uma possível falha de executor ou controlador. Algumas tarefas de transmissão em fluxo do Spark também incluem requisitos de garantia de dados que necessitam de configuração adicionais e a configuração. Por exemplo, um aplicativo de transmissão em fluxo pode ter um requisito comercial para uma perda de dados de zero, garantimos não obstante qualquer erro que ocorre no sistema de transmissão em fluxo de anfitrião ou cluster do HDInsight.

Se um **executor** falhar, suas tarefas e recetores são reiniciados pelo Spark automaticamente, portanto, não há nenhuma alteração de configuração necessária.

No entanto, se um **driver** falhar, em seguida, todos os seus associados executores falharem e recebidos de todos os blocos e resultados de computação são perdidos. Para recuperar de uma falha de driver, utilize *DStream pontos de verificação* conforme descrito na [criar Spark Streaming tarefas com exatamente-uma vez o evento de processamento](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). Ponto de verificação de DStream periodicamente guarda o *direcionado gráficos acíclicos* (DAG) de DStreams para o armazenamento tolerante a falhas, como o armazenamento do Azure.  Ponto de verificação permite Spark transmissão em fluxo estruturada reiniciar o controlador com falha das informações de ponto de verificação.  Este reinício do controlador inicia executores novo e também reinicia recetores.

Para recuperar os controladores com DStream pontos de verificação:

* Configurar o reinício do controlador automática no YARN com a definição de configuração `yarn.resourcemanager.am.max-attempts`.
* Definir um diretório de ponto de verificação num sistema de ficheiros compatível com HDFS com `streamingContext.checkpoint(hdfsDirectory)`.
* Reestruture o código-fonte para utilizar pontos de verificação para recuperação, por exemplo:

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

* Configurar a recuperação de perda de dados ao ativar o registo de escrita-ahead (WAL) com `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`e desative a replicação na memória para entrada DStreams com `StorageLevel.MEMORY_AND_DISK_SER`.

Para resumir, através do ponto de verificação + WAL + confiáveis recetores, poderá entregar ", pelo menos, uma vez" recuperação de dados:

* Exatamente uma vez, desde que os dados recebidos não são perdidos e as saídas são ambos idempotentes ou transacional.
* Exatamente uma vez, com a nova abordagem direta de Kafka que utilize Kafka como um log replicado, em vez de utilizar recetores ou WALs.

### <a name="typical-concerns-for-high-availability"></a>Questões típicas para elevada disponibilidade

* É mais difícil monitorizar tarefas de transmissão em fluxo dos trabalhos de lote. Tarefas de transmissão em fluxo do Spark estão normalmente longa e YARN não agrega registos até que seja uma tarefa concluída.  Pontos de verificação do Spark são perdidos durante a aplicação ou atualizações de Spark, e terá de limpar o diretório de ponto de verificação durante uma atualização.

* Configure o seu modo de cluster YARN para executar drivers, mesmo se um cliente falhar. Para configurar o reinício automático para controladores:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark e a IU de transmissão em fluxo do Spark tem um sistema de métricas configuráveis. Também pode utilizar bibliotecas adicionais, tais como Graphite/Grafana para transferir as métricas de dashboard, como "registos de núm processados", "Utilização de memória/GC no driver & executores", "atraso total", "utilização do cluster" e assim por diante. Transmissão em fluxo estruturada versão 2.1 ou superior, pode utilizar `StreamingQueryListener` para reunir as métricas adicionais.

* Deverá segmentar as tarefas de longa execução.  Quando um aplicativo de transmissão em fluxo do Spark é submetido para o cluster, tem de ser definida a fila YARN em que a tarefa é executada. Pode utilizar um [agendador de capacidade do YARN](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) submeter tarefas de longa execução para separar filas.

* Encerre corretamente o seu aplicativo de transmissão em fluxo. Se seus deslocamentos são conhecidos e todos os Estados do aplicativo são armazenados externamente, pode parar por meio de programação a aplicação de transmissão em fluxo no local apropriado. Uma técnica é usar "thread ganchos" no Spark, verificando para um sinalizador externo cada *n* segundos. Também pode utilizar um *ficheiro de marcador* que é criado no HDFS quando iniciar a aplicação, em seguida, removido quando deseja interromper. Para uma abordagem de ficheiro de marcador, utilize um thread separado na sua aplicação do Spark que chama o código semelhante ao seguinte:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral da transmissão em fluxo do Apache Spark](apache-spark-streaming-overview.md)
* [Criar tarefas do Apache Spark Streaming com exatamente-uma vez o evento de processamento](apache-spark-streaming-exactly-once.md)
* [Tarefas de transmissão em fluxo do Spark Apache de execução longa no YARN](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) 
* [Transmissão em fluxo estruturada: Semântica de tolerância a falhas](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Discretizado fluxos: Um modelo tolerante a falhas para processamento de Stream dimensionável](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
