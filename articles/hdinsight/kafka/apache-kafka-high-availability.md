---
title: Elevada disponibilidade com o Apache Kafka - Azure HDInsight
description: Saiba como garantir a elevada disponibilidade com o Apache Kafka no Azure HDInsight. Saiba como reequilibrar réplicas da partição no Kafka para que fiquem localizadas em domínios de falhas diferentes dentro da região do Azure que contém o HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 70843c368b0446a7c0e09559fa759a3cd51912d4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721230"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Elevada disponibilidade dos seus dados com o Apache Kafka no HDInsight

Saiba como configurar réplicas de partição para tópicos do Apache Kafka tirar partido da configuração de rack do hardware subjacente. Esta configuração garante a disponibilidade dos dados armazenados no Apache Kafka no HDInsight.

## <a name="fault-and-update-domains-with-apache-kafka"></a>Domínios de falha e atualização com o Apache Kafka

Um domínio de falha é um agrupamento lógico de hardware subjacente num centro de dados do Azure. Cada domínio de falha partilha um comutador de rede e uma fonte de alimentação. As máquinas virtuais e os discos geridos que implementam os nós num cluster HDInsight são distribuídos por esses domínios de falha. Esta arquitetura limita o possível impacto de falhas físicas de hardware.

Cada região do Azure tem um número específico de domínios de falha. Para obter uma lista de domínios e o número de domínios de falha que contêm, consulte a documentação [Availability Sets](../../virtual-machines/windows/regions-and-availability.md#availability-sets) (Conjuntos de disponibilidade).

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

Para obter um exemplo de criação de tópico e definição do fator de replicação, consulte a [introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md) documento.

## <a name="how-to-rebalance-partition-replicas"></a>Como reequilibrar as réplicas de partições

Utilize o [ferramenta de reequilíbrio de partições do Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) para reequilibrar os tópicos selecionados. Esta ferramenta deve ser executada numa sessão SSH para o nó principal do cluster do Kafka.

Para obter mais informações sobre a ligação ao HDInsight através do SSH, consulte o documento [Utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="next-steps"></a>Passos Seguintes

* [Escalabilidade do Apache Kafka no HDInsight](apache-kafka-scalability.md)
* [Espelhamento com o Apache Kafka no HDInsight](apache-kafka-mirroring.md)
