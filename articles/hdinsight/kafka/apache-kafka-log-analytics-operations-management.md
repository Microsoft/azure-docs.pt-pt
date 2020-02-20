---
title: Registos do Monitor Azure para Apache Kafka - Azure HDInsight
description: Aprenda a utilizar os registos do Monitor Azure para analisar os registos do cluster Apache Kafka no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471185"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analise os registos de Apache Kafka no HDInsight

Aprenda a utilizar os registos do Monitor Azure para analisar os registos gerados por Apache Kafka no HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Localização de registos

Os registos apache Kafka no cluster estão localizados em `/var/log/kafka`. Os troncos de Kafka não são salvos ou persistidos em ciclos de vida de cluster, independentemente de serem usados discos geridos. A tabela seguinte mostra os registos disponíveis.

|Registar |Descrição |
|---|---|
|kafka.out|stdout e stderr do processo Kafka. Encontrará saques de arranque e encerramento de Kafka neste ficheiro.|
|servidor.log|O registo principal do servidor Kafka. Todos os registos de corretorkafka acabam aqui.|
|controlador.log|Registos do controlador se o corretor estiver a agir como controlador.|
|statechange.log|Todos os eventos de mudança de estado para corretores estão registados neste ficheiro.|
|kafka-gc.log|Estatísticas da Kafka Garbage Collection.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Ativar os registos do Monitor Azure para Apache Kafka

Os passos para ativar os registos do Monitor Azure para o HDInsight são os mesmos para todos os clusters HDInsight. Utilize os seguintes links para entender como criar e configurar os serviços necessários:

1. Criar um espaço de trabalho log Analytics. Para mais informações, consulte os registos no documento [Do Monitor Azure.](../../azure-monitor/platform/data-platform-logs.md)

2. Crie um Kafka no cluster HDInsight. Para mais informações, consulte o Início com Apache Kafka no documento [HDInsight.](apache-kafka-get-started.md)

3. Configure o cluster Kafka para utilizar os registos do Monitor Azure. Para mais informações, consulte os registos do Monitor Use Azure para monitorizar o documento [HDInsight.](../hdinsight-hadoop-oms-log-analytics-tutorial.md)

> [!IMPORTANT]  
> Pode demorar cerca de 20 minutos até que os dados sejam disponibilizados para os registos do Monitor Azure.

## <a name="query-logs"></a>Registos de consultas

1. A partir do [portal Azure,](https://portal.azure.com)selecione o seu espaço de trabalho Log Analytics.

2. A partir do menu esquerdo, em **Geral,** selecione **Registos**. A partir daqui, pode pesquisar os dados recolhidos de Kafka. Introduza uma consulta na janela de consulta e, em seguida, selecione **Executar**. Seguem-se algumas pesquisas de exemplo:

* Utilização do disco:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Utilização do CPU:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Mensagens de entrada por segundo: (Substitua `your_kafka_cluster_name` pelo nome do cluster.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Bytes de entrada por segundo: (Substitua `wn0-kafka` por um nome de anfitrião do nó operário.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Bytes de saída por segundo: (Substitua `your_kafka_cluster_name` pelo nome do cluster.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Também pode entrar `*` para pesquisar todos os tipos registados. Atualmente estão disponíveis os seguintes registos para consultas:

    | Tipo de log | Descrição |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Controlador de corretor kafka.log |
    | métricas\_kafka\_CL | Métricas Kafka JMX |

    ![Apache kafka log analytics cpu uso](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Monitor Azure, consulte a [visão geral do Monitor Azure](../../log-analytics/log-analytics-get-started.md)e os [registos do Monitor DeRy Azure para monitorizar os clusters HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Para obter mais informações sobre o trabalho com Apache Kafka, consulte os seguintes documentos:

* [Mirror Apache Kafka entre clusters HDInsight](apache-kafka-mirroring.md)
* [Aumente a escala de Apache Kafka no HDInsight](apache-kafka-scalability.md)
* [Use o streaming Apache Spark (DStreams) com Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Use apache Spark streaming estruturado com Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
