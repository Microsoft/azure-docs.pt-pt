---
title: Logs de Azure Monitor para Apache Kafka-Azure HDInsight
description: Saiba como usar os logs de Azure Monitor para analisar logs de Apache Kafka cluster no Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 44eea1bc6390e743aff104550e5b6d7e97c45929
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960121"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analisar logs para Apache Kafka no HDInsight

Saiba como usar os logs de Azure Monitor para analisar os logs gerados pelo Apache Kafka no HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Habilitar logs de Azure Monitor para Apache Kafka

As etapas para habilitar os logs de Azure Monitor para o HDInsight são as mesmas para todos os clusters HDInsight. Use os links a seguir para entender como criar e configurar os serviços necessários:

1. Crie um espaço de trabalho Log Analytics. Para obter mais informações, consulte o documento [logs no Azure monitor](../../azure-monitor/platform/data-platform-logs.md) .

2. Crie um Kafka no cluster HDInsight. Para obter mais informações, consulte o documento [Iniciar com Apache Kafka no HDInsight](apache-kafka-get-started.md) .

3. Configure o cluster Kafka para usar logs de Azure Monitor. Para obter mais informações, consulte o documento [usar Azure monitor logs para monitorar o HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) .

> [!IMPORTANT]  
> Pode levar cerca de 20 minutos antes que os dados estejam disponíveis para logs de Azure Monitor.

## <a name="query-logs"></a>Logs de consulta

1. No [portal do Azure](https://portal.azure.com), selecione seu espaço de trabalho do log Analytics.

2. No menu à esquerda, em **geral**, selecione **logs**. A partir daqui, você pode pesquisar os dados coletados de Kafka. Insira uma consulta na janela de consulta e, em seguida, selecione **executar**. Veja a seguir algumas pesquisas de exemplo:

* Uso do disco:

    ```kusto
    Perf 
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Uso da CPU:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Mensagens de entrada por segundo:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Bytes de entrada por segundo:

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Bytes de saída por segundo:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    > [!IMPORTANT]  
    > Substitua os valores da consulta pelas informações específicas do cluster. Por exemplo, `ClusterName_s` deve ser definido como o nome do cluster. `HostName_s`deve ser definido como o nome de domínio de um nó de trabalho no cluster.
    
    Você também pode inserir `*` para pesquisar todos os tipos registrados. Atualmente, os seguintes logs estão disponíveis para consultas:
    
    | Tipo de registo | Descrição |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Servidor do Kafka Broker. log |
    | \_kafkacontroller\_CL de log | Controlador do agente Kafka. log |
    | métricas\_KafkaCL\_ | Métricas JMX do Kafka |
    
    ![Imagem da pesquisa de uso da CPU](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)
 
## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre Azure Monitor, consulte [Azure monitor visão geral](../../log-analytics/log-analytics-get-started.md)e [consultar logs de Azure monitor para monitorar clusters HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Para obter mais informações sobre como trabalhar com Apache Kafka, consulte os seguintes documentos:

* [Apache Kafka de espelho entre clusters HDInsight](apache-kafka-mirroring.md)
* [Aumentar a escalabilidade de Apache Kafka no HDInsight](apache-kafka-scalability.md)
* [Usar streaming de Apache Spark (DStreams) com Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Usar Apache Spark streaming estruturado com Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
