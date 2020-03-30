---
title: Monitorize um recurso Azure com o Monitor Azure
description: Saiba como recolher e analisar dados para um recurso Azure no Monitor Azure.
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: c4b80e62d3800392b847a411dfc66c3278e72bba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77661869"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Quickstart: Monitorize um recurso Azure com o Monitor Azure
[O Azure Monitor](../overview.md) começa a recolher dados dos recursos do Azure no momento em que são criados. Este quickstart fornece uma breve passagem pelos dados que são recolhidos automaticamente para um recurso e como vê-lo no portal Azure para um recurso particular. Mais tarde, pode adicionar configuração para recolher dados adicionais e pode ir ao menu Do Monitor Azure para utilizar as mesmas ferramentas para aceder aos dados recolhidos para todos os recursos da sua subscrição.

Para descrições mais detalhadas dos dados de monitorização recolhidos dos recursos do Azure, consulte [a Monitorização dos recursos do Azure com](../insights/monitor-azure-resource.md)o Monitor Azure .


## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em . 


## <a name="overview-page"></a>Página de visão geral
Muitos serviços incluirão a monitorização de dados na sua página **de visão geral** como um rápido olhar para o seu funcionamento. Isto será normalmente baseado num subconjunto de métricas de plataforma armazenadas em Métricas de Monitor Azure.

1. Localize um recurso Azure na sua subscrição.
2. Vá à página **de visão geral** e note se houver algum dado de desempenho apresentado. Estes dados serão fornecidos pelo Azure Monitor. O exemplo abaixo é a página **de visão geral** para uma conta de armazenamento Azure, e você pode ver que existem várias métricas exibidas.

    ![Página de visão geral](media/quick-monitor-azure-resource/overview.png)

3. Pode clicar em qualquer um dos gráficos para abrir os dados no explorador de métricas que é descrito abaixo.

## <a name="view-the-activity-log"></a>Ver o registo de Atividades
O registo de Atividades fornece informações sobre as operações em cada recurso Azure na subscrição. Isto incluirá informações como quando um recurso é criado ou modificado, quando um trabalho é iniciado, ou quando uma determinada operação ocorre.

1. Na parte superior do menu para o seu recurso, selecione **registo de atividade**.
2. O filtro atual está definido para eventos relacionados com o seu recurso. Se não vir nenhum evento, tente mudar o **Timespan** para aumentar o intervalo de tempo.

    ![Registo de atividades](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Se quiser ver eventos de outros recursos na sua subscrição, altere os critérios no filtro ou até remova as propriedades do filtro.

    ![Registo de atividades](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Ver métricas
As métricas são valores numéricos que descrevem algum aspeto do seu recurso num determinado momento. O Azure Monitor recolhe automaticamente as métricas da plataforma em intervalos de um minuto de todos os recursos do Azure. Pode ver estas métricas usando o explorador de métricas.

1. Na secção **de Monitorização** do menu do seu recurso, selecione **Métricas**. Isto abre o explorador de métricas com o âmbito definido para o seu recurso.
2. Clique **em Adicionar métrica** para adicionar uma métrica ao gráfico.
   
   ![Explorador de métricas](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Selecione uma **Métrica** da lista de dropdown e, em seguida, uma **agregação**. Isto define como os valores recolhidos serão amostrados em cada intervalo de tempo.

    ![Explorador de métricas](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Clique **em Adicionar métrica** para adicionar combinações métricas e de agregação adicionais ao gráfico.

    ![Explorador de métricas](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, viu o registo de Atividade e as métricas de um recurso Azure que são automaticamente recolhidos pelo Monitor Azure. Os registos de recursos fornecem informações sobre o funcionamento detalhado do recurso, mas devem ser configurados para serem recolhidos. Continue a ser o tutorial para recolher registos de recursos num espaço de trabalho do Log Analytics onde podem ser analisados usando consultas de registo.

> [!div class="nextstepaction"]
> [Recolher e analisar registos de recursos com o Monitor Azure](tutorial-resource-logs.md)
