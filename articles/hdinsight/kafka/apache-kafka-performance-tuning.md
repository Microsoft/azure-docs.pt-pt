---
title: Otimização de desempenho para os clusters do HDInsight no Apache Kafka
description: Fornece uma visão geral das técnicas para otimizar as cargas de trabalho apache Kafka no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494921"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Otimização de desempenho para os clusters do HDInsight no Apache Kafka

Este artigo dá algumas sugestões para otimizar o desempenho das suas cargas de trabalho Apache Kafka no HDInsight. O foco está em ajustar a configuração do produtor e corretor. Existem diferentes formas de medir o desempenho, e as otimizações que aplicar dependerão das necessidades do seu negócio.

## <a name="architecture-overview"></a>Descrição geral da arquitetura

Os tópicos de Kafka são usados para organizar discos. Os registos são produzidos por produtores e consumidos por consumidores. Os produtores enviam registos aos corretores kafka, que depois armazenam os dados. Cada nó de trabalho no cluster HDInsight é um mediador Kafka.

Registos de partição de tópicos em mediadores. Quando consumir registos, pode utilizar até um consumidor por partição para alcançar o processamento paralelo dos dados.

A replicação é usada para duplicar divisórias em nódosos. Isto protege contra interrupções de nó (corretor). Uma única divisão entre o grupo de réplicas é designada como líder da partição. O tráfico do produtor é encaminhado para o líder de cada nó mediante a utilização do estado gerido pelo ZooKeeper.

## <a name="identify-your-scenario"></a>Identificar o seu cenário

O desempenho de Apache Kafka tem dois aspetos principais – a entrada e a latência. A entrada é a taxa máxima a que os dados podem ser processados. A entrada mais alta é geralmente melhor. A latência é o tempo que os dados demoram a ser armazenados ou recuperados. Latência mais baixa costuma ser melhor. Encontrar o equilíbrio certo entre a entrada, a latência e o custo da infraestrutura da aplicação pode ser um desafio. Os seus requisitos de desempenho provavelmente corresponderão a uma das três situações comuns seguintes, com base em se necessita de alta entrada, baixa latência ou ambas:

* Alta entrada, baixa latência. Este cenário requer alta entrada e baixa latência (~100 milissegundos). Um exemplo deste tipo de aplicação é a monitorização da disponibilidade de serviços.
* Alta entrada, alta latência. Este cenário requer alta entrada (~1,5 GBps), mas pode tolerar maior latência (< 250 ms). Um exemplo deste tipo de aplicação é a ingestão de dados de telemetria para processos quase em tempo real, como aplicações de segurança e deteção de intrusões.
* Baixa entrada, baixa latência. Este cenário requer baixa latência (< 10 ms) para processamento em tempo real, mas pode tolerar uma menor entrada. Um exemplo deste tipo de aplicação é a ortografia online e os controlos gramaticais.

## <a name="producer-configurations"></a>Configurações de produtores

As seguintes secções irão destacar algumas das propriedades de configuração mais importantes para otimizar o desempenho dos seus produtores de Kafka. Para obter uma explicação detalhada de todas as propriedades de configuração, consulte a [documentação apache Kafka sobre as configurações do produtor](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Tamanho do lote

Os produtores apache kafka reúnem grupos de mensagens (chamadas lotes) que são enviadas como uma unidade a ser armazenada numa única divisória de armazenamento. Tamanho do lote significa o número de bytes que devem estar presentes antes de o grupo ser transmitido. O `batch.size` aumento do parâmetro pode aumentar a entrada, porque reduz a sobrecarga de processamento dos pedidos de rede e IO. Sob carga leve, o aumento do tamanho do lote pode aumentar a latência de envio de Kafka enquanto o produtor espera que um lote esteja pronto. Sob carga pesada, é aconselhável aumentar o tamanho do lote para melhorar a produção e a latência.

### <a name="producer-required-acknowledgments"></a>Produtor exigiu reconhecimentos

A configuração `acks` necessária ao produtor determina o número de agradecimentos exigidos pelo líder da partição antes de ser considerado um pedido de escrita. Esta definição afeta a fiabilidade `0`dos `1`dados `-1`e requer valores de, ou . O valor `-1` dos meios de que um reconhecimento deve ser recebido de todas as réplicas antes da escrita estar concluída. A `acks = -1` definição fornece garantias mais fortes contra a perda de dados, mas também resulta em maior latência e menor desempenho. Se os seus requisitos de aplicação exigirem uma maior entrada, tente definir `acks = 0` ou `acks = 1`. Tenha em mente que não reconhecer todas as réplicas pode reduzir a fiabilidade dos dados.

### <a name="compression"></a>Compressão

Um produtor de Kafka pode ser configurado para comprimir mensagens antes de enviá-las para corretores. A `compression.type` definição especifica o código de compressão a utilizar. Os códigos de compressão suportados são "gzip", "snappy" e "lz4". A compressão é benéfica e deve ser considerada se houver uma limitação na capacidade do disco.

Entre os dois códigos de compressão `snappy` `gzip` comumente usados, `gzip` e, tem uma relação de compressão mais elevada, o que resulta numa utilização mais baixa do disco ao custo de uma carga de CPU mais elevada. O `snappy` codec fornece menos compressão com menos sobrecarga de CPU. Pode decidir qual o codec a utilizar com base em limitações de CPU de disco de corretor ou de produtor. `gzip`pode comprimir dados a uma `snappy`taxa cinco vezes superior à .

A utilização da compressão de dados aumentará o número de registos que podem ser armazenados num disco. Também pode aumentar a sobrecarga do CPU nos casos em que há uma incompatibilidade entre os formatos de compressão que estão a ser utilizados pelo produtor e pelo corretor. uma vez que os dados devem ser comprimidos antes de serem enviados e, em seguida, descomprimidos antes de serem processados.

## <a name="broker-settings"></a>Definições de corretor

As seguintes secções irão destacar algumas das definições mais importantes para otimizar o desempenho dos seus corretores Kafka. Para obter uma explicação detalhada de todas as configurações do corretor, consulte a [documentação apache Kafka sobre as configurações do produtor](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="number-of-disks"></a>Número de discos

Os discos de armazenamento têm IOPS limitados (Operações de Entrada/Saída por Segundo) e bytes de leitura/escrita por segundo. Ao criar novas divisórias, a Kafka armazena cada nova divisória no disco com menos divisórias existentes para as equilibrar nos discos disponíveis. Apesar da estratégia de armazenamento, ao processar centenas de réplicas de divisórias em cada disco, Kafka pode facilmente saturar a entrada de disco disponível. A troca aqui é entre a entrada e o custo. Se a sua aplicação necessitar de uma maior entrada, crie um cluster com discos mais geridos por corretor. A HDInsight não suporta atualmente adicionar discos geridos a um cluster em execução. Para obter mais informações sobre como configurar o número de discos geridos, consulte o armazenamento e a escalabilidade do [Apache Kafka no HDInsight](apache-kafka-scalability.md). Compreenda as implicações de custos do aumento do espaço de armazenamento para os nós do seu cluster.

### <a name="number-of-topics-and-partitions"></a>Número de tópicos e divisórias

Os produtores de Kafka escrevem para tópicos. Os consumidores de Kafka lêem de tópicos. Um tópico está associado a um registo, que é uma estrutura de dados no disco. Kafka anexa registos de um(s) produtor(s) ao fim de um registo tópico. Um registo tópico consiste em muitas divisórias que estão espalhadas por vários ficheiros. Estes ficheiros estão, por sua vez, espalhados por vários nós do aglomerado de Kafka. Os consumidores lêem os tópicos de Kafka na sua cadência e podem escolher a sua posição (offset) no registo de tópicos.

Cada partição kafka é um ficheiro de registo no sistema, e os fios do produtor podem escrever em vários troncos simultaneamente. Da mesma forma, uma vez que cada fio de consumo lê mensagens de uma divisória, o consumo de várias divisórias também é manuseado em paralelo.

O aumento da densidade da partilha (o número de divisórias por corretor) adiciona uma sobrecarga relacionada com operações de metadados e por pedido/resposta de partição entre o líder da partição e os seus seguidores. Mesmo na ausência de dados que fluem, as réplicas de divisórias ainda recebem dados dos líderes, o que resulta num processamento extra para envio e receção de pedidos através da rede.

Para os clusters Apache Kafka 1.1 ou superior no HDInsight, recomendamos que tenha um máximo de 1000 divisórias por corretor, incluindo réplicas. Aumentar o número de divisórias por corretor diminui a entrada e também pode causar indisponibilidade de tópicos. Para obter mais informações sobre o suporte à partilha de Kafka, consulte [a publicação oficial do blog Apache Kafka sobre o aumento do número de divisórias suportadas na versão 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Para mais detalhes sobre a modificação de tópicos, consulte [Apache Kafka: modificar tópicos](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Número de réplicas

O maior fator de replicação resulta em pedidos adicionais entre o líder da partilha e os seguidores. Consequentemente, um fator de replicação mais elevado consome mais disco e CPU para lidar com pedidos adicionais, aumentando a latência por escrito e diminuindo a sua contribuição.

Recomendamos que utilize pelo menos 3x replicação para Kafka no Azure HDInsight. A maioria das regiões de Azure tem três domínios de falha, mas em regiões com apenas dois domínios de falha, os utilizadores devem usar replicação 4x.

Para obter mais informações sobre a replicação, consulte [Apache Kafka: replicação](https://kafka.apache.org/documentation/#replication) e [Apache Kafka: fator de replicação crescente](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Passos seguintes

* [Processing trillions of events per day with Apache Kafka on Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/) (Processar biliões de eventos por dia com o Apache Kafka no Azure)
* [O que é o Apache Kafka no HDInsight?](apache-kafka-introduction.md)
