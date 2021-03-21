---
title: Otimização de desempenho para os clusters do HDInsight no Apache Kafka
description: Fornece uma visão geral de técnicas para otimizar cargas de trabalho Apache Kafka em Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: d24527efe6adce6f16b7c890f23c755545f5d5a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935885"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Otimização de desempenho para os clusters do HDInsight no Apache Kafka

Este artigo dá algumas sugestões para otimizar o desempenho das suas cargas de trabalho Apache Kafka em HDInsight. O foco está no ajuste da configuração do produtor e do corretor. Existem diferentes formas de medir o desempenho, e as otimizações que aplica dependerão das necessidades do seu negócio.

## <a name="architecture-overview"></a>Descrição geral da arquitetura

Os tópicos kafka são usados para organizar discos. Os registos são produzidos por produtores e consumidos por consumidores. Os produtores enviam registos aos corretores kafka, que depois armazenam os dados. Cada nó de trabalho no cluster HDInsight é um mediador Kafka.

Registos de partição de tópicos em mediadores. Quando consumir registos, pode utilizar até um consumidor por partição para alcançar o processamento paralelo dos dados.

A replicação é usada para duplicar divisórias entre nós. Isto protege contra falhas de nó (corretor). Uma única divisória entre o grupo de réplicas é designada como líder da partição. O tráfico do produtor é encaminhado para o líder de cada nó mediante a utilização do estado gerido pelo ZooKeeper.

## <a name="identify-your-scenario"></a>Identificar o seu cenário

O desempenho de Apache Kafka tem dois aspetos principais - produção e latência. A produção é a taxa máxima a que os dados podem ser processados. Maior produção é geralmente melhor. A latência é o tempo que os dados demoram a ser armazenados ou recuperados. A latência mais baixa é geralmente melhor. Encontrar o equilíbrio certo entre a produção, a latência e o custo da infraestrutura da aplicação pode ser um desafio. Os seus requisitos de desempenho provavelmente corresponderão a uma das três situações comuns seguintes, com base na necessidade de alta produção, baixa latência ou ambas:

* Alta produção, baixa latência. Este cenário requer produção elevada e baixa latência (~100 milissegundos). Um exemplo deste tipo de aplicação é a monitorização da disponibilidade de serviços.
* Alta produção, alta latência. Este cenário requer uma produção elevada (~1,5 GBps), mas pode tolerar uma maior latência (< 250 ms). Um exemplo deste tipo de aplicação é a ingestão de dados de telemetria para processos quase em tempo real, como aplicações de deteção de segurança e de deteção de intrusões.
* Baixa produção, baixa latência. Este cenário requer baixa latência (< 10 ms) para o processamento em tempo real, mas pode tolerar uma menor produção. Um exemplo deste tipo de aplicação é a ortografia online e verificações gramaticais.

## <a name="producer-configurations"></a>Configurações do produtor

As seguintes secções irão destacar algumas das propriedades de configuração mais importantes para otimizar o desempenho dos seus produtores Kafka. Para obter uma explicação detalhada de todas as propriedades de configuração, consulte [a documentação da Apache Kafka sobre as configurações do produtor.](https://kafka.apache.org/documentation/#producerconfigs)

### <a name="batch-size"></a>Tamanho do lote

Os produtores apache kafka reúnem grupos de mensagens (chamadas lotes) que são enviadas como uma unidade para serem armazenadas numa única divisória de armazenamento. Tamanho do lote significa o número de bytes que devem estar presentes antes de esse grupo ser transmitido. Aumentar o `batch.size` parâmetro pode aumentar a produção, porque reduz a sobrecarga de processamento a partir de pedidos de rede e IO. Sob a carga leve, o tamanho do lote aumentado pode aumentar a latência do envio de Kafka à medida que o produtor espera que um lote esteja pronto. Sob carga pesada, é recomendado aumentar o tamanho do lote para melhorar a produção e a latência.

### <a name="producer-required-acknowledgments"></a>O produtor exigiu reconhecimentos

A configuração exigida pelo produtor `acks` determina o número de reconhecimentos exigidos pelo líder da partição antes de um pedido de escrita ser considerado concluído. Esta definição afeta a fiabilidade dos dados e requer valores de `0` `1` , ou `-1` . O valor dos `-1` meios que um reconhecimento deve ser recebido de todas as réplicas antes da escrita ser concluída. A definição `acks = -1` fornece garantias mais fortes contra a perda de dados, mas também resulta em maior latência e menor produção. Se os requisitos de aplicação exigirem uma produção mais elevada, tente definir `acks = 0` ou `acks = 1` . Tenha em mente que não reconhecer todas as réplicas pode reduzir a fiabilidade dos dados.

### <a name="compression"></a>Compressão

Um produtor kafka pode ser configurado para comprimir mensagens antes de enviá-las para corretores. A `compression.type` definição especifica o codec de compressão a utilizar. Os codecs de compressão suportados são "gzip", "snappy" e "lz4". A compressão é benéfica e deve ser considerada se houver uma limitação na capacidade do disco.

Entre os dois codecs de compressão comumente usados, `gzip` e tem uma relação de `snappy` `gzip` compressão mais alta, o que resulta numa menor utilização do disco ao custo de uma carga de CPU mais elevada. O `snappy` codec proporciona menos compressão com menos sobrecarga de CPU. Pode decidir qual o código a utilizar com base nas limitações do disco de corretagem ou do cpu do produtor. `gzip` pode comprimir dados a uma taxa cinco vezes superior `snappy` a .

A utilização da compressão de dados aumentará o número de registos que podem ser armazenados num disco. Também pode aumentar a sobrecarga do CPU nos casos em que há um desfasamento entre os formatos de compressão que estão a ser usados pelo produtor e pelo corretor. uma vez que os dados devem ser comprimidos antes de enviar e depois descomprimidos antes do processamento.

## <a name="broker-settings"></a>Definições de corretor

As seguintes secções irão destacar algumas das configurações mais importantes para otimizar o desempenho dos seus corretores Kafka. Para obter uma explicação detalhada de todas as definições do corretor, consulte [a documentação da Apache Kafka sobre as configurações do produtor](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="number-of-disks"></a>Número de discos

Os discos de armazenamento têm IOPS limitado (Operações de entrada/saída por segundo) e bytes de leitura/escrita por segundo. Ao criar novas divisórias, a Kafka armazena cada nova divisória no disco com menos divisórias existentes para equilibrá-las através dos discos disponíveis. Apesar da estratégia de armazenamento, ao processar centenas de réplicas de partição em cada disco, Kafka pode facilmente saturar a produção de disco disponível. A troca aqui é entre a produção e o custo. Se a sua aplicação necessitar de maior produção, crie um cluster com discos mais geridos por corretor. O HDInsight não suporta atualmente a adição de discos geridos a um cluster de execução. Para obter mais informações sobre como configurar o número de discos geridos, consulte [o armazenamento e escalabilidade de Configuração para Apache Kafka em HDInsight](apache-kafka-scalability.md). Compreenda as implicações de custos do aumento do espaço de armazenamento para os nós do seu cluster.

### <a name="number-of-topics-and-partitions"></a>Número de tópicos e divisórias

Produtores de Kafka escrevem para tópicos. Os consumidores de Kafka lêem os tópicos. Um tópico está associado a um log, que é uma estrutura de dados no disco. Kafka anexa os registos de um(s) produtor(s) até ao final de um registo de tópicos. Um registo de tópicos consiste em muitas divisórias que estão espalhadas por vários ficheiros. Estes ficheiros estão, por sua vez, espalhados por vários nós de cluster Kafka. Os consumidores lêem os tópicos de Kafka na sua cadência e podem escolher a sua posição (offset) no registo de tópicos.

Cada partição Kafka é um ficheiro de registo no sistema, e os fios do produtor podem escrever em vários registos simultaneamente. Da mesma forma, uma vez que cada fio de consumo lê mensagens de uma partição, consumir de várias divisórias também é manuseado em paralelo.

O aumento da densidade de partição (o número de divisórias por corretor) adiciona uma sobrecarga relacionada com operações de metadados e por pedido de partição/resposta entre o líder da partição e os seus seguidores. Mesmo na ausência de dados que fluem, as réplicas de partição ainda recolhem dados dos líderes, o que resulta num processamento extra para envio e receber pedidos através da rede.

Para os clusters Apache Kafka 1.1 e superior em HDInsight, recomendamos que tenha um máximo de 1000 divisórias por corretor, incluindo réplicas. Aumentar o número de divisórias por corretor diminui a produção e também pode causar indisponibilidade de tópicos. Para obter mais informações sobre o suporte à partição de Kafka, consulte [o post oficial do blog Apache Kafka sobre o aumento do número de divisórias apoiadas na versão 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Para mais detalhes sobre a modificação de tópicos, consulte [Apache Kafka: modificar tópicos](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Número de réplicas

O fator de replicação mais elevado resulta em pedidos adicionais entre o líder da partição e os seguidores. Consequentemente, um fator de replicação mais elevado consome mais disco e CPU para lidar com pedidos adicionais, aumentando a latência do escrita e diminuindo a produção.

Recomendamos que utilize pelo menos uma replicação de 3x para Kafka em Azure HDInsight. A maioria das regiões de Azure tem três domínios de falha, mas em regiões com apenas dois domínios de avaria, os utilizadores devem usar a replicação 4x.

Para obter mais informações sobre a replicação, consulte [Apache Kafka: replicação](https://kafka.apache.org/documentation/#replication) e [Apache Kafka: aumento do fator de replicação](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Passos seguintes

* [Processing trillions of events per day with Apache Kafka on Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/) (Processar biliões de eventos por dia com o Apache Kafka no Azure)
* [O que é o Apache Kafka no HDInsight?](apache-kafka-introduction.md)
