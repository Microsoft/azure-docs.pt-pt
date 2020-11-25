---
title: Streaming em escala em Azure HDInsight
description: Como utilizar o streaming de dados com clusters Apache escaláveis em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: a04ce77c7e81a3a73b87eaf5790b383dece35d86
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022722"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Transmissão em fluxo em escala no HDInsight

As grandes soluções de dados em tempo real atuam em dados que estão em movimento. Normalmente, estes dados são mais valiosos no momento da sua chegada. Se o fluxo de dados de entrada se tornar maior do que pode ser manuseado nesse momento, poderá ter de reduzir os recursos. Em alternativa, um cluster HDInsight pode escalar para atender a sua solução de streaming adicionando nós a pedido.

Numa aplicação de streaming, uma ou mais fontes de dados estão a gerar eventos (às vezes nos milhões por segundo) que precisam de ser ingeridos rapidamente sem deixar cair qualquer informação útil. Os eventos de entrada são tratados com *tampão de fluxo,* também chamado *de fila de eventos,* por um serviço como [Apache Kafka](kafka/apache-kafka-introduction.md) ou [Event Hubs.](https://azure.microsoft.com/services/event-hubs/) Depois de recolher os eventos, pode então analisar os dados utilizando um sistema de análise em tempo real dentro da camada de processamento de *fluxo,* como [Apache Storm](storm/apache-storm-overview.md) ou [Apache Spark Streaming.](spark/apache-spark-streaming-overview.md) Os dados processados podem ser armazenados em sistemas de armazenamento de longo prazo, como [o Azure Data Lake Storage,](https://azure.microsoft.com/services/storage/data-lake-storage/)e exibidos em tempo real num dashboard de inteligência empresarial, como [Power BI,](https://powerbi.microsoft.com)Tableau ou uma página web personalizada.

![Padrões de streaming Azure HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka fornece um serviço de fila de mensagens de baixa latência de alta produção, e agora faz parte do conjunto Apache da Open Source Software (OSS). Kafka usa um modelo de mensagens de publicação e subscrição e armazena streams de dados divididos com segurança num cluster distribuído e replicado. Kafka escala linearmente à medida que a produção aumenta.

Para obter mais informações, consulte [a introdução de Apache Kafka no HDInsight.](kafka/apache-kafka-introduction.md)

## <a name="apache-storm"></a>Apache Storm

Apache Storm é um sistema de computação de código aberto distribuído, tolerante a falhas, que é otimizado para o processamento de fluxos de dados em tempo real com Hadoop. A unidade principal de dados para um evento de entrada é um Tuple, que é um conjunto imutável de pares chave/valor. Uma sequência ilimitada destes Tuples forma um fluxo, que vem de um Bico. O Spout envolve uma fonte de dados de streaming (como Kafka), e emite Tuples. Uma tempestade Topologia é uma sequência de transformações nestes riachos.

Para mais informações, veja [o que é a Tempestade Apache em Azure HDInsight?](storm/apache-storm-overview.md)

## <a name="spark-streaming"></a>Transmissão em fluxo do Spark

O Spark Streaming é uma extensão ao Spark, que permite reutilizar o mesmo código que utiliza para o processamento de lotes. Pode combinar perguntas de lote e interativas na mesma aplicação. Ao contrário da Tempestade, o Spark Streaming fornece semântica de processamento. Quando usado em combinação com a [Kafka Direct API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), que garante que todos os dados kafka são recebidos pelo Spark Streaming exatamente uma vez, é possível alcançar de ponta a ponta exatamente uma vez que as garantias. Um dos pontos fortes do Spark Streaming é a sua capacidade tolerante a falhas, recuperando os nós defeituosos rapidamente quando vários nós estão sendo usados dentro do cluster.

Para mais informações, veja [o que é Apache Spark Streaming?](./spark/apache-spark-streaming-overview.md)

## <a name="scaling-a-cluster"></a>Escalar um aglomerado

Embora possa especificar o número de nós no seu cluster durante a criação, pode querer crescer ou encolher o cluster para corresponder à carga de trabalho. Todos os clusters HDInsight permitem [alterar o número de nós no cluster](hdinsight-administer-use-portal-linux.md#scale-clusters). Os clusters de faíscas podem ser eliminados sem perda de dados, uma vez que todos os dados são armazenados no Armazenamento de Azure ou no Armazenamento do Lago de Dados.

Há vantagens em dissociar tecnologias. Por exemplo, Kafka é uma tecnologia de tampão de eventos, por isso é muito intensivo e não precisa de muito poder de processamento. Em comparação, processadores de fluxo como o Spark Streaming são intensivos em computação, requerendo VMs mais potentes. Ao ter estas tecnologias dissociadas em diferentes clusters, pode escaloná-las de forma independente, utilizando melhor os VMs.

### <a name="scale-the-stream-buffering-layer"></a>Dimensione a camada de tampão de fluxo

As tecnologias de tampão de fluxo Event Hubs e Kafka usam divisórias, e os consumidores lêem dessas divisórias. A escala da entrada requer um aumento do número de divisórias, e a adição de divisórias proporciona um paralelismo crescente. No Event Hubs, a contagem de divisórias não pode ser alterada após a implementação, por isso é importante começar com a escala de destino em mente. Com Kafka, é possível [adicionar divisórias,](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion)mesmo quando Kafka está a processar dados. Kafka fornece uma ferramenta para reatribuir divisórias,  `kafka-reassign-partitions.sh` . O HDInsight fornece uma [ferramenta de reequilíbrio de réplica de partição,](https://github.com/hdinsight/hdinsight-kafka-tools)  `rebalance_rackaware.py` . Esta ferramenta de reequilíbrio chama a ferramenta de forma a `kafka-reassign-partitions.sh` que cada réplica esteja num domínio separado de falha e de atualização, tornando o rack Kafka consciente e aumentando a tolerância à falha.

### <a name="scale-the-stream-processing-layer"></a>Dimensione a camada de processamento de fluxo

Tanto o suporte da Apache Storm como do Spark Streaming adicionando nós de trabalhadores aos seus clusters, mesmo quando os dados estão a ser processados.

Para tirar partido de novos nós adicionados através da tempestade de escala, você precisa reequilibrar quaisquer topologias de tempestade iniciadas antes do aumento do tamanho do cluster. Este reequilíbrio pode ser feito utilizando a UI da teia storm ou o seu CLI. Para mais informações, consulte a [documentação da Tempestade Apache.](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)

A Apache Spark utiliza três parâmetros-chave para configurar o seu ambiente, dependendo dos requisitos de aplicação: `spark.executor.instances` `spark.executor.cores` , e `spark.executor.memory` . Um *executor* é um processo que é lançado para uma aplicação Spark. Um executor funciona no nó do trabalhador e é responsável pela execução das tarefas da aplicação. O número predefinido dos executores e os tamanhos do executor para cada cluster são calculados com base no número de nós de trabalhador e no tamanho do nó do trabalhador. Estes números são armazenados `spark-defaults.conf` no ficheiro de cada nó de cabeça de cluster.

Estes três parâmetros podem ser configurados ao nível do cluster, para todas as aplicações que funcionam no cluster, e também podem ser especificados para cada aplicação individual. Para obter mais informações, consulte [a Gestão de Recursos para clusters Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Passos seguintes

* [Criar e monitorizar uma topologia da Tempestade Apache em Azure HDInsight](storm/apache-storm-quickstart.md)
* [Topologias de exemplo para Apache Storm no HDInsight](storm/apache-storm-example-topology.md)
* [Introdução a Apache Spark em HDInsight](spark/apache-spark-overview.md)
* [Introdução ao Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md)