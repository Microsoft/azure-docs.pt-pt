---
title: Introdução ao Apache Kafka no HDInsight - Azure
description: 'Saiba mais sobre o Apache Kafka no HDInsight: o que é, o que faz e onde encontrar exemplos e obter informações sobre como começar.'
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: fbe4041b74f90b2ef8a9ef4384e83c7aa2d56461
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939275"
---
# <a name="what-is-apache-kafka-in-azure-hdinsight"></a>O que é Apache Kafka em Azure HDInsight

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de transmissão em fluxo distribuída de código aberto que pode ser utilizada para criar aplicações e pipelines de dados de transmissão em fluxo em tempo real. O Kafka também fornece a funcionalidade de mediador de mensagem semelhante a uma fila de mensagens, onde pode publicar e subscrever fluxos de dados nomeados.

Seguem-se as características específicas do Kafka no HDInsight:

* É um serviço gerido que fornece um processo de configuração simplificado. O resultado é uma configuração que é testada e suportada pela Microsoft.

* A Microsoft fornece um Contrato de Nível de Serviço (SLA) de 99,9% de tempo de atividade do Kafka. Para obter mais informações, veja o documento [Informações do SLA para o HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Utiliza os Managed Disks do Azure como arquivo de cópias de segurança do Kafka. Os Managed Disks podem fornecer até 16 TB de armazenamento por mediador Kafka. Para obter informações sobre a configuração de discos geridos com Kafka em HDInsight, consulte [Aumentar a escalabilidade de Apache Kafka em HDInsight](apache-kafka-scalability.md).

    Para obter mais informações sobre os discos geridos, consulte [Azure Managed Disks](../../virtual-machines/managed-disks-overview.md).

* O Kafka foi concebido com uma única vista dimensional de um bastidor. O Azure separa um bastidor em duas dimensões: Domínios de Atualização (UD) e Domínios de Falha (FD). A Microsoft oferece ferramentas que reequilibram as réplicas e partições do Kafka em UDs e FDs.

    Para mais informações, consulte [Alta disponibilidade com Apache Kafka em HDInsight.](apache-kafka-high-availability.md)

* O HDInsight permite-lhe alterar o número de nós de trabalho (que alojam o mediador Kafka) após a criação do cluster. A escala ascendente pode ser realizada a partir do portal Azure PowerShell e outras interfaces de gestão Azure. Para o Kafka, deve reequilibrar as réplicas de partições após as operações de dimensionamento. Reequilibrar partições permite ao Kafka tirar partido do novo número de nós de trabalho.

   HDInsight Kafka não suporta a escala descendente ou diminui o número de corretores dentro de um cluster. Se for feita uma tentativa de diminuir o número de nós, é devolvido um `InvalidKafkaScaleDownRequestErrorCode` erro.

    Para mais informações, consulte [Alta disponibilidade com Apache Kafka em HDInsight.](apache-kafka-high-availability.md)

* Os registos do Monitor Azure podem ser utilizados para monitorizar Kafka em HDInsight. Os registos do Azure Monitor superam informações de nível de máquina virtual, tais como métricas de disco e NIC, e métricas JMX de Kafka.

    Para obter mais informações, consulte [os registos de Apache Kafka em HDInsight](apache-kafka-log-analytics-operations-management.md).

## <a name="apache-kafka-on-hdinsight-architecture"></a>Apache Kafka na arquitetura HDInsight

O diagrama seguinte mostra uma configuração do Kafka comum que utiliza grupos de consumidores, particionamento e replicação para oferecer leitura paralela de eventos com tolerância a falhas:

![Diagrama de configuração do cluster do Kafka](./media/apache-kafka-introduction/kafka-cluster-diagram.png)

O Apache ZooKeeper gere o estado do cluster do Kafka. O Zookeeper foi concebido para transações em simultâneo, resilientes e de baixa latência.

O Kafka armazena os registos (dados) em **tópicos**. Os registos são produzidos por **produtores** e consumidos por **consumidores**. Os produtores enviam os registos para **mediadores** Kafka. Cada nó de trabalho no cluster HDInsight é um mediador Kafka.

Registos de partição de tópicos em mediadores. Quando consumir registos, pode utilizar até um consumidor por partição para alcançar o processamento paralelo dos dados.

A replicação é utilizada para duplicar as partições nos nós, ao proteger contra interrupções do nó (mediador). A partição assinalada com *(L)* no diagrama é a partição líder dessa partição específica. O tráfico do produtor é encaminhado para o líder de cada nó mediante a utilização do estado gerido pelo ZooKeeper.

## <a name="why-use-apache-kafka-on-hdinsight"></a>Porquê usar Apache Kafka na HDInsight?

Seguem-se as tarefas comuns e os padrões que podem ser efetuados com o Kafka no HDInsight:

|Utilização |Descrição |
|---|---|
|Replicação dos dados de Apache Kafka|Kafka fornece o utilitário MirrorMaker, que replica dados entre clusters Kafka. Para obter informações sobre a utilização do MirrorMaker, consulte [os tópicos De Replicar Apache Kafka com Apache Kafka em HDInsight](apache-kafka-mirroring.md).|
|Padrão de mensagens de subscrição de publicação|Kafka fornece uma API de produtor para publicar registos para um tema Kafka. A API de Consumidor é utilizada ao subscrever um tópico. Para obter mais informações, consulte [Start with Apache Kafka em HDInsight](apache-kafka-get-started.md).|
|Processamento de fluxos|Kafka é frequentemente usado com Apache Storm ou Spark para processamento de fluxo em tempo real. O Kafka 0.10.0.0 (versão 3.5 e 3.6 do HDInsight) introduziu uma API de transmissão em fluxo que lhe permite criar soluções de transmissão em fluxo, sem precisar do Storm ou do Spark. Para obter mais informações, consulte [Start with Apache Kafka em HDInsight](apache-kafka-get-started.md).|
|Dimensionamento horizontal|As divisórias kafka fluem através dos nós no aglomerado HDInsight. Os processos de consumidor podem estar associados a partições individuais para fornecer balanceamento de carga ao consumir registos. Para obter mais informações, consulte [Start with Apache Kafka em HDInsight](apache-kafka-get-started.md).|
|Entrega em encomenda|Dentro de cada divisória, os registos são armazenados no fluxo na ordem em que foram recebidos. Ao associar um processo de consumidor por partição, pode garantir que os registos são processados por ordem. Para obter mais informações, consulte [Start with Apache Kafka em HDInsight](apache-kafka-get-started.md).|
|Mensagens|Uma vez que suporta o padrão de mensagem de publicação- subscrição, Kafka é frequentemente usado como um corretor de mensagens.|
|Rastreio de atividade|Uma vez que a Kafka fornece registos in-order, pode ser usado para rastrear e recriar atividades. Por exemplo, ações do utilizador num site ou numa aplicação.|
|Agregação|Utilizando o processamento de fluxo, pode agregar informações de diferentes fluxos para combinar e centralizar a informação em dados operacionais.|
|Transformação|Utilizando o processamento de fluxo, pode combinar e enriquecer dados de vários tópicos de entrada em um ou mais tópicos de saída.|

## <a name="next-steps"></a>Próximos passos

Utilize as seguintes ligações para saber como utilizar o Apache Kafka no HDInsight:

* [Quickstart: Criar Apache Kafka em HDInsight](apache-kafka-get-started.md)

* [Use Kafka com proxy REST](rest-proxy.md)

* [Tutorial: Use Apache Spark com Apache Kafka em HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Tutorial: Utilizar o Apache Storm com Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
