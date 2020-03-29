---
title: Streaming em escala em Azure HDInsight
description: Como utilizar o streaming de dados com clusters Apache escaláveis no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 006310f1a0efa69881bbe6d6ea4403b9c50402e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435386"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Transmissão em fluxo em escala no HDInsight

As soluções de big data em tempo real agem em dados que estão em movimento. Normalmente, estes dados são mais valiosos no momento da sua chegada. Se o fluxo de dados de entrada se tornar maior do que pode ser tratado nesse momento, poderá ter de reduzir os recursos. Em alternativa, um cluster HDInsight pode escalar para satisfazer a sua solução de streaming adicionando nós a pedido.

Numa aplicação de streaming, uma ou mais fontes de dados estão a gerar eventos (por vezes nos milhões por segundo) que precisam de ser ingeridos rapidamente sem deixar cair nenhuma informação útil. Os eventos de entrada são tratados com *buffering de fluxo*, também chamado de *fila de eventos*, por um serviço como [Apache Kafka](kafka/apache-kafka-introduction.md) ou [Event Hubs](https://azure.microsoft.com/services/event-hubs/). Depois de recolher os eventos, pode então analisar os dados utilizando um sistema de análise em tempo real dentro da camada de processamento de *fluxo,* como [apache Storm](storm/apache-storm-overview.md) ou Apache [Spark Streaming](spark/apache-spark-streaming-overview.md). Os dados processados podem ser armazenados em sistemas de armazenamento de longo prazo, como o Armazenamento de [Lagos De Dados De Azure,](https://azure.microsoft.com/services/storage/data-lake-storage/)e exibidos em tempo real num dashboard de inteligência empresarial, como Power [BI,](https://powerbi.microsoft.com)Tableau ou uma página web personalizada.

![Padrões de streaming Azure HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka fornece um serviço de fila de mensagens de alta qualidade e baixa latência, e agora faz parte do conjunto Apache de Software Open Source (OSS). Kafka usa um modelo de mensagens de publicação e subscrição e armazena fluxos de dados divididos com segurança num cluster distribuído e replicado. Kafka escala linearmente à medida que a entrada aumenta.

Para mais informações, consulte [A introdução de Apache Kafka no HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm é um sistema de computação distribuído, tolerante a falhas e de código aberto que é otimizado para processar fluxos de dados em tempo real com Hadoop. A unidade central de dados para um evento de entrada é um Tuple, que é um conjunto imutável de pares chave/valor. Uma sequência ilimitada destes Tuples forma um Riacho, que vem de um bico. O Spout envolve uma fonte de dados de streaming (como Kafka), e emite Tuples. Uma tempestade Topologia é uma sequência de transformações nestes riachos.

Para mais informações, consulte [o que é a Tempestade Apache no Azure HDInsight?](storm/apache-storm-overview.md)

## <a name="spark-streaming"></a>Streaming de faíscas

Spark Streaming é uma extensão da Spark, que permite reutilizar o mesmo código que utiliza para o processamento de lotes. Pode combinar consultas de lote e interativas na mesma aplicação. Ao contrário da Tempestade, o Spark Streaming fornece uma semântica imponente exatamente uma vez que processa a semântica. Quando usado em combinação com a [API Direta](https://spark.apache.org/docs/latest/streaming-kafka-integration.html)de Kafka, que garante que todos os dados de Kafka são recebidos pelo Spark Streaming exatamente uma vez, é possível alcançar o fim-a-dia exatamente uma vez que garante. Um dos pontos fortes do Spark Streaming é a sua capacidade tolerante a falhas, recuperando os nós falhados rapidamente quando vários nós estão sendo usados dentro do cluster.

Para mais informações, veja [o que é Apache Spark Streaming?](hdinsight-spark-streaming-overview.md)

## <a name="scaling-a-cluster"></a>Escalar um aglomerado

Embora possa especificar o número de nós no seu cluster durante a criação, pode querer crescer ou encolher o cluster para corresponder à carga de trabalho. Todos os clusters HDInsight permitem [alterar o número de nós no cluster](hdinsight-administer-use-portal-linux.md#scale-clusters). Os clusters de faíscas podem ser eliminados sem perda de dados, uma vez que todos os dados são armazenados em Armazenamento de Azure ou armazenamento de data lake.

Há vantagens em dissociar tecnologias. Por exemplo, Kafka é uma tecnologia de tampão de eventos, por isso é muito intensiva e não precisa de muito poder de processamento. Em comparação, processadores de streaming como o Spark Streaming são intensivos em computação, requerendo VMs mais potentes. Ao ter estas tecnologias dissociadas em diferentes clusters, pode escaloná-las de forma independente, utilizando melhor os VMs.

### <a name="scale-the-stream-buffering-layer"></a>Escala a camada de tampão de fluxo

As tecnologias de buffering stream Event Hubs e Kafka usam divisórias, e os consumidores lêem dessas divisórias. A escalação da entrada de entrada requer aumentar o número de divisórias e adicionar divisórias proporciona um crescente paralelismo. Em Event Hubs, a contagem de partição não pode ser alterada após a implantação, por isso é importante começar com a escala de alvo em mente. Com Kafka, é possível [adicionar divisórias](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), mesmo enquanto Kafka está processando dados. Kafka fornece uma ferramenta para reatribuir `kafka-reassign-partitions.sh`divisórias, . O HDInsight fornece uma ferramenta `rebalance_rackaware.py`de reequilíbrio de réplica de [divisórias,](https://github.com/hdinsight/hdinsight-kafka-tools). Esta ferramenta de `kafka-reassign-partitions.sh` reequilíbrio chama a ferramenta de tal forma que cada réplica está num domínio de falha separado e domínio de atualização, tornando a cremalheira Kafka consciente e aumentando a tolerância à falha.

### <a name="scale-the-stream-processing-layer"></a>Escala a camada de processamento de fluxo

Tanto a Apache Storm como a Spark Streaming apoiam a adição de nós dos trabalhadores aos seus clusters, mesmo quando os dados estão a ser processados.

Para aproveitar os novos nós adicionados através da escalada da tempestade, você precisa reequilibrar quaisquer topoologias de tempestade iniciadas antes do tamanho do cluster ser aumentado. Este reequilíbrio pode ser feito utilizando a UI web storm ou o seu CLI. Para mais informações, consulte a documentação da [Tempestade Apache.](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)

A Apache Spark utiliza três parâmetros-chave para configurar `spark.executor.instances`o `spark.executor.cores`seu `spark.executor.memory`ambiente, dependendo dos requisitos de aplicação: , e . Um *executor* é um processo que é lançado para uma aplicação Spark. Um executor corre no nó do trabalhador e é responsável pelo execução das tarefas da aplicação. O número padrão de executores e os tamanhos dos executores para cada cluster são calculados com base no número de nós dos trabalhadores e no tamanho do nó do trabalhador. Estes números são `spark-defaults.conf`armazenados no ficheiro de cada nó de cabeça de cluster.

Estes três parâmetros podem ser configurados ao nível do cluster, para todas as aplicações que funcionam no cluster, e também podem ser especificados para cada aplicação individual. Para obter mais informações, consulte [a Gestão de recursos para clusters Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Passos seguintes

* [Crie e monitorize uma topologia de tempestade Apache em Azure HDInsight](storm/apache-storm-quickstart.md)
* [Topologias de exemplo para Apache Storm no HDInsight](storm/apache-storm-example-topology.md)
* [Introdução à Faísca Apache no HDInsight](spark/apache-spark-overview.md)
* [Introdução ao Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md)
