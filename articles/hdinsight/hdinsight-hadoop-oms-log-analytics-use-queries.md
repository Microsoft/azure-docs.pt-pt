---
title: Consultar logs de Azure Monitor para monitorar os clusters do Azure HDInsight
description: Saiba como executar consultas em logs de Azure Monitor para monitorar trabalhos em execução em um cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803842"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Consultar logs de Azure Monitor para monitorar clusters HDInsight

Conheça alguns cenários básicos sobre como usar os logs de Azure Monitor para monitorar os clusters do Azure HDInsight:

* [Analisar métricas do cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Criar alertas de eventos](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter configurado um cluster HDInsight para usar logs de Azure Monitor e adicionar soluções de monitoramento de logs de Azure Monitor específicas do cluster HDInsight ao espaço de trabalho. Para obter instruções, consulte [usar logs de Azure monitor com clusters HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analisar métricas do cluster HDInsight

Saiba como procurar métricas específicas para seu cluster HDInsight.

1. Abra o espaço de trabalho Log Analytics associado ao seu cluster HDInsight do portal do Azure.
1. Em **geral**, selecione **logs**.
1. Digite a consulta a seguir na caixa de pesquisa para procurar todas as métricas de todas as métricas disponíveis para todos os clusters HDInsight configurados para usar logs de Azure Monitor e, em seguida, selecione **executar**. Reveja os resultados.

    ```kusto
    search *
    ```

    ![O Apache Ambari Analytics pesquisa todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Pesquisar todas as métricas")

1. No menu à esquerda, selecione a guia **filtro** .

1. Em **tipo**, selecione **pulsação**. Em seguida, selecione **aplicar & executar**.

    ![métricas específicas de pesquisa do log Analytics](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Pesquisar métricas específicas")

1. Observe que a consulta na caixa de texto muda para:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Você pode se aprofundar usando as opções disponíveis no menu à esquerda. Por exemplo:

    - Para ver os logs de um nó específico:

        ![Pesquisar erros específicos Saída1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Pesquisar erros específicos Saída1")

    - Para ver os logs em determinados momentos:

        ![Pesquisar erros específicos output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Pesquisar erros específicos output2")

1. Selecione **aplicar & executar** e revisar os resultados. Observe também que a consulta foi atualizada para:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Consultas de exemplo adicionais

Uma consulta de exemplo com base na média de recursos usados em um intervalo de 10 minutos, Categorizado pelo nome do cluster:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Em vez de refinar com base na média de recursos usados, você pode usar a consulta a seguir para refinar os resultados com base em quando os recursos máximos foram usados (bem como 90 º e 95 º percentil) em uma janela de 10 minutos:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Criar alertas para eventos de rastreamento

A primeira etapa para criar um alerta é chegar em uma consulta com base na qual o alerta é disparado. Você pode usar qualquer consulta que deseje criar um alerta.

1. Abra o espaço de trabalho Log Analytics associado ao seu cluster HDInsight do portal do Azure.
1. Em **geral**, selecione **logs**.
1. Execute a consulta a seguir na qual você deseja criar um alerta e, em seguida, selecione **executar**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    A consulta fornece a lista de aplicativos com falha em execução em clusters HDInsight.

1. Selecione **nova regra de alerta** na parte superior da página.

    ![Inserir consulta para criar um alert1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Inserir consulta para criar um alert1")

1. Na janela **criar regra** , insira a consulta e outros detalhes para criar um alerta e, em seguida, selecione **criar regra de alerta**.

    ![Inserir consulta para criar um alert2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Inserir consulta para criar um alert2")

### <a name="edit-or-delete-an-existing-alert"></a>Editar ou excluir um alerta existente

1. Abra o espaço de trabalho Log Analytics da portal do Azure.

1. No menu à esquerda, em **monitoramento**, selecione **alertas**.

1. Em direção à parte superior, selecione **gerenciar regras de alerta**.

1. Selecione o alerta que você deseja editar ou excluir.

1. Você tem as seguintes opções: **salvar**, **descartar**, **desabilitar**e **excluir**.

    ![Log de Azure Monitor do HDInsight logs de exclusão de alertas](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Para obter mais informações, consulte [criar, exibir e gerenciar alertas de métrica usando Azure monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Ver também

* [Introdução às consultas de log no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Criar exibições personalizadas usando o designer de exibição no Azure Monitor](../azure-monitor/platform/view-designer.md)
