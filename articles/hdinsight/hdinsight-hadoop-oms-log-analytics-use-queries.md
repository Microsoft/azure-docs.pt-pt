---
title: Consultar o Azure Log Analytics para monitorizar clusters do HDInsight do Azure
description: Saiba como executar consultas no Azure Log Analytics para monitorizar tarefas em execução num cluster do HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jasonh
ms.openlocfilehash: 2d2de3c2e2b291ec1f5ad170350f19e9e0582eaa
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2018
ms.locfileid: "39602096"
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Consultar o Azure Log Analytics para monitorizar clusters do HDInsight

Saiba mais alguns cenários básicos sobre como utilizar o Azure Log Analytics para monitorizar clusters do HDInsight do Azure:

* [Analisar as métricas de cluster do HDInsight](#analyze-hdinsight-cluster-metrics)
* [Pesquisa de mensagens de registo específicas](#search-for-specific-log-messages)
* [Criar alertas de eventos](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>Pré-requisitos

* Tem de ter configurado um cluster do HDInsight para utilizar o Azure Log Analytics e adicionadas as soluções de gestão do Log Analytics de específicos do cluster HDInsight para a área de trabalho. Para obter instruções, consulte [utilização do Azure Log Analytics com clusters do HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analisar as métricas de cluster do HDInsight

Saiba como procurar métricas específicas para o seu cluster do HDInsight.

1. Abra a área de trabalho do OMS que está associada ao seu cluster do HDInsight no portal do Azure.
2. Selecione o **pesquisa de registos** mosaico.
3. Escreva a consulta seguinte na caixa de pesquisa para procurar todas as métricas para todas as métricas disponíveis para todos os clusters do HDInsight configurados para utilizar o Azure Log Analytics e, em seguida, selecione **executar**.

        search *

    ![Todas as métricas de pesquisa](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "todas as métricas de pesquisa")

    O resultado deverá ter o seguinte aspeto:

    ![Pesquisar todos os resultados de métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "pesquisar todos os resultados de métricas")

5. No painel esquerdo, sob **tipo**, selecione uma métrica que pretende analisar profundamente e, em seguida, selecione **aplicar**. A captura de ecrã a seguir mostra o `metrics_resourcemanager_queue_root_default_CL` é selecionar o tipo.

    > [!NOTE]
    > Poderá ter de selecionar o **[+] mais** botão para encontrar a métrica que está procurando. Além disso, o **aplicar** botão está na parte inferior da lista, de modo tem desloque para baixo para vê-lo.

    Tenha em atenção que a consulta na caixa de texto é alterado para mostrado na caixa de correio realçada na captura de ecrã seguinte:

    ![Procure métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "procure métricas específicas")

6. Para saber mais sobre esta métrica específica. Por exemplo, pode refinar a saída existente com base na média de recursos utilizados num intervalo de 10 minutos, categorizado pelo nome de cluster utilizando a seguinte consulta:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Em vez de refinar com base na média de recursos utilizados, pode usar a seguinte consulta para refinar os resultados com base em quando os recursos máximos foram utilizados (bem como 90th e 95 º percentil) numa janela de 10 minutos:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Pesquisa de mensagens de registo específicas

Saiba como ver mensagens de erro durante a janela de tempo específico. Os passos aqui são apenas um exemplo sobre como pode deparar-se com a mensagem de erro que está interessado. Pode usar qualquer propriedade que está disponível em busca de erros que está tentando encontrar.

1. Abra a área de trabalho do OMS que está associada ao seu cluster do HDInsight no portal do Azure.
2. Selecione o **pesquisa de registos** mosaico.
3. Tipo a seguinte consulta para procurar todas as mensagens de erro para todos os clusters do HDInsight configurados para utilizar o Azure Log Analytics e, em seguida, selecione **executar**. 

         search "Error"

    Deverá ver um resultado como o resultado seguinte:

    ![Pesquisar todos os resultados de erros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "pesquisar todos os resultados de erros")

4. No painel esquerdo, sob **tipo** categoria, selecione um tipo de erro que deseja aprofundar profundamente e em seguida, selecione **aplicar**.  Tenha em atenção que os resultados são refinados para mostrar apenas o erro do tipo que selecionou.
5. Pode analisar mais aprofundadamente esta lista de erro específico, utilizando as opções disponíveis no painel esquerdo. Por exemplo:

    - Para ver mensagens de erro a partir de um nó de trabalho específico:

        ![Procure a saída de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "pesquisa para a saída de erros específicos")

    - Para ver o que erro em determinado momento:

        ![Procure a saída de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "pesquisa para a saída de erros específicos")

6. Para ver o erro específico. Pode selecionar **[+] Mostrar mais** para ver a mensagem de erro real.

    ![Procure a saída de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "pesquisa para a saída de erros específicos")

## <a name="create-alerts-for-tracking-events"></a>Criar alertas para eventos de controlo

O primeiro passo para criar um alerta é deparar-se com uma consulta com base no qual o alerta é acionado. Pode usar qualquer consulta que pretende criar um alerta.

1. Abra a área de trabalho do Log Analytics que está associada ao seu cluster do HDInsight no portal do Azure.
2. Selecione o **pesquisa de registos** mosaico.
3. Execute a seguinte consulta no qual pretende criar um alerta e, em seguida, selecione **executar**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    A consulta fornece a lista de aplicações com falhas em execução em clusters do HDInsight.

4. Selecione **nova regra de alerta** no topo da página.

    ![Consulta de ENTER para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "consulta de Enter para criar um alerta")

5. Na **criar regra** janela, introduza a consulta e outros detalhes para criar um alerta e, em seguida, selecione **criar regra de alerta**.

    ![Consulta de ENTER para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "consulta de Enter para criar um alerta")

Para editar ou eliminar um alerta existente:

1. Abra a área de trabalho do Log Analytics a partir do portal do Azure.
2. No menu à esquerda, selecione **alerta**.
3. Selecione o alerta que pretende editar ou eliminar.
4. Tem as seguintes opções: **salvar**, **descartar**, **desativar**, e **eliminar**.

    ![Edição de eliminação de um alerta do Log Analytics OMS do HDInsight](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Para obter mais informações, consulte [trabalhar com regras de alerta no Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Consulte também

* [Trabalhar com o Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Criar regras de alerta no Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
