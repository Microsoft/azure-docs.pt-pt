---
title: NoClassDefFoundError - Apache Spark com dados apache kafka em Azure HDInsight
description: O trabalho de streaming apache Spark que lê dados de um cluster Apache Kafka falha com um NoClassDefFoundErroror em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 4d00cbcb0151da39feb0cb015660291af544d7f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946380"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Trabalho de streaming Apache Spark que lê dados de Apache Kafka falha com NoClassDefFoundError em HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

O aglomerado Apache Spark gere um trabalho de streaming spark que lê dados de um cluster Apache Kafka. O trabalho de streaming spark falha se a compressão do fluxo Kafka for ligada. Neste caso, a aplicação Spark streaming Yarn application_1525986016285_0193 falhou, devido a erro:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Causa

Este erro pode ser causado especificando uma versão do `spark-streaming-kafka` ficheiro do frasco que é diferente da versão do cluster Kafka que está a executar.

Por exemplo, se estiver a executar uma versão de cluster Kafka 0.10.1, o seguinte comando resultará num erro:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Resolução

Utilize o comando Spark-submit com a `–packages` opção e certifique-se de que a versão do ficheiro de jarro spark-streaming-kafka é a mesma que a versão do cluster Kafka que está a executar.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]