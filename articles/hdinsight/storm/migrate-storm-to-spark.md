---
title: Migração Azure HDInsight 3.6 Apache Storm para HDInsight 4.0 Apache Spark
description: As diferenças e o fluxo de migração para a migração das cargas de trabalho da Tempestade Apache para o Streaming de Faíscas ou o Streaming Estruturado de Faíscas.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157795"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Migração Azure HDInsight 3.6 Apache Storm para HDInsight 4.0 Apache Spark

Este documento descreve como migrar as cargas de trabalho da Tempestade Apache em HDInsight 3.6 para HDInsight 4.0. O HDInsight 4.0 não suporta o tipo de cluster apache storm e terá de migrar para outra plataforma de dados de streaming. Duas opções adequadas são Apache Spark Streaming e Spark Structured Streaming. Este documento descreve as diferenças entre estas plataformas e também recomenda um fluxo de trabalho para a migração das cargas de trabalho da Tempestade Apache.

## <a name="storm-migration-paths-in-hdinsight"></a>Caminhos de migração de tempestades em HDInsight

Se quiser migrar da Tempestade Apache no HDInsight 3.6 tem várias opções:

* Streaming de faíscas em HDInsight 4.0
* Fluxo estruturado de faísca sondar em HDInsight 4.0
* Azure Stream Analytics

Este documento fornece um guia para migrar da Tempestade Apache para o Streaming de Faíscas e O Streaming Estruturado de Faíscas.

> [!div class="mx-imgBorder"]
> ![Caminho de migração da tempestade HDInsight](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Comparação entre Apache Storm e Spark Streaming, Spark Structured Streaming

O Apache Storm pode fornecer diferentes níveis de processamento garantido de mensagens. Por exemplo, uma aplicação de tempestade básica pode garantir pelo menos uma vez o processamento, e [tridente](https://storm.apache.org/releases/current/Trident-API-Overview.html) pode garantir exatamente uma vez processamento. O Spark Streaming e o Spark Structured Streaming garantem que qualquer evento de entrada é processado exatamente uma vez, mesmo que ocorra uma falha no nó. Storm tem um modelo que processa cada evento, e você também pode usar o modelo Micro Batch com Trident. O Streaming de Faíscas e o Streaming Estruturado de Faíscas fornecem o modelo de processamento de micro-lote.

|  |Storm |Transmissão em fluxo do Spark | Fluxo estruturado de faíscas|
|---|---|---|---|
|**Garantia de processamento de eventos**|Pelo menos uma vez. <br> Exatamente uma vez (Tridente) |[Exatamente uma vez](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Exatamente uma vez](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Modelo de Processamento**|Tempo real <br> Micro Lote (Tridente) |Lote Micro |Lote Micro |
|**Suporte ao tempo do evento**|[Sim](https://storm.apache.org/releases/2.0.0/Windowing.html)|Não|[Sim](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Idiomas**|Java, etc.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Streaming de faísca vs Spark estruturado streaming

O Spark Structured Streaming está a substituir o Spark Streaming (DStreams). O Streaming Estruturado continuará a receber melhorias e manutenção, enquanto os DStreams estarão apenas em modo de manutenção. **Nota: é preciso links para enfatizar este ponto**. O Streaming Estruturado não tem tantas funcionalidades como DStreams para as fontes e pias que suporta fora da caixa, por isso avalie os seus requisitos para escolher a opção de processamento de fluxo spark apropriada.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Processamento de streaming (evento único) vs processamento de micro-lote

Storm fornece um modelo que processa cada evento. Isto significa que todos os registos de entrada serão processados assim que chegarem. As aplicações spark streaming devem esperar uma fração de segundo para recolher cada microlote de eventos antes de enviar esse lote para processamento. Em contraste, uma aplicação orientada para eventos processa cada evento imediatamente. A latência do Spark Streaming é normalmente inferior a alguns segundos. Os benefícios da abordagem do microlote são o processamento de dados mais eficiente e os cálculos agregados mais simples.

> [!div class="mx-imgBorder"]
> ![streaming e processamento de micro-lotes](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Arquitetura de tempestade e componentes

As topologias do Storm são compostas por múltiplos componentes que são dispostos num grafo dirigido acíclico (DAG). Fluxos de dados entre os componentes no gráfico. Cada componente consome um ou mais fluxos de dados e podem, opcionalmente, emitir um ou mais fluxos.

|Componente |Descrição |
|---|---|
|Bico|Traz dados para uma topologia. Emitem um ou mais fluxos para a topologia.|
|Parafuso|Consome fluxos emitidos a partir de bicos ou outros parafusos. Opcionalmente, os bolts podem emitir fluxos para a topologia. Os bolts também são responsáveis pela escrita de dados em serviços externos ou armazenamento, como o HDFS, Kafka ou HBase.|

> [!div class="mx-imgBorder"]
> ![interação dos componentes da tempestade](./media/migrate-storm-to-spark/apache-storm-components.png)

A tempestade consiste nos seguintes três daemons, que mantêm o aglomerado de tempestade funcionando.

|Daemon |Descrição |
|---|---|
|Nimbus|Semelhante ao Hadoop JobTracker, é responsável por distribuir código em torno do cluster e atribuir tarefas a máquinas e monitorização de falhas.|
|Zookeeper|Usado para coordenação de clusters.|
|Supervisor|Ouve o trabalho atribuído à sua máquina e inicia e para os processos dos trabalhadores com base em diretivas da Nimbus. Cada processo de trabalho executa um subconjunto de uma topologia. A lógica de aplicação do utilizador (Bicos e Parafusos) é executada aqui.|

> [!div class="mx-imgBorder"]
> ![nimbus, zookeeper, e daemons supervisor](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Arquitetura e componentes de streaming de faíscas

Os seguintes passos resumem como os componentes funcionam em conjunto no Spark Streaming (DStreams) e no Spark Structured Streaming:

* Quando o Spark Streaming é lançado, o condutor lança a tarefa no executor.
* O executor recebe um fluxo de uma fonte de dados de streaming.
* Quando o executor recebe fluxos de dados, divide o fluxo em blocos e mantém-nos na memória.
* Os blocos de dados são replicados a outros executores.
* Os dados processados são então armazenados na loja de dados alvo.

> [!div class="mx-imgBorder"]
> ![caminho de streaming de faíscas para a saída](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Fluxo de trabalho de Spark Streaming (DStream)

À medida que cada intervalo de lote decorre, é produzido um novo DDr que contém todos os dados desse intervalo. Os conjuntos contínuos de RDDs são recolhidos num DStream. Por exemplo, se o intervalo do lote for de um segundo de comprimento, o seu DStream emite um lote a cada segundo contendo um RDD que contém todos os dados ingeridos durante esse segundo. Ao processar o DStream, o evento de temperatura aparece num destes lotes. Uma aplicação Spark Streaming processa os lotes que contêm os eventos e, em última análise, atua nos dados armazenados em cada RDD.

> [!div class="mx-imgBorder"]
> ![lotes de processamento de streaming de faíscas](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Para mais detalhes sobre as diferentes transformações disponíveis com o Spark Streaming, consulte [Transformations on DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Fluxo estruturado de faíscas

O Spark Structured Streaming representa um fluxo de dados como uma tabela que não está limitada em profundidade. A tabela continua a crescer à medida que novos dados chegam. Esta tabela de entrada é continuamente processada por uma consulta de longa duração, e os resultados são enviados para uma tabela de saída.

No Streaming Estruturado, os dados chegam ao sistema e são imediatamente ingeridos numa tabela de entrada. Escreve consultas (utilizando as APIs dataFrame e Dataset) que realizam operações contra esta tabela de entrada.

A saída de consulta produz uma tabela de *resultados,* que contém os resultados da sua consulta. Pode extrair dados da tabela de resultados para uma datastore externa, uma base de dados relacional.

O tempo de quando os dados são processados a partir da tabela de entrada é controlado pelo intervalo do gatilho. Por padrão, o intervalo do gatilho é zero, por isso o Streaming Estruturado tenta processar os dados assim que chega. Na prática, isto significa que assim que o Streaming Estruturado é feito processando a execução da consulta anterior, inicia outra execução de processamento contra quaisquer dados recentemente recebidos. Pode configurar o gatilho a ser executado num intervalo, de modo a que os dados de streaming sejam processados em lotes baseados no tempo.

> [!div class="mx-imgBorder"]
> ![processamento de dados em streaming estruturado](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![modelo de programação para streaming estruturado](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>Fluxo geral de migração

O fluxo de migração recomendado de Storm para Spark assume a seguinte arquitetura inicial:

* Kafka é usada como fonte de dados de streaming
* Kafka e Storm são implantados na mesma rede virtual
* Os dados processados pela Storm são escritos para um sumidouro de dados, como o Azure Storage ou o Azure Data Lake Storage Gen2.

    > [!div class="mx-imgBorder"]
    > ![diagrama de ambiente atual presumido](./media/migrate-storm-to-spark/presumed-current-environment.png)

Para migrar a sua aplicação de Storm para uma das APIs de streaming de Faíscas, faça o seguinte:

1. **Implementar um novo aglomerado.** Implemente um novo cluster HDInsight 4.0 Spark na mesma rede virtual e implemente a sua aplicação Spark Streaming ou Spark Structured Streaming nele e teste-o completamente.

    > [!div class="mx-imgBorder"]
    > ![nova implantação de faíscas no HDInsight](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **Pare de consumir no velho aglomerado de tempestades.** Na tempestade existente, pare de consumir dados da fonte de dados de streaming e espere que os dados terminem de escrever para o lavatório alvo.

    > [!div class="mx-imgBorder"]
    > ![parar de consumir no aglomerado atual](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Comece a consumir o novo cluster Spark.** Comece a transmitir dados de um cluster hDInsight 4.0 Spark recentemente implantado. Neste momento, o processo é assumido consumindo a partir da última compensação de Kafka.

    > [!div class="mx-imgBorder"]
    > ![começar a consumir em novo cluster](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **Retire o aglomerado antigo conforme necessário.** Uma vez que o interruptor esteja completo e funcione corretamente, remova o antigo aglomerado de tempestade HDInsight 3.6 conforme necessário.

    > [!div class="mx-imgBorder"]
    > ![remover os antigos clusters HDInsight conforme necessário](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre Storm, Spark Streaming e Spark Structured Streaming, consulte os seguintes documentos:

* [Visão geral do Apache Spark Streaming](../spark/apache-spark-streaming-overview.md)
* [Visão geral do streaming estruturado de faíscas Apache](../spark/apache-spark-structured-streaming-overview.md)