---
title: Otimização de desempenho para os clusters do Apache Kafka HDInsight
description: Fornece uma descrição geral das técnicas para otimizar cargas de trabalho do Apache Kafka no HDInsight do Azure.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 903cd8921801ffb47dd73f48e507f30aa0b6dccc
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373154"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Otimização de desempenho para os clusters do Apache Kafka HDInsight

Este artigo fornece algumas sugestões para otimizar o desempenho das suas cargas de trabalho do Apache Kafka no HDInsight. O foco é sobre o ajuste de produtor e de configuração do Mediador. Existem várias formas de avaliar o desempenho e as otimizações que aplicar dependerá de suas necessidades empresariais.

## <a name="architecture-overview"></a>Descrição geral da arquitetura

Tópicos do Kafka são utilizados para organizar os registos. Registos são produzidos pelos produtores e consumidos pelos consumidores. Os produtores enviam registos para mediadores Kafka, o que, em seguida, armazenam os dados. Cada nó de trabalho no cluster HDInsight é um mediador Kafka.

Registos de partição de tópicos em mediadores. Quando consumir registos, pode utilizar até um consumidor por partição para alcançar o processamento paralelo dos dados.

A replicação é utilizada para duplicar partições entre nós. Esta ação protege contra falhas de nó (Mediador). Uma única partição entre o grupo de réplicas é designada como o coordenador de partição. O tráfico do produtor é encaminhado para o líder de cada nó mediante a utilização do estado gerido pelo ZooKeeper.

## <a name="identify-your-scenario"></a>Identificar o seu cenário

Desempenho do Apache Kafka tem dois aspectos principais – o débito e latência. O débito é a velocidade máxima a que os dados podem ser processados. Um débito mais elevado, geralmente é melhor. A latência é o tempo que demora para os dados sejam armazenados ou recuperados. Latência mais baixa, geralmente é melhor. Encontrar o equilíbrio entre débito, latência e o custo da infraestrutura da aplicação pode ser um desafio. Seus requisitos de desempenho provavelmente corresponde a um das seguintes três situações comuns, com base em se necessita de alto débito, baixa latência ou ambas:

* Alto débito, baixa latência. Este cenário requer um débito elevado e baixa latência (~ 100 milissegundos). Um exemplo desse tipo de aplicação é do serviço de monitorização de disponibilidade.
* Alto débito, latência elevada. Este cenário requer o alto débito (~1.5 GBps), mas pode tolerar a latência superior (< 250 ms). Um exemplo deste tipo de aplicativo é a ingestão de dados de telemetria para perto de processos em tempo real, como aplicações de deteção de intrusões e segurança.
* Débito de baixa, baixa latência. Este cenário requer a baixa latência (< 10 ms) para processamento em tempo real, mas pode tolerar o débito mais baixo. Um exemplo desse tipo de aplicação é verificações de ortografia e gramática online.

## <a name="producer-configurations"></a>Configurações de produtor

As secções seguintes, irão realçar algumas das propriedades de configuração mais importantes para otimizar o desempenho de seu produtores de Kafka. Para obter uma explicação detalhada de todas as propriedades de configuração, consulte [documentação do Apache Kafka em configurações de produtor](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Tamanho do batch

Os produtores de Apache Kafka organizar grupos de mensagens (denominadas lotes) que são enviadas como uma unidade sejam armazenados numa partição de armazenamento única. Tamanho do lote significa que o número de bytes que tem de estar presente antes desse grupo é transmitido. Aumentar o `batch.size` parâmetro pode aumentar o débito, dado que reduz o processamento de sobrecarga de rede e pedidos de e/s. Sob carga leve, tamanho do lote maior pode aumentar latência de envio do Kafka, conforme o produtor aguarda um lote estar preparado. Com muita carga, é recomendado para aumentar o tamanho do lote para melhorar o débito e latência.

### <a name="producer-required-acknowledgements"></a>Confirmações de produtor necessário

O produtor necessário `acks` configuração determina o número de confirmações necessária para o líder de partição antes de uma solicitação de gravação é considerada concluída. Esta definição afeta a fiabilidade dos dados e valores de tempo demora `0`, `1`, ou `-1`. O valor de `-1` significa que uma confirmação têm de ser recebida de todas as réplicas antes da operação de escrita é concluída. Definição `acks = -1` oferece mais garantias contra a perda de dados, mas também os resultados na latência superior e o débito mais baixo. Se os requisitos da aplicação exigem um débito mais elevado, tente definição `acks = 0` ou `acks = 1`. Tenha em mente, o que pode reduzir a fiabilidade dos dados não reconhecê-todas as réplicas.

### <a name="compression"></a>Compressão

Um produtor do Kafka pode ser configurado para comprimir mensagens antes de enviá-los para corretores. O `compression.type` definição especifica o codec de compressão a ser utilizado. Codecs de compressão suportados são "gzip," "snappy," e "lz4." A compressão é benéfico e deve ser considerada se houver uma limitação à capacidade do disco.

Entre os dois codecs mais usados de compressão, `gzip` e `snappy`, `gzip` tem uma taxa de compressão superior, o que resulta na utilização do disco inferior ao custo da carga de CPU superior. O `snappy` codec oferece menos compressão com menos overhead de CPU. Pode decidir que codec para utilizar com base nos limites de CPU de disco ou produtor do Mediador. `gzip` Pode comprimir os dados a uma taxa de cinco vezes maior do que `snappy`.

A utilização de compressão de dados irá aumentar o número de registos que podem ser armazenados num disco. Ele também pode aumentar CPU sobrecarga nos casos em que existe um erro de correspondência entre os formatos de compactação a ser utilizados pelo produtor e o mediador. como os dados devem ser comprimidos antes de enviar e, em seguida, descompactados antes do processamento.

## <a name="broker-settings"></a>Definições de Mediador

As secções seguintes, irão realçar algumas das configurações mais importantes para otimizar o desempenho de seu mediadores Kafka. Para obter uma explicação detalhada de todas as definições de Mediador, consulte [documentação do Apache Kafka em configurações de produtor](https://kafka.apache.org/documentation/#producerconfigs).


### <a name="number-of-disks"></a>Número de discos

Discos de armazenamento limitaram IOPS (entrada/saída operações por segundo) e leitura/gravação de bytes por segundo. Ao criar novas partições, o Kafka armazena cada nova partição no disco com o menor número de partições existentes para equilibrá-los em todos os discos disponíveis. Apesar de estratégia de armazenamento, ao processar centenas de réplicas da partição em cada disco, o Kafka pode saturar facilmente o débito de disco disponível. É aqui o compromisso entre débito e o custo. Se a sua aplicação requeira uma saída maior, criar um cluster com mais managed disks por Mediador. HDInsight não suporta atualmente a adição de discos geridos para um cluster em execução. Para obter mais informações sobre como configurar o número de discos geridos, consulte [configurar o armazenamento e escalabilidade para o Apache Kafka no HDInsight](apache-kafka-scalability.md). Compreenda as implicações de custo de cada vez maior espaço de armazenamento para os nós do cluster.

### <a name="number-of-topics-and-partitions"></a>Número de tópicos e partições

Os produtores de Kafka escrevem tópicos. Consumidores de Kafka ler a partir de tópicos. Um tópico está associado um registo, o que é uma estrutura de dados no disco. Kafka acrescenta registos a partir de um producer(s) ao final de um registo de tópico. Um registo de tópico consiste em várias partições que estão distribuídas por vários ficheiros. Estes ficheiros são, por sua vez, distribuídos por vários nós de cluster do Kafka. Os consumidores de leem a partir de tópicos do Kafka em sua cadência e e possa escolher a respetiva posição (deslocamento) no registo de tópico.

Cada partição de Kafka é um ficheiro de registo do sistema e os threads de produtor podem escrever para vários registos simultaneamente. Da mesma forma, uma vez que cada thread de consumidor lê mensagens de uma partição, consumindo de várias partições é processada em paralelo bem.

Aumentar a densidade de partição (o número de partições por Mediador) adiciona uma sobrecarga para operações de metadados e por partição. o pedido/resposta entre o líder de partição e seus seguidores relacionada. Até mesmo na ausência de fluxo através de dados, as réplicas de partições ainda buscar dados líderes, o que resulta num processamento extra para enviar e receber pedidos através da rede.

Para o Apache Kafka clusters 1.1 e acima no HDInsight, recomendamos que tenha um máximo de partições de 1000 por agente, incluindo as réplicas. Aumentar o número de partições por Mediador diminui a taxa de transferência e também pode fazer com que a indisponibilidade de tópico. Para obter mais informações sobre o suporte de partições do Kafka, consulte [a mensagem de blogue oficial do Apache Kafka no aumento no número de partições suportados na versão 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Para obter detalhes sobre como modificar os tópicos, consulte [Apache Kafka: modificar tópicos](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Número de réplicas

Fator de replicação mais alto resulta em pedidos adicionais entre o líder de partição e seguidores. Consequentemente, um fator de replicação superior consome mais disco e CPU para processar pedidos adicionais, aumentando a escrever a latência e de reduzir o débito.

Recomendamos que utilize replicação de x, pelo menos, 3 para o Kafka no HDInsight do Azure. Regiões mais do Azure tem três domínios de falha, mas em regiões com apenas dois domínios de falha, os utilizadores devem utilizar a replicação de x 4.

Para obter mais informações sobre a replicação, consulte [Apache Kafka: replicação](https://kafka.apache.org/documentation/#replication) e [Apache Kafka: aumentar o fator de replicação](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Passos Seguintes

* [Processing trillions of events per day with Apache Kafka on Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/) (Processar biliões de eventos por dia com o Apache Kafka no Azure)
* [O que é o Apache Kafka no HDInsight?](apache-kafka-introduction.md)
