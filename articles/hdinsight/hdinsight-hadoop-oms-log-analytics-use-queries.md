---
title: Registos do Monitor de Consulta Azure para monitorizar os clusters Azure HDInsight
description: Aprenda a executar consultas nos registos do Azure Monitor para monitorizar os trabalhos em execução num cluster HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 360a9730025dc24eda93868903fcd356c37d06ef
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576328"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Registos do Monitor de Consulta Azure para monitorizar clusters HDInsight

Saiba alguns cenários básicos sobre como usar os registos do Azure Monitor para monitorizar os clusters Azure HDInsight:

* [Analisar métricas de cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Criar alertas de eventos](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Deve ter configurado um cluster HDInsight para utilizar registos do Monitor Azure e adicionado as soluções de monitorização do monitor de monitores do cluster HDInsight para o espaço de trabalho. Para obter instruções, consulte [registos do Monitor Azure com clusters HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analisar métricas de cluster HDInsight

Aprenda a procurar métricas específicas para o seu cluster HDInsight.

1. Abra o espaço de trabalho Log Analytics que está associado ao seu cluster HDInsight a partir do portal Azure.
1. Em **Geral**, selecione **Registos**.
1. Digite a seguinte consulta na caixa de pesquisa para procurar todas as métricas para todas as métricas disponíveis para todos os clusters HDInsight configurados para usar registos do Monitor Azure e, em seguida, selecione **Executar**. Reveja os resultados.

    ```kusto
    search *
    ```

    ![Apache Ambari analíticos pesquisam todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Pesquisar todas as métricas")

1. A partir do menu esquerdo, selecione o **separador Filtro.**

1. Sob **o tipo**, selecione **Heartbeat**. Em seguida, **selecione Aplicar & Executar**.

    ![registar métricas específicas de pesquisa de análise](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Pesquisa de métricas específicas")

1. Note que a consulta na caixa de texto muda para:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Pode cavar mais fundo utilizando as opções disponíveis no menu esquerdo. Por exemplo:

    - Para ver registos de um nó específico:

        ![Procurar erros específicos de saída1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Procurar erros específicos de saída1")

    - Para ver registos em determinados momentos:

        ![Procurar erros específicos output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Procurar erros específicos output2")

1. **Selecione Aplicar & Executar** e rever os resultados. Note também que a consulta foi atualizada para:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Consultas adicionais de amostra

Uma consulta de amostra com base na média dos recursos utilizados num intervalo de 10 minutos, categorizado pelo nome do cluster:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Em vez de refinar com base na média dos recursos utilizados, pode utilizar a seguinte consulta para refinar os resultados com base na utilização dos recursos máximos (bem como o percentil 90 e 95) numa janela de 10 minutos:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Criar alertas para eventos de rastreio

O primeiro passo para criar um alerta é chegar a uma consulta com base na qual o alerta é desencadeado. Pode utilizar qualquer consulta que queira criar um alerta.

1. Abra o espaço de trabalho Log Analytics que está associado ao seu cluster HDInsight a partir do portal Azure.
1. Em **Geral**, selecione **Registos**.
1. Faça a seguinte consulta sobre a qual pretende criar um alerta e, em seguida, selecione **Executar**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    A consulta fornece uma lista de aplicações falhadas em execução em clusters HDInsight.

1. Selecione **Nova regra** de alerta no topo da página.

    ![Insira consulta para criar um alerta1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Insira consulta para criar um alerta1")

1. Na janela **regra Criar,** insira a consulta e outros detalhes para criar um alerta e, em seguida, selecione **Criar a regra de alerta**.

    ![Insira consulta para criar um alerta2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Insira consulta para criar um alerta2")

### <a name="edit-or-delete-an-existing-alert"></a>Editar ou apagar um alerta existente

1. Abra o espaço de trabalho Log Analytics a partir do portal Azure.

1. A partir do menu esquerdo, em **Monitorização,** **selecione Alertas**.

1. Para o topo, **selecione Gerir as regras de alerta**.

1. Selecione o alerta que pretende editar ou apagar.

1. Tem as seguintes opções: **Guardar,** **Descartar,** **desativar** e **Eliminar**.

    ![HDInsight Azure Monitor alerta eliminar edição](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Para obter mais informações, consulte [Criar, ver e gerir alertas métricos utilizando o Azure Monitor](../azure-monitor/alerts/alerts-metric.md).

## <a name="see-also"></a>Ver também

* [Introdução às consultas de registos no Azure Monitor](../azure-monitor/logs/get-started-queries.md)
* [Crie vistas personalizadas utilizando o View Designer no Azure Monitor](../azure-monitor/visualize/view-designer.md)
