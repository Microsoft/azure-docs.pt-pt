---
title: Alta disponibilidade com o Apache Kafka-HDInsight do Azure
description: Saiba como garantir a elevada disponibilidade com o Apache Kafka no Azure HDInsight. Saiba como reequilibrar réplicas da partição no Kafka para que fiquem localizadas em domínios de falhas diferentes dentro da região do Azure que contém o HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: a90be471ca068869ee26fb02bba77dfdd476a44e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435297"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Elevada disponibilidade dos seus dados com o Apache Kafka no HDInsight

Saiba como configurar réplicas de partição para tópicos Apache Kafka para aproveitar a configuração de rack de hardware subjacente. Esta configuração garante a disponibilidade dos dados armazenados no Apache Kafka no HDInsight.

## <a name="fault-and-update-domains-with-apache-kafka"></a>Domínios de falha e atualização com Apache Kafka

Um domínio de falha é um agrupamento lógico de hardware subjacente num centro de dados do Azure. Cada domínio de falha partilha um comutador de rede e uma fonte de alimentação. As máquinas virtuais e os discos geridos que implementam os nós num cluster HDInsight são distribuídos por esses domínios de falha. Esta arquitetura limita o possível impacto de falhas físicas de hardware.

Cada região do Azure tem um número específico de domínios de falha. Para obter uma lista de domínios e o número de domínios de falha que contêm, consulte a documentação [Availability Sets](../../virtual-machines/windows/availability.md#availability-sets) (Conjuntos de disponibilidade).

> [!IMPORTANT]  
> O Kafka não está ciente dos domínios de falha. Quando cria um tópico no Kafka, pode armazenar todas as réplicas da partição no mesmo domínio de falha. Para resolver este problema, o HDInsight disponibiliza a [ferramenta de reequilíbrio de partições do Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

## <a name="when-to-rebalance-partition-replicas"></a>Quando deve reequilibrar as réplicas de partições

Para garantir a maior disponibilidade dos seus dados do Kafka, deve reequilibrar as réplicas de partições do seu tópico nos seguintes momentos:

* Quando é criado um novo tópico ou partição

* Quando expandir um cluster

## <a name="replication-factor"></a>Fator de replicação

> [!IMPORTANT]  
> É recomendável utilizar uma região do Azure que contenha três domínios de falha e um fator de replicação de 3.

Se tiver de utilizar uma região que contenha apenas dois domínios de falha, utilize um fator de replicação de 4 para distribuir as réplicas uniformemente entre os dois domínios de falha.

Para obter um exemplo de como criar tópicos e definir o fator de replicação, consulte o documento [Iniciar com Apache Kafka no HDInsight](apache-kafka-get-started.md) .

## <a name="how-to-rebalance-partition-replicas"></a>Como reequilibrar as réplicas de partições

Use a [ferramenta de rebalanceamento de partição Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) para reequilibrar os tópicos selecionados. Esta ferramenta deve ser executada numa sessão SSH para o nó principal do cluster do Kafka.

Para obter mais informações sobre a ligação ao HDInsight através do SSH, consulte o documento [Utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="next-steps"></a>Passos seguintes

* [Escalabilidade de Apache Kafka no HDInsight](apache-kafka-scalability.md)
* [Espelhamento com Apache Kafka no HDInsight](apache-kafka-mirroring.md)
