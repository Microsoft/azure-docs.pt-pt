---
title: Introdução ao Apache Kafka no HDInsight - Azure
description: 'Saiba mais sobre o Apache Kafka no HDInsight: o que é, o que faz e onde encontrar exemplos e obter informações sobre como começar.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: 92f56f3b405470bc8ae0e9ebab2450ddc31b3c6a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77672179"
---
# <a name="what-is-apache-kafka-in-azure-hdinsight"></a>O que é Apache Kafka em Azure HDInsight

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de transmissão em fluxo distribuída de código aberto que pode ser utilizada para criar aplicações e pipelines de dados de transmissão em fluxo em tempo real. O Kafka também fornece a funcionalidade de mediador de mensagem semelhante a uma fila de mensagens, onde pode publicar e subscrever fluxos de dados nomeados.

Seguem-se as características específicas do Kafka no HDInsight:

* É um serviço gerido que fornece um processo de configuração simplificado. O resultado é uma configuração que é testada e suportada pela Microsoft.

* A Microsoft fornece um Contrato de Nível de Serviço (SLA) de 99,9% de tempo de atividade do Kafka. Para obter mais informações, veja o documento [Informações do SLA para o HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Utiliza os Managed Disks do Azure como arquivo de cópias de segurança do Kafka. Os Managed Disks podem fornecer até 16 TB de armazenamento por mediador Kafka. Para obter informações sobre a configuração de discos geridos com Kafka no HDInsight, consulte [Aumentar a escalabilidade de Apache Kafka no HDInsight](apache-kafka-scalability.md).

    Para obter mais informações sobre os discos geridos, consulte [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* O Kafka foi concebido com uma única vista dimensional de um bastidor. O Azure separa um bastidor em duas dimensões: Domínios de Atualização (UD) e Domínios de Falha (FD). A Microsoft oferece ferramentas que reequilibram as réplicas e partições do Kafka em UDs e FDs.

    Para mais informações, consulte [A elevada disponibilidade com Apache Kafka no HDInsight](apache-kafka-high-availability.md).

* O HDInsight permite-lhe alterar o número de nós de trabalho (que alojam o mediador Kafka) após a criação do cluster. O escalonamento pode ser executado a partir do portal do Azure, do Azure PowerShell e de outras interfaces de gestão do Azure. Para o Kafka, deve reequilibrar as réplicas de partições após as operações de dimensionamento. Reequilibrar partições permite ao Kafka tirar partido do novo número de nós de trabalho.

    Para mais informações, consulte [A elevada disponibilidade com Apache Kafka no HDInsight](apache-kafka-high-availability.md).

* Os registos do Monitor Azure podem ser utilizados para monitorizar o Kafka no HDInsight. O Azure Monitor regista informações de nível de máquina virtual, tais como métricas de disco e NIC, e métricas JMX de Kafka.

    Para mais informações, consulte [os registos do Apache Kafka no HDInsight](apache-kafka-log-analytics-operations-management.md).

## <a name="apache-kafka-on-hdinsight-architecture"></a>Apache Kafka na arquitetura HDInsight

O diagrama seguinte mostra uma configuração do Kafka comum que utiliza grupos de consumidores, particionamento e replicação para oferecer leitura paralela de eventos com tolerância a falhas:

![Diagrama de configuração do cluster do Kafka](./media/apache-kafka-introduction/kafka-cluster-diagram.png)

O Apache ZooKeeper gere o estado do cluster do Kafka. O Zookeeper foi concebido para transações em simultâneo, resilientes e de baixa latência.

O Kafka armazena os registos (dados) em **tópicos**. Os registos são produzidos por **produtores** e consumidos por **consumidores**. Os produtores enviam os registos para **mediadores** Kafka. Cada nó de trabalho no cluster HDInsight é um mediador Kafka.

Registos de partição de tópicos em mediadores. Quando consumir registos, pode utilizar até um consumidor por partição para alcançar o processamento paralelo dos dados.

A replicação é utilizada para duplicar as partições nos nós, ao proteger contra interrupções do nó (mediador). A partição assinalada com *(L)* no diagrama é a partição líder dessa partição específica. O tráfico do produtor é encaminhado para o líder de cada nó mediante a utilização do estado gerido pelo ZooKeeper.

## <a name="why-use-apache-kafka-on-hdinsight"></a>Porquê usar o Apache Kafka no HDInsight?

Seguem-se as tarefas comuns e os padrões que podem ser efetuados com o Kafka no HDInsight:

|Utilizar |Descrição |
|---|---|
|Replicação de dados apache kafka|Kafka fornece o utilitário MirrorMaker, que replica dados entre os clusters kafka. Para obter informações sobre a utilização do MirrorMaker, consulte [tópicos Replicate Apache Kafka com Apache Kafka no HDInsight](apache-kafka-mirroring.md).|
|Padrão de mensagens de subscrição de publicação|Kafka fornece um Produtor API para publicar registos a um tópico kafka. A API de Consumidor é utilizada ao subscrever um tópico. Para mais informações, consulte [Start with Apache Kafka no HDInsight](apache-kafka-get-started.md).|
|Processamento de fluxos|Kafka é frequentemente usado com Apache Storm ou Spark para processamento de fluxo em tempo real. O Kafka 0.10.0.0 (versão 3.5 e 3.6 do HDInsight) introduziu uma API de transmissão em fluxo que lhe permite criar soluções de transmissão em fluxo, sem precisar do Storm ou do Spark. Para mais informações, consulte [Start with Apache Kafka no HDInsight](apache-kafka-get-started.md).|
|Dimensionamento horizontal|As divisórias kafka fluem através dos nós do cluster HDInsight. Os processos de consumidor podem estar associados a partições individuais para fornecer balanceamento de carga ao consumir registos. Para mais informações, consulte [Start with Apache Kafka no HDInsight](apache-kafka-get-started.md).|
|Entrega em encomenda|Dentro de cada divisória, os registos são armazenados no fluxo na ordem em que foram recebidos. Ao associar um processo de consumidor por partição, pode garantir que os registos são processados por ordem. Para mais informações, consulte [Start with Apache Kafka no HDInsight](apache-kafka-get-started.md).|
|Mensagens|Uma vez que suporta o padrão de mensagens de subscrição de publicação, Kafka é frequentemente usado como corretor de mensagens.|
|Rastreio de atividades|Uma vez que Kafka fornece registos em ordem, pode ser usado para rastrear e recriar atividades. Por exemplo, ações do utilizador num site ou numa aplicação.|
|Agregação|Utilizando o processamento de fluxo, pode agregar informações de diferentes streams para combinar e centralizar a informação em dados operacionais.|
|Transformação|Utilizando o processamento de fluxo, pode combinar e enriquecer dados de vários tópicos de entrada em um ou mais tópicos de saída.|

## <a name="next-steps"></a>Passos seguintes

Utilize as seguintes ligações para saber como utilizar o Apache Kafka no HDInsight:

* [Quickstart: Crie Apache Kafka no HDInsight](apache-kafka-get-started.md)

* [Use Kafka com procuração REST](rest-proxy.md)

* [Tutorial: Use Apache Spark com Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Tutorial: Utilizar o Apache Storm com Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
