---
title: Transmissão em fluxo em escala no Azure HDInsight
description: Como utilizar os dados de transmissão em fluxo com clusters do HDInsight dimensionáveis.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: fd2614c258aff146397e24e688eae18d84d3cfa6
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201165"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Transmissão em fluxo em escala no HDInsight

Soluções de macrodados em tempo real agirem sobre dados em movimento. Normalmente, estes dados são mais valiosos em seu tempo de chegada. Se o fluxo de dados de entrada se torna maior do que podem ser processadas nesse momento, se pretender limitar a redução de recursos. Em alternativa, um cluster do HDInsight, pode aumentar verticalmente para atender a sua solução de transmissão em fluxo ao adicionar nós a pedido.


Num aplicativo de transmissão em fluxo, uma ou mais origens de dados estão a gerar eventos (por vezes em milhões por segundo), que tem de ser ingeridos rapidamente sem remover quaisquer informações úteis. Os eventos de entrada são processados com *fluxo na memória intermédia*, também conhecido como *fila de eventos*, por um serviço como [Apache Kafka](kafka/apache-kafka-introduction.md) ou [dos Hubs de eventos](https://azure.microsoft.com/services/event-hubs/). Depois de recolher os eventos, em seguida, pode analisar os dados a utilizar um sistema de análise em tempo real dentro do *processamento de fluxo* colocar em camadas, como [Apache Storm](storm/apache-storm-overview.md) ou [Apache Spark Streaming](spark/apache-spark-streaming-overview.md). Os dados processados podem ser armazenados em sistemas de armazenamento de longo prazo, como [armazenamento do Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)e apresentadas em tempo real num business intelligence dashboard, tais como [Power BI](https://powerbi.microsoft.com), Tableau ou um web personalizado página.


![Padrões de Streaming do HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka fornece um alto débito e o serviço de colocação em fila de mensagens de baixa latência e faz agora parte do pacote do Apache Software de código aberto (OSS). Kafka utiliza um publicar e subscrever fluxos de modelo e arquivos de mensagens de dados particionados com segurança num cluster distribuído, replicado. Kafka dimensionados de maneira linear à medida que aumenta o débito.

Para obter mais informações, consulte [introdução ao Apache Kafka no HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

O Apache Storm é um sistema de computação distribuída, tolerante a falhas, código-fonte aberto otimizada para o processamento de fluxos de dados em tempo real com o Hadoop. A unidade básica de dados para um evento de entrada é uma cadeia de identificação, o que é um imutável conjunto de pares chave/valor. Uma sequência não vinculada essas formas de cadeias de identificação é um Stream, que é proveniente de um Spout. O Spout encapsula uma origem de dados de transmissão em fluxo (por exemplo, o Kafka) e emite as Tuplas. Um topologia do storm é uma sequência de transformações nestes fluxos.

Para obter mais informações, consulte [o que é o Apache Storm no HDInsight do Azure?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Transmissão em fluxo do Spark

Transmissão em fluxo do Spark é uma extensão para Spark, permitindo-lhe reutilizar o mesmo código que utiliza para processamento em lotes. Pode combinar o batch e consultas interativas no mesmo aplicativo. Ao contrário do Storm, Spark Streaming permite com monitoração de estado exatamente-processamento semântica uma vez. Quando utilizado em combinação com o [API direta do Kafka](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), que garante que todos os dados do Kafka são recebidos pelo Spark Streaming exatamente uma vez, é possível obter o ponto-a-ponto exatamente-garante uma vez. Uma das vantagens do Spark Streaming é seus recursos de tolerante a falhas, a recuperação falha nós rapidamente quando vários nós que estão a ser utilizados dentro do cluster.

Para obter mais informações, consulte [o que é o Apache Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Dimensionar um cluster

Embora possa especificar o número de nós do cluster durante a criação, pode querer aumentar ou diminuir o cluster de acordo com a carga de trabalho. Todos os clusters do HDInsight permitem-lhe [alterar o número de nós do cluster](hdinsight-administer-use-portal-linux.md#scale-clusters). Possível ignorar os clusters do Spark sem perda de dados, como todos os dados são armazenados no armazenamento do Azure ou o armazenamento do Data Lake.

Há vantagens em tecnologias de desassociação. Por exemplo, o Kafka é um evento na memória intermédia de tecnologia, para que ele é bastante e/s intensivas e não precisa de muito poder de processamento. Em comparação, processadores de fluxos, como o Spark Streaming são intensivas de computação, que requerem mais poderosas VMs. Fazendo com que essas tecnologias dissociadas em clusters diferentes, pode ajustá-las forma independente ao utilizar as VMs melhor.

### <a name="scale-the-stream-buffering-layer"></a>Dimensionar o fluxo na memória intermédia de camada

O fluxo na memória intermédia de tecnologias de Hubs de eventos e Kafka utilizam partições e os consumidores leem nessas partições. Dimensionar o débito de entrada requer a aumentar o número de partições e adicionar partições oferece a cada vez maior de paralelismo. Nos Hubs de eventos, não é possível alterar o número de partições após a implementação, portanto, é importante começar com a escala de destino em mente. Com o Kafka, é possível [adicionar partições](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), até mesmo enquanto Kafka está a processar dados. O Kafka fornece uma ferramenta para reatribuir partições, `kafka-reassign-partitions.sh`. HDInsight fornece um [ferramenta de reequilíbrio de réplicas de partição](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Essa ferramenta rebalancear chama o `kafka-reassign-partitions.sh` ferramenta de tal forma que cada réplica está num domínio de falha e o domínio de atualização, tornando a tolerância de falhas de conhecimento e cada vez maior de rack do Kafka.

### <a name="scale-the-stream-processing-layer"></a>Dimensionar a camada de processamento de fluxo

Apache Storm e Spark Streaming suportam a adição de nós de trabalho para os clusters, mesmo quando os dados que está a ser processados.

Para tirar partido dos novos nós adicionados através do Storm de dimensionamento, tem de reequilibrar as qualquer topologias do Storm iniciadas antes de o aumento do tamanho de cluster. Este reequilíbrio pode ser feito usando o Storm web de interface do Usuário ou a CLI. Para obter mais informações, consulte a [documentação do Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark utiliza três parâmetros de chave para configurar o seu ambiente, dependendo dos requisitos de aplicação: `spark.executor.instances`, `spark.executor.cores`, e `spark.executor.memory`. Uma *executor* é um processo que é iniciado para uma aplicação de Spark. Um executor é executado no nó de trabalho e é responsável pela realização de tarefas do aplicativo. O número predefinido de executores e os tamanhos de executor para cada cluster é calculado com base no número de nós de trabalho e o tamanho de nó de trabalho. Esses números são armazenados no `spark-defaults.conf`arquivo em cada nó principal do cluster.

Estes três parâmetros podem ser configurados ao nível do cluster, para todas as aplicações que são executados no cluster e, também pode ser especificado para cada aplicativo individual. Para obter mais informações, consulte [gestão de recursos para clusters do Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Apache Storm no HDInsight](storm/apache-storm-tutorial-get-started-linux.md)
* [Topologias de exemplo para Apache Storm no HDInsight](storm/apache-storm-example-topology.md)
* [Introdução ao Apache Spark no HDInsight](spark/apache-spark-overview.md)
* [Introdução ao Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md)
