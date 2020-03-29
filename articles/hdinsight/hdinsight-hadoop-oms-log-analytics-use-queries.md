---
title: Registos do Monitor De Consulta Azure para monitorizar clusters Azure HDInsight
description: Aprenda a executar consultas em registos do Monitor Azure para monitorizar os trabalhos em funcionamento num cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74803842"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Registos do Monitor De Consulta Azure para monitorizar os clusters HDInsight

Aprenda alguns cenários básicos sobre como usar os registos do Monitor Azure para monitorizar os clusters Azure HDInsight:

* [Analisar as métricas do cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Criar alertas de eventos](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Deve ter configurado um cluster HDInsight para utilizar registos do Monitor Azure e adicionado as soluções de monitorização de registos do Monitor Azure do cluster HDInsight para o espaço de trabalho. Para obter instruções, consulte Os registos do [Monitor Azure com os clusters HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analisar as métricas do cluster HDInsight

Aprenda a procurar métricas específicas para o seu cluster HDInsight.

1. Abra o espaço de trabalho Log Analytics que está associado ao seu cluster HDInsight a partir do portal Azure.
1. Em **Geral,** selecione **Registos**.
1. Digite a seguinte consulta na caixa de pesquisa para procurar todas as métricas disponíveis para todos os clusters HDInsight configurados para utilizar os registos do Monitor Azure e, em seguida, selecione **Executar**. Reveja os resultados.

    ```kusto
    search *
    ```

    ![Apache Ambari pesquisa todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Pesquise todas as métricas")

1. A partir do menu esquerdo, selecione o separador **Filter.**

1. Em **Tipo,** selecione **Heartbeat**. Em **seguida,** selecione Aplicar & Executar .

    ![log analytics pesquisar métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Procurar métricas específicas")

1. Note que a consulta na caixa de texto muda para:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Pode cavar mais fundo utilizando as opções disponíveis no menu esquerdo. Por exemplo:

    - Para ver os registos de um nó específico:

        ![Procurar erros específicos saída1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Procurar erros específicos saída1")

    - Para ver registos em determinados momentos:

        ![Procurar erros específicos saída2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Procurar erros específicos saída2")

1. Selecione **Aplicar & Executar** e rever os resultados. Note também que a consulta foi atualizada para:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Consultas de amostra adicionais

Uma consulta de amostra com base na média dos recursos utilizados num intervalo de 10 minutos, categorizada pelo nome do cluster:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Em vez de refinar com base na média dos recursos utilizados, pode utilizar a seguinte consulta para refinar os resultados com base no momento em que os recursos máximos foram utilizados (bem como percentil 90 e 95) numa janela de 10 minutos:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Criar alertas para rastrear eventos

O primeiro passo para criar um alerta é chegar a uma consulta com base na qual o alerta é desencadeado. Pode usar qualquer consulta que queira criar um alerta.

1. Abra o espaço de trabalho Log Analytics que está associado ao seu cluster HDInsight a partir do portal Azure.
1. Em **Geral,** selecione **Registos**.
1. Faça a seguinte consulta na qual pretende criar um alerta e, em seguida, selecione **Executar**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    A consulta fornece a lista de aplicações falhadas em execução em clusters HDInsight.

1. Selecione **Nova regra** de alerta no topo da página.

    ![Insira a consulta para criar um alerta1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Insira a consulta para criar um alerta1")

1. Na janela de **regra Criar,** introduza a consulta e outros detalhes para criar um alerta e, em seguida, selecione Criar a regra de **alerta**.

    ![Insira a consulta para criar um alerta2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Insira a consulta para criar um alerta2")

### <a name="edit-or-delete-an-existing-alert"></a>Editar ou eliminar um alerta existente

1. Abra o espaço de trabalho log Analytics a partir do portal Azure.

1. A partir do menu esquerdo, em **Monitorização,** selecione **Alertas**.

1. Para o topo, selecione **Gerir as regras**de alerta .

1. Selecione o alerta que pretende editar ou apagar.

1. Tem as seguintes opções: **Guardar,** **Descartar,** **Desativar**e **Eliminar**.

    ![Alerta de alerta hDInsight Azure Monitor elimina editar](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Para mais informações, consulte [Criar, visualizar e gerir alertas métricos utilizando o Monitor Azure.](../azure-monitor/platform/alerts-metric.md)

## <a name="see-also"></a>Consulte também

* [Começar com consultas de log no Monitor Azure](../azure-monitor/log-query/get-started-queries.md)
* [Crie vistas personalizadas usando o View Designer no Monitor Azure](../azure-monitor/platform/view-designer.md)
