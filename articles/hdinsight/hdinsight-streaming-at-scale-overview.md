---
title: Streaming em escala no Azure HDInsight
description: Como usar o streaming de dados com clusters Apache escalonáveis no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: e18203162229e6c3db24a5f76b65de61d572f96a
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076783"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Transmissão em fluxo em escala no HDInsight

Soluções de Big Data em tempo real atuam em dados que estão em movimento. Normalmente, esses dados são mais valiosos no momento da chegada. Se o fluxo de dados de entrada se tornar maior que o que pode ser tratado nesse momento, talvez seja necessário limitar os recursos. Como alternativa, um cluster HDInsight pode escalar verticalmente para atender à sua solução de streaming adicionando nós sob demanda.


Em um aplicativo de streaming, uma ou mais fontes de dados estão gerando eventos (às vezes, em milhões por segundo) que precisam ser ingeridas rapidamente sem descartar informações úteis. Os eventos de entrada são tratados com o *buffer de fluxo*, também chamado de *enfileiramento de eventos*, por um serviço como [Apache Kafka](kafka/apache-kafka-introduction.md) ou [hubs de eventos](https://azure.microsoft.com/services/event-hubs/). Depois de coletar os eventos, você pode analisar os dados usando um sistema de análise em tempo real dentro da camada de *processamento de fluxo* , como [Apache Storm](storm/apache-storm-overview.md) ou [Apache Spark streaming](spark/apache-spark-streaming-overview.md). Os dados processados podem ser armazenados em sistemas de armazenamento de longo prazo, como [Azure data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)e exibidos em tempo real em um painel de Business Intelligence, como [Power bi](https://powerbi.microsoft.com), tableau ou uma página da Web personalizada.


![Padrões de streaming do HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

O Apache Kafka fornece um serviço de enfileiramento de mensagens de baixa latência e alta taxa de transferência e agora faz parte do pacote Apache de software livre (OSS). O Kafka usa um modelo de mensagens de publicação e assinatura e armazena fluxos de dados particionados com segurança em um cluster distribuído e replicado. Kafka escala linearmente à medida que a taxa de transferência aumenta.

Para obter mais informações, consulte [introdução ao Apache Kafka no HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

O Apache Storm é um sistema de computação distribuído, tolerante a falhas e de software livre que é otimizado para processar fluxos de dados em tempo real com o Hadoop. A unidade principal de dados para um evento de entrada é uma tupla, que é um conjunto imutável de pares de chave/valor. Uma sequência não vinculada dessas tuplas forma um fluxo, que é proveniente de um Spout. O Spout encapsula uma fonte de dados de streaming (como Kafka) e emite tuplas. Uma topologia Storm é uma sequência de transformações nesses fluxos.

Para obter mais informações, consulte [o que é Apache Storm no Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Streaming do Spark

O streaming do Spark é uma extensão do Spark, que permite reutilizar o mesmo código que você usa para o processamento em lotes. Você pode combinar consultas de lote e interativas no mesmo aplicativo. Ao contrário do Storm, o Spark streaming fornece semântica de processamento com estado exatamente uma vez. Quando usado em combinação com a [API direta do Kafka](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), que garante que todos os dados do Kafka sejam recebidos pelo Spark streaming exatamente uma vez, é possível atingir garantias de ponta a ponta exatamente uma vez. Um dos pontos fortes do Spark streaming são seus recursos tolerantes a falhas, recuperando os nós com falha rapidamente quando vários nós estão sendo usados dentro do cluster.

Para obter mais informações, consulte [o que é Apache Spark streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Dimensionando um cluster

Embora você possa especificar o número de nós no cluster durante a criação, talvez você queira aumentar ou reduzir o cluster para corresponder à carga de trabalho. Todos os clusters HDInsight permitem [alterar o número de nós no cluster](hdinsight-administer-use-portal-linux.md#scale-clusters). Os clusters do Spark podem ser descartados sem perda de dados, pois todos os dados são armazenados no armazenamento ou Data Lake Storage do Azure.

Há vantagens em desacoplar as tecnologias. Por exemplo, Kafka é uma tecnologia de buffer de eventos, portanto, ele é muito intensivo de e/s e não precisa de muita capacidade de processamento. Em comparação, os processadores de fluxo, como o Spark streaming, são de computação intensiva, exigindo VMs mais poderosas. Ao ter essas tecnologias desacopladas em diferentes clusters, você pode dimensioná-las de forma independente e melhor utilizando as VMs.

### <a name="scale-the-stream-buffering-layer"></a>Dimensionar a camada de buffer de fluxo

Os hubs de eventos das tecnologias de buffer de fluxo e os Kafka usam partições e os consumidores lêem dessas partições. O dimensionamento da taxa de transferência de entrada requer a expansão do número de partições e a adição de partições fornece paralelismo crescente. Nos hubs de eventos, a contagem de partições não pode ser alterada após a implantação, portanto, é importante começar com a escala de destino em mente. Com o Kafka, é possível [Adicionar partições](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), mesmo quando o Kafka está processando dados. O Kafka fornece uma ferramenta para reatribuir partições `kafka-reassign-partitions.sh`,. O HDInsight fornece uma [ferramenta de rebalanceamento de réplica de partição](https://github.com/hdinsight/hdinsight-kafka-tools),. `rebalance_rackaware.py` Essa ferramenta de rebalanceamento chama `kafka-reassign-partitions.sh` a ferramenta de forma que cada réplica esteja em um domínio de falha e atualização de domínio separado, tornando o Kafka rack ciente e aumentando a tolerância a falhas.

### <a name="scale-the-stream-processing-layer"></a>Dimensionar a camada de processamento de fluxo

O Apache Storm e o Spark streaming dão suporte à adição de nós de trabalho a seus clusters, mesmo enquanto os dados estão sendo processados.

Para aproveitar os novos nós adicionados por meio de dimensionamento do Storm, você precisa reequilibrar todas as topologias do Storm iniciadas antes que o tamanho do cluster seja aumentado. Esse rebalanceamento pode ser feito usando a interface do usuário da Web do Storm ou sua CLI. Para obter mais informações, consulte a [documentação do Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

O Apache Spark usa três parâmetros principais para configurar seu ambiente, dependendo dos requisitos do aplicativo `spark.executor.instances`: `spark.executor.cores`, e `spark.executor.memory`. Um *executor* é um processo que é iniciado para um aplicativo Spark. Um executor é executado no nó de trabalho e é responsável por realizar as tarefas do aplicativo. O número padrão de executores e os tamanhos de executor para cada cluster são calculados com base no número de nós de trabalho e no tamanho do nó de trabalho. Esses números são armazenados no `spark-defaults.conf`arquivo em cada nó de cabeçalho do cluster.

Esses três parâmetros podem ser configurados no nível do cluster, para todos os aplicativos que são executados no cluster e também podem ser especificados para cada aplicativo individual. Para obter mais informações, consulte [Gerenciando recursos para clusters de Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Passos Seguintes

* [Criar e monitorar uma topologia de Apache Storm no Azure HDInsight](storm/apache-storm-quickstart.md)
* [Topologias de exemplo para Apache Storm no HDInsight](storm/apache-storm-example-topology.md)
* [Introdução ao Apache Spark no HDInsight](spark/apache-spark-overview.md)
* [Iniciar com o Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md)
