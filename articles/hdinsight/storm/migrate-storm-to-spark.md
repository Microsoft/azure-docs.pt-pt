---
title: Migrar Apache Storm do Azure HDInsight 3,6 para o HDInsight 4,0 Apache Spark
description: As diferenças e o fluxo de migração para migrar cargas de trabalho de Apache Storm para o streaming do Spark ou o fluxo estruturado do Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157795"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Migrar Apache Storm do Azure HDInsight 3,6 para o HDInsight 4,0 Apache Spark

Este documento descreve como migrar cargas de trabalho Apache Storm no HDInsight 3,6 para o HDInsight 4,0. O HDInsight 4,0 não dá suporte ao tipo de cluster Apache Storm e você precisará migrar para outra plataforma de dados de streaming. Duas opções adequadas são Apache Spark streaming estruturado e do Spark. Este documento descreve as diferenças entre essas plataformas e também recomenda um fluxo de trabalho para migrar cargas de trabalho Apache Storm.

## <a name="storm-migration-paths-in-hdinsight"></a>Caminhos de migração do Storm no HDInsight

Se você quiser migrar do Apache Storm no HDInsight 3,6, terá várias opções:

* Streaming do Spark no HDInsight 4,0
* Streaming estruturado do Spark no HDInsight 4,0
* Azure Stream Analytics

Este documento fornece um guia para migração de Apache Storm para Spark streaming e streaming estruturado Spark.

> [!div class="mx-imgBorder"]
> ![caminho de migração do HDInsight Storm](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Comparação entre Apache Storm e o streaming do Spark, streaming estruturado do Spark

O Apache Storm pode fornecer diferentes níveis de processamento garantido de mensagens. Por exemplo, um aplicativo Storm básico pode garantir um processamento de pelo menos uma vez e o [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) pode garantir exatamente o processamento. O streaming do Spark e o streaming estruturado do Spark garantem que qualquer evento de entrada seja processado exatamente uma vez, mesmo se ocorrer uma falha de nó. O Storm tem um modelo que processa cada evento único, e você também pode usar o modelo micro batch com o Trident. O streaming do Spark e o streaming estruturado do Spark fornecem o modelo de processamento micro-Batch.

|  |Storm |Transmissão em fluxo do Spark | Streaming estruturado do Spark|
|---|---|---|---|
|**Garantia de processamento de eventos**|Pelo menos uma vez <br> Exatamente uma vez (Trident) |[Exatamente uma vez](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Exatamente uma vez](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Modelo de processamento**|Tempo real <br> Micro batch (Trident) |Micro batch |Micro batch |
|**Suporte ao horário do evento**|[Sim](https://storm.apache.org/releases/2.0.0/Windowing.html)|Não|[Sim](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Idiomas**|Java, etc.|Escala, Java, Python|Python, R, escalar, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Streaming do Spark versus streaming estruturado do Spark

O streaming estruturado do Spark está substituindo o streaming do Spark (DStreams). O streaming estruturado continuará a receber melhorias e manutenção, enquanto o DStreams estará apenas no modo de manutenção. **Observação: é necessário ter links para enfatizar este ponto**. O streaming estruturado não tem tantos recursos quanto DStreams para as fontes e coletores que ele dá suporte pronto para uso, portanto, avalie seus requisitos para escolher a opção de processamento de fluxo Spark apropriada.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Processamento de streaming (único evento) versus processamento micro batch

O Storm fornece um modelo que processa cada único evento. Isso significa que todos os registros de entrada serão processados assim que eles chegarem. Os aplicativos de streaming do Spark devem aguardar uma fração de um segundo para coletar cada micro lote de eventos antes de enviar esse lote para processamento. Por outro lado, um aplicativo orientado por eventos processa cada evento imediatamente. A latência de streaming do Spark normalmente está abaixo de alguns segundos. Os benefícios da abordagem de micro batch são processamento de dados mais eficiente e cálculos de agregação mais simples.

> [!div class="mx-imgBorder"]
> ![o processamento de streaming e micro-batch](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Componentes e arquitetura do Storm

As topologias do Storm são compostas por múltiplos componentes que são dispostos num grafo dirigido acíclico (DAG). Fluxos de dados entre os componentes no gráfico. Cada componente consome um ou mais fluxos de dados e podem, opcionalmente, emitir um ou mais fluxos.

|Componente |Descrição |
|---|---|
|Spout|Coloca os dados em uma topologia. Emitem um ou mais fluxos para a topologia.|
|Relâmpago|Consome fluxos emitidos de esgotamentos ou outros parafusos. Opcionalmente, os bolts podem emitir fluxos para a topologia. Os bolts também são responsáveis pela escrita de dados em serviços externos ou armazenamento, como o HDFS, Kafka ou HBase.|

> [!div class="mx-imgBorder"]
> ![a interação dos componentes do Storm](./media/migrate-storm-to-spark/apache-storm-components.png)

O Storm consiste nos três daemons a seguir, que mantêm o funcionamento do cluster Storm.

|Demonstração |Descrição |
|---|---|
|Nimbus|Semelhante ao Hadoop JobTracker, é responsável por distribuir código em todo o cluster e atribuir tarefas a computadores e monitorar falhas.|
|Zookeeper|Usado para coordenação de cluster.|
|Supervisor|Escuta o trabalho atribuído à sua máquina e inicia e interrompe os processos de trabalho com base nas diretivas de Nimbus. Cada processo de trabalho executa um subconjunto de uma topologia. A lógica do aplicativo (esgotamento e o parafuso) do usuário é executada aqui.|

> [!div class="mx-imgBorder"]
> ![os daemons Nimbus, Zookeeper e supervisor](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Componentes e arquitetura de streaming do Spark

As etapas a seguir resumem como os componentes funcionam juntos no streaming do Spark (DStreams) e no streaming estruturado do Spark:

* Quando o streaming do Spark é iniciado, o driver inicia a tarefa no executor.
* O executor recebe um fluxo de uma fonte de dados de streaming.
* Quando o executor recebe fluxos de dados, ele divide o fluxo em blocos e os mantém na memória.
* Blocos de dados são replicados para outros executores.
* Os dados processados são então armazenados no armazenamento de dados de destino.

> [!div class="mx-imgBorder"]
> ![caminho de streaming do Spark para a saída](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Fluxo de trabalho do Spark streaming (DStream)

À medida que cada intervalo de lote expira, é produzido um novo RDD que contém todos os dados desse intervalo. Os conjuntos contínuos de RDDs são coletados em um DStream. Por exemplo, se o intervalo de lote for um segundo, o DStream emitirá um lote a cada segundo contendo um RDD que contém todos os dados ingeridos durante esse segundo. Ao processar o DStream, o evento de temperatura aparece em um desses lotes. Um aplicativo Spark streaming processa os lotes que contêm os eventos e, por fim, atua nos dados armazenados em cada RDD.

> [!div class="mx-imgBorder"]
> ![lotes de processamento de streaming do Spark](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Para obter detalhes sobre as diferentes transformações disponíveis no Spark streaming, consulte [transformações em DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Streaming estruturado do Spark

O streaming estruturado do Spark representa um fluxo de dados como uma tabela que não está associada em profundidade. A tabela continua crescendo conforme novos dados chegam. Essa tabela de entrada é processada continuamente por uma consulta de execução longa e os resultados são enviados para uma tabela de saída.

No streaming estruturado, os dados chegam ao sistema e são imediatamente incluídos em uma tabela de entrada. Você escreve consultas (usando as APIs dataframe e DataSet) que executam operações nessa tabela de entrada.

A saída da consulta produz uma *tabela de resultados*, que contém os resultados da consulta. Você pode desenhar dados da tabela de resultados para um repositório de dados externo, como um banco de dado relacional.

O tempo de quando os dados são processados da tabela de entrada é controlado pelo intervalo de gatilho. Por padrão, o intervalo de gatilho é zero, portanto, o streaming estruturado tenta processar os dados assim que eles chegam. Na prática, isso significa que assim que o streaming estruturado terminar de processar a execução da consulta anterior, ele iniciará outra execução de processamento em relação a quaisquer dados recebidos recentemente. Você pode configurar o gatilho para ser executado em um intervalo, para que os dados de streaming sejam processados em lotes baseados em tempo.

> [!div class="mx-imgBorder"]
> ![processamento de dados em](./media/migrate-storm-to-spark/structured-streaming-data-processing.png) de streaming estruturado

> [!div class="mx-imgBorder"]
> modelo de programação de ![para](./media/migrate-storm-to-spark/structured-streaming-model.png) de streaming estruturado

## <a name="general-migration-flow"></a>Fluxo de migração geral

O fluxo de migração recomendado do Storm para o Spark pressupõe a seguinte arquitetura inicial:

* Kafka é usado como a fonte de dados de streaming
* O Kafka e o Storm são implantados na mesma rede virtual
* Os dados processados pelo Storm são gravados em um coletor de dados, como o armazenamento do Azure ou o Azure Data Lake Storage Gen2.

    > [!div class="mx-imgBorder"]
    > ![diagrama de ambiente atual presumido](./media/migrate-storm-to-spark/presumed-current-environment.png)

Para migrar seu aplicativo do Storm para uma das APIs de streaming do Spark, faça o seguinte:

1. **Implante um novo cluster.** Implante um novo cluster HDInsight 4,0 Spark na mesma rede virtual e implante seu aplicativo de streaming estruturado Spark ou Spark e teste-o completamente.

    > [!div class="mx-imgBorder"]
    > ![nova implantação do Spark no HDInsight](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **Pare de consumir no antigo cluster Storm.** No Storm existente, interrompa o consumo de dados da fonte de dados de streaming e aguarde que os dados terminem de gravar no coletor de destino.

    > [!div class="mx-imgBorder"]
    > ![parar de consumir no cluster atual](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Comece a consumir no novo cluster Spark.** Inicie o streaming de dados de um cluster HDInsight 4,0 Spark implantado recentemente. Neste momento, o processo é assumido pelo consumo do deslocamento Kafka mais recente.

    > [!div class="mx-imgBorder"]
    > ![começar a consumir no novo cluster](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **Remova o cluster antigo, conforme necessário.** Quando o comutador estiver concluído e funcionando corretamente, remova o antigo cluster do HDInsight 3,6 Storm, conforme necessário.

    > [!div class="mx-imgBorder"]
    > ![remover clusters HDInsight antigos, conforme necessário](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Storm, o streaming do Spark e o streaming estruturado do Spark, consulte os seguintes documentos:

* [Visão geral de Apache Spark streaming](../spark/apache-spark-streaming-overview.md)
* [Visão geral do Apache Spark streaming estruturado](../spark/apache-spark-structured-streaming-overview.md)