---
title: Migrar Azure HDInsight 3.6 Apache Storm to HDInsight 4.0 Apache Spark
description: As diferenças e o fluxo migratório para a migração das cargas de carga da Tempestade Apache para o Streaming de Faíscas ou Para o Streaming Estruturado de Faíscas.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/16/2019
ms.openlocfilehash: b8b054d06c9c0987508abfdf03bbcf9470572bd1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868771"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Migrar Azure HDInsight 3.6 Apache Storm to HDInsight 4.0 Apache Spark

Este documento descreve como migrar cargas de carga de Apache Storm em HDInsight 3.6 a HDInsight 4.0. O HDInsight 4.0 não suporta o tipo de cluster Apache Storm e terás de migrar para outra plataforma de dados de streaming. Duas opções adequadas são Apache Spark Streaming e Spark Structured Streaming. Este documento descreve as diferenças entre estas plataformas e também recomenda um fluxo de trabalho para a migração das cargas de trabalho da Tempestade Apache.

## <a name="storm-migration-paths-in-hdinsight"></a>Caminhos de migração da tempestade em HDInsight

Se quiser migrar da Tempestade Apache em HDInsight 3.6 tem várias opções:

* Streaming de faísca em HDInsight 4.0
* Fluxo estruturado de faísca em HDInsight 4.0
* Azure Stream Analytics

Este documento fornece um guia para migrar da Tempestade Apache para o Streaming de Faíscas e O Streaming Estruturado de Faíscas.

:::image type="content" source="./media/migrate-storm-to-spark/storm-migration-path.png" alt-text="Caminho de migração da tempestade HDInsight" border="false":::

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Comparação entre Tempestade Apache e Streaming de Faíscas, Streaming Estruturado de Faíscas

O Apache Storm pode fornecer diferentes níveis de processamento garantido de mensagens. Por exemplo, uma aplicação básica storm pode garantir pelo menos uma vez o processamento, e [trident pode](https://storm.apache.org/releases/current/Trident-API-Overview.html) garantir exatamente uma vez o processamento. O Streaming spark e o streaming estruturado de faíscas garantem que qualquer evento de entrada é processado exatamente uma vez, mesmo que ocorra uma falha no nó. Storm tem um modelo que processa cada evento, e você também pode usar o modelo Micro Batch com Trident. O streaming de faíscas e o streaming estruturado de faíscas fornecem Micro-Batch modelo de processamento.

|  |Storm |Transmissão em fluxo do Spark | Fluxo estruturado de faísca|
|---|---|---|---|
|**Garantia de processamento de eventos**|Pelo menos uma vez. <br> Exatamente uma vez (Tridente) |[Exatamente uma vez](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Exatamente uma vez](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Modelo de processamento**|Em tempo real <br> Micro Lote (Tridente) |Micro Lote |Micro Lote |
|**Suporte de tempo do evento**|[Sim](https://storm.apache.org/releases/2.0.0/Windowing.html)|No|[Sim](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Idiomas**|Java, etc.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Streaming de faísca vs Spark streaming estruturado

O Streaming Estruturado de Faísca está a substituir o Streaming spark (DStreams). O Streaming Estruturado continuará a receber melhorias e manutenção, enquanto o DStreams estará apenas em modo de manutenção. **Nota: precisa de ligações para enfatizar este ponto.** O Streaming Estruturado não tem tantas funcionalidades como o DStreams para as fontes e pias que suporta fora da caixa, por isso avalie os seus requisitos para escolher a opção de processamento de fluxo spark apropriado.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Processamento de streaming (evento único) vs processamento Micro-Batch

A tempestade fornece um modelo que processa cada evento. Isto significa que todos os registos de entrada serão processados assim que chegarem. As aplicações spark streaming devem esperar uma fração de segundo para recolher cada micro-lote de eventos antes de enviar esse lote para processamento. Em contraste, uma aplicação orientada para o evento processa cada evento imediatamente. A latência do streaming de faíscas é normalmente em poucos segundos. Os benefícios da abordagem do micro-lote são o processamento de dados mais eficiente e cálculos agregados mais simples.

:::image type="content" source="./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png" alt-text="processamento de streaming e micro-lotes" border="false":::

## <a name="storm-architecture-and-components"></a>Arquitetura de tempestade e componentes

As topologias do Storm são compostas por múltiplos componentes que são dispostos num grafo dirigido acíclico (DAG). Fluxos de dados entre os componentes no gráfico. Cada componente consome um ou mais fluxos de dados e podem, opcionalmente, emitir um ou mais fluxos.

|Componente |Descrição |
|---|---|
|Bico|Traz dados para uma topologia. Emitem um ou mais fluxos para a topologia.|
|Parafuso|Consome riachos emitidos a partir de bicos ou outros parafusos. Opcionalmente, os bolts podem emitir fluxos para a topologia. Os bolts também são responsáveis pela escrita de dados em serviços externos ou armazenamento, como o HDFS, Kafka ou HBase.|

:::image type="content" source="./media/migrate-storm-to-spark/apache-storm-components.png" alt-text="interação dos componentes da tempestade" border="false":::

A tempestade consiste nos seguintes três daemons, que mantêm o aglomerado de tempestades funcionando.

|Rio Daemon |Description |
|---|---|
|Nimbus|Semelhante ao Hadoop JobTracker, é responsável por distribuir código em torno do cluster e atribuir tarefas a máquinas e monitorização de falhas.|
|Zookeeper|Usado para coordenação de agrupamentos.|
|Supervisor|Ouve o trabalho atribuído à sua máquina e inicia e para os processos dos trabalhadores com base em diretivas da Nimbus. Cada processo de trabalho executa um subconjunto de uma topologia. A lógica de aplicação do utilizador (Bicos e Parafusos) é executada aqui.|

:::image type="content" source="./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png" alt-text="nimbus, zookeeper, e daemons supervisor" border="false":::

## <a name="spark-streaming-architecture-and-components"></a>Arquitetura e componentes de streaming de faíscas

Os seguintes passos resumem como os componentes funcionam em conjunto no Streaming de Faíscas (DStreams) e no Streaming Estruturado de Faíscas:

* Quando o Spark Streaming é lançado, o controlador lança a tarefa no executor.
* O executor recebe um fluxo de uma fonte de dados de streaming.
* Quando o executor recebe fluxos de dados, divide o fluxo em blocos e mantém-nos na memória.
* Os blocos de dados são replicados a outros executores.
* Os dados processados são então armazenados na loja de dados-alvo.

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-to-output.png" alt-text="caminho de streaming de faísca para a saída" border="false":::

## <a name="spark-streaming-dstream-workflow"></a>Fluxo de trabalho de streaming de faíscas (DStream)

À medida que cada intervalo de lote decorre, é produzido um novo RDD que contém todos os dados desse intervalo. Os conjuntos contínuos de RDDs são recolhidos num DStream. Por exemplo, se o intervalo do lote tiver um segundo de comprimento, o seu DStream emite um lote a cada segundo contendo um RDD que contém todos os dados ingeridos durante esse segundo. Ao processar o DStream, o evento de temperatura aparece num destes lotes. Uma aplicação Spark Streaming processa os lotes que contêm os eventos e, em última análise, atua nos dados armazenados em cada RDD.

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-batches.png" alt-text="lotes de processamento de streaming de faíscas" border="false":::

Para mais detalhes sobre as diferentes transformações disponíveis com o Spark Streaming, consulte [Transformações em DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Fluxo Estruturado de Faísca

O Streaming Estruturado de Faíscas representa um fluxo de dados como uma tabela que não é limitada em profundidade. A tabela continua a crescer à medida que chegam novos dados. Esta tabela de entrada é continuamente processada por uma consulta de longa duração, e os resultados são enviados para uma tabela de saída.

No Streaming Estruturado, os dados chegam ao sistema e são imediatamente ingeridos numa tabela de entrada. Escreve consultas (utilizando as APIs do DataFrame e do Dataset) que realizam operações contra esta tabela de entrada.

A produção de consulta produz uma *tabela de resultados,* que contém os resultados da sua consulta. Pode extrair dados da tabela de resultados de uma datastore externa, tal base de dados relacional.

O tempo de quando os dados são tratados a partir da tabela de entrada é controlado pelo intervalo do gatilho. Por predefinição, o intervalo do gatilho é zero, pelo que o Streaming Estruturado tenta processar os dados assim que chega. Na prática, isto significa que assim que o Streaming Estruturado é feito o processamento da execução da consulta anterior, inicia outra execução de processamento contra quaisquer dados recém-recebidos. Pode configurar o gatilho para ser executado num intervalo, de modo a que os dados de streaming são processados em lotes baseados no tempo.

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-data-processing.png" alt-text="tratamento de dados em streaming estruturado" border="false":::

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-model.png" alt-text="modelo de programação para streaming estruturado" border="false":::

## <a name="general-migration-flow"></a>Fluxo migratório geral

O fluxo de migração recomendado de Tempestade a Faísca pressupõe a seguinte arquitetura inicial:

* Kafka é usada como fonte de dados de streaming
* Kafka e Storm são implantados na mesma rede virtual
* Os dados processados por Storm são escritos para um sumidouro de dados, como O Azure Storage ou Azure Data Lake Storage Gen2.

   :::image type="content" source="./media/migrate-storm-to-spark/presumed-current-environment.png" alt-text="diagrama do ambiente presumível"  border="false":::

Para migrar a sua aplicação de Storm para uma das APIs de streaming spark, faça o seguinte:

1. **Implementar um novo aglomerado.** Implemente um novo cluster HDInsight 4.0 Spark na mesma rede virtual e implemente a sua aplicação spark streaming ou spark Structured Streaming nele e teste-o cuidadosamente.

   :::image type="content" source="./media/migrate-storm-to-spark/new-spark-deployment.png" alt-text="nova implantação de faíscas em HDInsight" border="false":::

1. **Pare de consumir no velho aglomerado de tempestades.** Na tempestade existente, pare de consumir dados da fonte de dados de streaming e espere que os dados terminem de escrever para o afundanço alvo.

   :::image type="content" source="./media/migrate-storm-to-spark/stop-consuming-current-cluster.png" alt-text="parar de consumir no cluster atual" border="false":::

1. **Comece a consumir no novo cluster Spark.** Inicie o streaming de dados a partir de um cluster HDInsight 4.0 Spark recentemente implantado. Neste momento, o processo é assumido consumindo a partir da mais recente compensação kafka.

   :::image type="content" source="./media/migrate-storm-to-spark/start-consuming-new-cluster.png" alt-text="começar a consumir em novo cluster" border="false":::

1. **Retire o aglomerado antigo conforme necessário.** Uma vez que o interruptor esteja completo e funcionando corretamente, remova o antigo cluster de tempestade HDInsight 3.6, conforme necessário.

   :::image type="content" source="./media/migrate-storm-to-spark/remove-old-clusters1.png" alt-text="remover clusters HDInsight antigos, conforme necessário" border="false":::

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre Storm, Spark Streaming e Spark Structured Streaming, consulte os seguintes documentos:

* [Visão geral do Fluxo de Faíscas Apache](../spark/apache-spark-streaming-overview.md)
* [Visão geral do Fluxo Estruturado de Faíscas Apache Spark](../spark/apache-spark-structured-streaming-overview.md)
