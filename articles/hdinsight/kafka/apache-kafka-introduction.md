---
title: Introdução ao Apache Kafka no HDInsight - Azure
description: 'Saiba mais sobre o Apache Kafka no HDInsight: O que é, o que faz e onde encontrar exemplos e obter informações de introdução.'
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/13/2019
ms.openlocfilehash: 7cf83af52d5bedee5b4d57ee1b4dda2fb34d1b3f
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960101"
---
# <a name="what-is-apache-kafka-in-azure-hdinsight"></a>O que é Apache Kafka no Azure HDInsight

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de transmissão em fluxo distribuída de código aberto que pode ser utilizada para criar aplicações e pipelines de dados de transmissão em fluxo em tempo real. O Kafka também fornece a funcionalidade de mediador de mensagem semelhante a uma fila de mensagens, onde pode publicar e subscrever fluxos de dados nomeados. 

Seguem-se as características específicas do Kafka no HDInsight:

* É um serviço gerido que fornece um processo de configuração simplificado. O resultado é uma configuração que é testada e suportada pela Microsoft.

* A Microsoft fornece um Contrato de Nível de Serviço (SLA) de 99,9% de tempo de atividade do Kafka. Para obter mais informações, veja o documento [Informações do SLA para o HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Utiliza os Managed Disks do Azure como arquivo de cópias de segurança do Kafka. Os Managed Disks podem fornecer até 16 TB de armazenamento por mediador Kafka. Para obter informações sobre como configurar discos gerenciados com o Kafka no HDInsight, consulte [aumentar a escalabilidade de Apache Kafka no hdinsight](apache-kafka-scalability.md).

    Para obter mais informações sobre os discos geridos, consulte [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* O Kafka foi concebido com uma única vista dimensional de um bastidor. O Azure separa um bastidor em duas dimensões: Domínios de Atualização (UD) e Domínios de Falha (FD). A Microsoft oferece ferramentas que reequilibram as réplicas e partições do Kafka em UDs e FDs. 

    Para obter mais informações, consulte [alta disponibilidade com o Apache Kafka no HDInsight](apache-kafka-high-availability.md).

* O HDInsight permite-lhe alterar o número de nós de trabalho (que alojam o mediador Kafka) após a criação do cluster. O escalonamento pode ser executado a partir do portal do Azure, do Azure PowerShell e de outras interfaces de gestão do Azure. Para o Kafka, deve reequilibrar as réplicas de partições após as operações de dimensionamento. Reequilibrar partições permite ao Kafka tirar partido do novo número de nós de trabalho.

    Para obter mais informações, consulte [alta disponibilidade com o Apache Kafka no HDInsight](apache-kafka-high-availability.md).

* Azure Monitor logs podem ser usados para monitorar o Kafka no HDInsight. Os logs de Azure Monitor superfícies informações de nível de máquina virtual, como métricas de disco e NIC, e métricas JMX de Kafka.

    Para obter mais informações, consulte [analisar logs para Apache Kafka no HDInsight](apache-kafka-log-analytics-operations-management.md).

### <a name="apache-kafka-on-hdinsight-architecture"></a>Apache Kafka na arquitetura do HDInsight

O diagrama seguinte mostra uma configuração do Kafka comum que utiliza grupos de consumidores, particionamento e replicação para oferecer leitura paralela de eventos com tolerância a falhas:

![Diagrama de configuração do cluster do Kafka](./media/apache-kafka-introduction/kafka-cluster-diagram.png)

O Apache ZooKeeper gere o estado do cluster do Kafka. O Zookeeper foi concebido para transações em simultâneo, resilientes e de baixa latência. 

O Kafka armazena os registos (dados) em **tópicos**. Os registos são produzidos por **produtores** e consumidos por **consumidores**. Os produtores enviam os registos para **mediadores** Kafka. Cada nó de trabalho no cluster HDInsight é um mediador Kafka. 

Registos de partição de tópicos em mediadores. Quando consumir registos, pode utilizar até um consumidor por partição para alcançar o processamento paralelo dos dados.

A replicação é utilizada para duplicar as partições nos nós, ao proteger contra interrupções do nó (mediador). A partição assinalada com *(L)* no diagrama é a partição líder dessa partição específica. O tráfico do produtor é encaminhado para o líder de cada nó mediante a utilização do estado gerido pelo ZooKeeper.

## <a name="why-use-apache-kafka-on-hdinsight"></a>Por que usar o Apache Kafka no HDInsight?

Seguem-se as tarefas comuns e os padrões que podem ser efetuados com o Kafka no HDInsight:

* **Replicação de dados de Apache Kafka**: O Kafka fornece o utilitário MirrorMaker, que replica dados entre clusters Kafka.

    Para obter informações sobre como usar o MirrorMaker, consulte [replicar tópicos de Apache Kafka com Apache Kafka no HDInsight](apache-kafka-mirroring.md).

* **Padrão de mensagens de publicação/assinatura**: O Kafka fornece uma API de produtor para publicar registros em um tópico do Kafka. A API de Consumidor é utilizada ao subscrever um tópico.

    Para obter mais informações, consulte [Iniciar com Apache Kafka no HDInsight](apache-kafka-get-started.md).

* **Processamento de fluxo**: O Kafka é geralmente usado com Apache Storm ou Spark para processamento de fluxo em tempo real. O Kafka 0.10.0.0 (versão 3.5 e 3.6 do HDInsight) introduziu uma API de transmissão em fluxo que lhe permite criar soluções de transmissão em fluxo, sem precisar do Storm ou do Spark.

    Para obter mais informações, consulte [Iniciar com Apache Kafka no HDInsight](apache-kafka-get-started.md).

* **Escala horizontal**: O Kafka particiona fluxos entre os nós no cluster HDInsight. Os processos de consumidor podem estar associados a partições individuais para fornecer balanceamento de carga ao consumir registos.

    Para obter mais informações, consulte [Iniciar com Apache Kafka no HDInsight](apache-kafka-get-started.md).

* **Entrega em ordem**: Em cada partição, os registros são armazenados no fluxo na ordem em que foram recebidos. Ao associar um processo de consumidor por partição, pode garantir que os registos são processados por ordem.

    Para obter mais informações, consulte [Iniciar com Apache Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="use-cases"></a>Casos de utilização

* **Mensagens**: Como ele dá suporte ao padrão de mensagem de publicação/assinatura, o Kafka geralmente é usado como um agente de mensagem.

* **Acompanhamento de atividades**: Como o Kafka fornece registro em log em ordem de registros, ele pode ser usado para rastrear e recriar atividades. Por exemplo, ações do utilizador num site ou numa aplicação.

* **Agregação**: Usando o processamento de fluxo, você pode agregar informações de diferentes fluxos para combinar e centralizar as informações em dados operacionais.

* **Transformação**: Usando o processamento de fluxo, você pode combinar e enriquecer dados de vários tópicos de entrada em um ou mais tópicos de saída.

## <a name="next-steps"></a>Passos Seguintes

Utilize as seguintes ligações para saber como utilizar o Apache Kafka no HDInsight:

* [Quickstart: Criar Apache Kafka no HDInsight](apache-kafka-get-started.md)

* [Tutorial: Usar Apache Spark com Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Tutorial: Usar Apache Storm com Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
