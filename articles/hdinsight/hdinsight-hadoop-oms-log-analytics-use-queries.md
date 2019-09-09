---
title: Consultar logs de Azure Monitor para monitorar os clusters do Azure HDInsight
description: Saiba como executar consultas em logs de Azure Monitor para monitorar trabalhos em execução em um cluster HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 031879ac1d0d2dd1148c0c37ee72c60d093f8a7d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70809376"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Consultar logs de Azure Monitor para monitorar clusters HDInsight

Conheça alguns cenários básicos sobre como usar os logs de Azure Monitor para monitorar os clusters do Azure HDInsight:

* [Analisar métricas do cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Pesquisar mensagens de log específicas](#search-for-specific-log-messages)
* [Criar alertas de eventos](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter configurado um cluster HDInsight para usar logs de Azure Monitor e adicionar soluções de monitoramento de logs de Azure Monitor específicas do cluster HDInsight ao espaço de trabalho. Para obter instruções, consulte [usar logs de Azure monitor com clusters HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analisar métricas do cluster HDInsight

Saiba como procurar métricas específicas para seu cluster HDInsight.

1. Abra o espaço de trabalho Log Analytics associado ao seu cluster HDInsight do portal do Azure.
2. Selecione o bloco **pesquisa de logs** .
3. Digite a consulta a seguir na caixa de pesquisa para procurar todas as métricas de todas as métricas disponíveis para todos os clusters HDInsight configurados para usar logs de Azure Monitor e, em seguida, selecione **executar**.

        search *

    ![Pesquisar todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Pesquisar todas as métricas")

    A saída deverá ser semelhante a:

    ![Pesquisar saída de todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Pesquisar saída de todas as métricas")

5. No painel esquerdo, em **tipo**, selecione uma métrica que você deseja aprofundar e, em seguida, selecione **aplicar**. A captura de tela a `metrics_resourcemanager_queue_root_default_CL` seguir mostra que o tipo está selecionado.

    > [!NOTE]  
    > Talvez seja necessário selecionar o botão **[+] mais** para localizar a métrica que você está procurando. Além disso, o botão **aplicar** está na parte inferior da lista, portanto, você deve rolar para baixo para vê-lo.

    Observe que a consulta na caixa de texto é alterada para uma mostrada na caixa realçada na seguinte captura de tela:

    ![Pesquisar métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Pesquisar métricas específicas")

6. Para se aprofundar nessa métrica específica. Por exemplo, você pode refinar a saída existente com base na média de recursos usados em um intervalo de 10 minutos, Categorizado pelo nome do cluster usando a seguinte consulta:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Em vez de refinar com base na média de recursos usados, você pode usar a consulta a seguir para refinar os resultados com base em quando os recursos máximos foram usados (bem como 90 º e 95 º percentil) em uma janela de 10 minutos:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Pesquisar mensagens de log específicas

Saiba como procurar mensagens de erro durante uma janela de tempo específica. As etapas aqui são apenas um exemplo de como você pode chegar à mensagem de erro em que você está interessado. Você pode usar qualquer propriedade disponível para procurar os erros que está tentando localizar.

1. Abra o espaço de trabalho Log Analytics associado ao seu cluster HDInsight do portal do Azure.
2. Selecione o bloco **pesquisa de logs** .
3. Digite a consulta a seguir para pesquisar todas as mensagens de erro de todos os clusters HDInsight configurados para usar logs de Azure Monitor e, em seguida, selecione **executar**. 

         search "Error"

    Você deverá ver uma saída como a seguinte saída:

    ![Pesquisar saída de todos os erros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Pesquisar saída de todos os erros")

4. No painel esquerdo, em categoria do **tipo** , selecione um tipo de erro que você deseja aprofundar e, em seguida, selecione **aplicar**.  Observe que os resultados são refinados para mostrar apenas o erro do tipo selecionado.
5. Você pode se aprofundar nessa lista de erros específica usando as opções disponíveis no painel esquerdo. Por exemplo:

    - Para ver as mensagens de erro de um nó de trabalho específico:

        ![Pesquisar erros específicos Saída1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Pesquisar erros específicos Saída1")

    - Para ver se ocorreu um erro em um determinado momento:

        ![Pesquisar erros específicos output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Pesquisar erros específicos output2")

6. Para ver o erro específico. Você pode selecionar **[+] mostrar mais** para examinar a mensagem de erro real.

    ![Pesquisar erros específicos output3](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Pesquisar erros específicos output3")

## <a name="create-alerts-for-tracking-events"></a>Criar alertas para eventos de rastreamento

A primeira etapa para criar um alerta é chegar em uma consulta com base na qual o alerta é disparado. Você pode usar qualquer consulta que deseje criar um alerta.

1. Abra o espaço de trabalho Log Analytics associado ao seu cluster HDInsight do portal do Azure.
2. Selecione o bloco **pesquisa de logs** .
3. Execute a consulta a seguir na qual você deseja criar um alerta e, em seguida, selecione **executar**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    A consulta fornece a lista de aplicativos com falha em execução em clusters HDInsight.

4. Selecione **nova regra de alerta** na parte superior da página.

    ![Inserir consulta para criar um alert1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Inserir consulta para criar um alert1")

5. Na janela **criar regra** , insira a consulta e outros detalhes para criar um alerta e, em seguida, selecione **criar regra de alerta**.

    ![Inserir consulta para criar um alert2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Inserir consulta para criar um alert2")

Para editar ou excluir um alerta existente:

1. Abra o espaço de trabalho Log Analytics da portal do Azure.
2. No menu à esquerda, selecione **alerta**.
3. Selecione o alerta que você deseja editar ou excluir.
4. Existem as seguintes opções: **Salvar**, **descartar**, **desabilitar**e **excluir**.

    ![Log de Azure Monitor do HDInsight logs de exclusão de alertas](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Para obter mais informações, consulte [criar, exibir e gerenciar alertas de métrica usando Azure monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Consulte também

* [Criar exibições personalizadas usando o designer de exibição no Azure Monitor](../azure-monitor/platform/view-designer.md)
* [Criar, exibir e gerenciar alertas de métrica usando Azure Monitor](../azure-monitor/platform/alerts-metric.md)
