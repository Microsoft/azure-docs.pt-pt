---
title: Monitorize um recurso Azure com o Azure Monitor
description: Saiba como recolher e analisar dados para um recurso Azure no Azure Monitor.
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 00cec2ca11b77f6cb9d6c0f3c429931bdcc6f6e4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186138"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Quickstart: Monitorize um recurso Azure com o Azure Monitor
[O Azure Monitor](../overview.md) começa a recolher dados dos recursos da Azure no momento em que são criados. Este quickstart fornece uma breve passagem pelos dados que são automaticamente recolhidos para um recurso e como vê-lo no portal Azure para um determinado recurso. Mais tarde, pode adicionar configuração para recolher dados adicionais e pode ir ao menu Azure Monitor para usar as mesmas ferramentas para aceder aos dados recolhidos para todos os recursos da sua subscrição.

Para descrições mais detalhadas dos dados de monitorização recolhidos a partir dos recursos da Azure consulte [monitorizar os recursos do Azure com o Azure Monitor](../insights/monitor-azure-resource.md).


## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 


## <a name="overview-page"></a>Página geral
Muitos serviços incluirão a monitorização de dados na sua página **de Visão Geral** como um rápido olhar para o seu funcionamento. Isto será normalmente baseado num subconjunto de métricas de plataforma armazenadas em Métricas do Monitor Azure.

1. Localize um recurso Azure na sua subscrição.
2. Vá à página **'Vista Geral'** e note se há algum dado de desempenho apresentado. Estes dados serão fornecidos pelo Azure Monitor. O exemplo abaixo é a página **'Vista Geral'** para uma conta de armazenamento Azure, e pode ver que existem várias métricas apresentadas.

    ![Página geral](media/quick-monitor-azure-resource/overview.png)

3. Pode clicar em qualquer um dos gráficos para abrir os dados no explorador de métricas que é descrito abaixo.

## <a name="view-the-activity-log"></a>Ver o registo de atividades
O registo de Atividade fornece informações sobre as operações de cada recurso Azure na subscrição. Isto incluirá informações como quando um recurso é criado ou modificado, quando um trabalho é iniciado, ou quando ocorre uma determinada operação.

1. No topo do menu para o seu recurso, selecione **Registo de atividade**.
2. O filtro atual está definido para eventos relacionados com o seu recurso. Se não vir nenhum evento, tente alterar o **Timespan** para aumentar o tempo.

    ![Screenshot de um recurso Activity Log in Azure portal mostrando uma lista de operações para o recurso gerado usando os critérios de filtro predefinidos.](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Se pretender ver eventos de outros recursos na sua subscrição, altere os critérios no filtro ou até remova as propriedades do filtro.

    ![Screenshot de um recurso Activity Log in Azure portal mostrando uma lista de operações para o recurso gerado usando critérios de filtro modificados.](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Ver métricas
As métricas são valores numéricos que descrevem algum aspeto do seu recurso num determinado momento. O Azure Monitor recolhe automaticamente as métricas da plataforma em intervalos de um minuto de todos os recursos Azure. Pode ver estas métricas usando o explorador de métricas.

1. Na secção **de Monitorização** do menu do seu recurso, selecione **Métricas**. Isto abre métricas de explorador com o âmbito definido para o seu recurso.
2. Clique **em Adicionar métrica** para adicionar uma métrica ao gráfico.
   
   ![Screenshot do explorador de métricas no portal Azure mostrando as métricas para um recurso. A queda métrica está aberta para adicionar novas métricas.](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Selecione uma **métrica** da lista de abandono e, em **seguida, uma agregação**. Isto define como os valores recolhidos serão amostrados ao longo de cada intervalo de tempo.

    ![Screenshot do explorador de métricas no portal Azure mostrando as métricas para um recurso. O dropdown de agregação está aberto e Avg é selecionado.](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Clique **em Adicionar métrica** para adicionar combinações métricas e agregações adicionais ao gráfico.

    ![Screenshot do explorador de métricas no portal Azure mostrando as métricas para um recurso.](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, viu o registo de atividade e as métricas de um recurso Azure que são automaticamente recolhidos pelo Azure Monitor. Continue até ao próximo quickstart que lhe mostra como recolher o login de Atividade num espaço de trabalho log Analytics onde podem ser [analisados](../log-query/log-query-overview.md)através de consultas de registo .

> [!div class="nextstepaction"]
> [Enviar log de atividade azure para log analytics espaço de trabalho](./quick-collect-activity-log-portal.md)