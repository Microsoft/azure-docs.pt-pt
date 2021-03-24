---
title: Registos do Monitor Azure para Apache Kafka - Azure HDInsight
description: Aprenda a usar registos do Azure Monitor para analisar registos do cluster Apache Kafka no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 74db121c33864ee72ad984b49c8fa43afa0f598c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870522"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analyze logs for Apache Kafka on HDInsight (Analisar registos do Apache Kafka no HDInsight)

Aprenda a usar registos do Azure Monitor para analisar os registos gerados por Apache Kafka no HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Localização dos registos

Os troncos Apache Kafka no aglomerado estão localizados em `/var/log/kafka` . Os registos kafka não são salvos ou persistidos em ciclos de vida do cluster, independentemente de os discos geridos forem utilizados. A tabela a seguir mostra os registos disponíveis.

|Registo |Descrição |
|---|---|
|kafka.out|stdout e stderr do processo Kafka. Você vai encontrar logs de arranque e encerramento de Kafka neste ficheiro.|
|servidor.log|O registo principal do servidor Kafka. Todos os registos de corretores kafka acabam aqui.|
|controlador.log|O controlador regista registos se o corretor estiver a agir como controlador.|
|mudança de estado.log|Todos os eventos de alteração de estado para corretores são registados neste ficheiro.|
|kafka-gc.log|Estatísticas da Coleção de Lixo de Kafka.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Ativar registos do Monitor Azure para Apache Kafka

Os passos para permitir os registos do Monitor Azure para HDInsight são os mesmos para todos os clusters HDInsight. Utilize os seguintes links para entender como criar e configurar os serviços necessários:

1. Criar uma área de trabalho do Log Analytics. Para mais informações, consulte o documento ['Logs in Azure Monitor'.](../../azure-monitor/logs/data-platform-logs.md)

2. Crie um Kafka no cluster HDInsight. Para mais informações, consulte o Início com Apache Kafka no documento [HDInsight.](apache-kafka-get-started.md)

3. Configure o cluster Kafka para utilizar registos do Monitor Azure. Para obter mais informações, consulte os [registos do Monitor de Utilização Azure para monitorizar o documento HDInsight.](../hdinsight-hadoop-oms-log-analytics-tutorial.md)

> [!IMPORTANT]  
> Pode demorar cerca de 20 minutos até que os dados estão disponíveis para registos do Azure Monitor.

## <a name="query-logs"></a>Troncos de consulta

1. A partir do [portal Azure,](https://portal.azure.com)selecione o seu espaço de trabalho Log Analytics.

2. A partir do menu esquerdo, em **Geral,** selecione **Logs**. A partir daqui, pode pesquisar os dados recolhidos de Kafka. Introduza uma consulta na janela de consulta e, em seguida, selecione **Executar**. Seguem-se algumas pesquisas por exemplo:

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

* Mensagens recebidas por segundo: `your_kafka_cluster_name` (Substitua-as pelo nome do cluster.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Bytes de entrada por segundo: `wn0-kafka` (Substitua por um nome de anfitrião do nó do trabalhador.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Bytes de saída por segundo: (Substitua `your_kafka_cluster_name` pelo seu nome de cluster.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Também pode introduzir `*` para pesquisar todos os tipos registados. Atualmente, os seguintes registos estão disponíveis para consultas:

    | Tipo de registo | Descrição |
    | ---- | ---- |
    | log \_ kafkaserver \_ CL | Servidor de corretor kafka.log |
    | log \_ kafkacontroller \_ CL | Controlador de corretor kafka.log |
    | métricas \_ kafka \_ CL | Métricas Kafka JMX |

    :::image type="content" source="./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png" alt-text="Apache kafka log analytics uso cpu" border="true":::

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure Monitor, consulte [a visão geral do Monitor Azure](../../azure-monitor/overview.md)e [os registos do Monitor Desatado para monitorizar os clusters HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Para obter mais informações sobre o trabalho com a Apache Kafka, consulte os seguintes documentos:

* [Espelho Apache Kafka entre clusters HDInsight](apache-kafka-mirroring.md)
* [Aumentar a escala de Apache Kafka em HDInsight](apache-kafka-scalability.md)
* [Use o streaming Apache Spark (DStreams) com Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Use o streaming estruturado apache spark com Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)