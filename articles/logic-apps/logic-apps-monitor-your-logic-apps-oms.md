---
title: Monitorar aplicativos lógicos com Azure Monitor
description: Obtenha informações e depuração de dados para solucionar problemas e diagnosticar execuções de aplicativo lógico com logs de Azure Monitor
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/29/2019
ms.openlocfilehash: 305b50c86a468354f049fcc57fcb79b537e8dfed
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791890"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>Obter informações e depurar dados para aplicativos lógicos usando logs de Azure Monitor

Para monitorar e obter detalhes de depuração mais avançados sobre seus aplicativos lógicos, ative [Azure monitor logs](../log-analytics/log-analytics-overview.md) ao criar seu aplicativo lógico. Os logs de Azure Monitor fornecem log de diagnóstico e monitoramento para seus aplicativos lógicos quando você instala a solução de gerenciamento de aplicativos lógicos no portal do Azure. Essa solução também fornece informações agregadas para o aplicativo lógico executado com detalhes específicos, como status, tempo de execução, status de reenvio e IDs de correlação. Este artigo mostra como ativar os logs de Azure Monitor para que você possa exibir eventos de tempo de execução e dados para suas execuções de aplicativo lógico.

Este tópico mostra como configurar os logs de Azure Monitor quando você cria seu aplicativo lógico. Para ativar os logs de Azure Monitor para os aplicativos lógicos existentes, siga estas etapas para [ativar o log de diagnóstico e enviar dados de tempo de execução do aplicativo lógico para Azure monitor logs](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Esta página descreveu anteriormente as etapas de como executar essas tarefas com o Microsoft Operations Management Suite (OMS), que é [desativado em janeiro de 2019](../azure-monitor/platform/oms-portal-transition.md)e substitui essas etapas por [Azure monitor logs](../azure-monitor/platform/data-platform-logs.md), que substituiu o termo log Analytics. Os dados de log ainda são armazenados em um espaço de trabalho Log Analytics e ainda são coletados e analisados pelo mesmo serviço Log Analytics. Para obter mais informações, consulte [Azure monitor as alterações de terminologia](../azure-monitor/terminology.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisa de um espaço de trabalho Log Analytics. Saiba [como criar um espaço de trabalho log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="turn-on-logging-for-new-logic-apps"></a>Ativar o registro em log para novos aplicativos lógicos

1. Em [portal do Azure](https://portal.azure.com), crie seu aplicativo lógico. No menu principal do Azure, selecione **criar um recurso** > **integração** > **aplicativo lógico**.

   ![Criar novo aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. Em **aplicativo lógico**, siga estas etapas:

   1. Forneça um nome para seu aplicativo lógico e selecione sua assinatura do Azure.

   1. Crie ou selecione um grupo de recursos do Azure. Selecione o local para seu aplicativo lógico.

   1. Em **log Analytics**, selecione **ativado**.

   1. Na lista **log Analytics espaço de trabalho** , selecione o espaço de trabalho para o qual você deseja enviar os dados de suas execuções de aplicativo lógico.

      ![Indicar as informações da aplicação lógica](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      Depois de concluir esta etapa, o Azure criará seu aplicativo lógico, que agora está associado ao seu espaço de trabalho Log Analytics. Além disso, essa etapa instala automaticamente a solução de gerenciamento de aplicativos lógicos em seu espaço de trabalho.

   1. Quando terminar, selecione **Criar**.

1. Para exibir as execuções de seu aplicativo lógico, [continue com estas etapas](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Instalar solução de gerenciamento de aplicativos lógicos

Se você já configurou os logs de Azure Monitor quando criou seu aplicativo lógico, ignore esta etapa. Você já tem a solução de gerenciamento de aplicativos lógicos instalada.

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, localize "espaços de trabalho do log Analytics" e selecione **log Analytics espaços de trabalho**.

   ![Selecione "espaços de trabalho do Log Analytics"](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. Em **espaços de trabalho do log Analytics**, selecione seu espaço de trabalho.

   ![Selecione seu espaço de trabalho Log Analytics](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. No painel Visão geral, em introdução **ao Log Analytics** > **configurar soluções de monitoramento**, selecione **Exibir soluções**.

   ![Selecione "Exibir soluções"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Em **visão geral**, selecione **Adicionar**.

   ![Selecione "Adicionar"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. Depois que o **Marketplace** for aberto, na caixa de pesquisa, insira "gerenciamento de aplicativos lógicos" e selecione **Gerenciamento de aplicativos lógicos**.

   ![Selecione "gerenciamento de aplicativos lógicos"](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. No painel Descrição da solução, selecione **criar**.

   ![Selecione "criar" para "gerenciamento de aplicativos lógicos"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. Examine e confirme o espaço de trabalho Log Analytics onde você deseja instalar a solução e selecione **criar** novamente.

   ![Selecione "criar" para "gerenciamento de aplicativos lógicos"](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   Depois que o Azure implantar a solução no grupo de recursos do Azure que contém seu espaço de trabalho Log Analytics, a solução aparecerá no painel Resumo do espaço de trabalho.

   ![Painel de resumo do espaço de trabalho](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Exibir informações de execução do aplicativo lógico

Depois que o aplicativo lógico for executado, você poderá exibir o status e a contagem dessas execuções no bloco **Gerenciamento de aplicativos lógicos** .

1. Vá para o espaço de trabalho Log Analytics e selecione **Resumo do espaço de trabalho** > gerenciamento de **aplicativos lógicos**.

   ![Status e contagem de execução do aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   Aqui, suas execuções de aplicativo lógico são agrupadas por nome ou por status de execução. Essa página também mostra detalhes sobre falhas em ações ou gatilhos para as execuções do aplicativo lógico.

   ![Resumo de status para suas execuções de aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. Para exibir todas as execuções de um aplicativo lógico específico ou status, selecione a linha de um aplicativo lógico ou um status.

   Aqui está um exemplo que mostra todas as execuções para um aplicativo lógico específico:

   ![Exibir as execuções e o status do aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Esta página tem opções avançadas: 

   * Coluna **Propriedades rastreadas** : para um aplicativo lógico em que você configura as propriedades rastreadas, que são agrupadas por ações, você pode exibir essas propriedades desta coluna. Para exibir essas propriedades rastreadas, selecione **Exibir**. Para pesquisar as propriedades rastreadas, use o filtro de coluna.

      ![Exibir propriedades rastreadas para um aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

      Todas as propriedades rastreadas adicionadas recentemente podem levar 10-15 minutos antes de aparecerem pela primeira vez. Saiba [como adicionar propriedades rastreadas ao seu aplicativo lógico](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Reenviar**: você pode reenviar uma ou mais execuções de aplicativos lógicos que falharam, tiveram êxito ou ainda estão em execução. Marque as caixas de seleção das execuções que você deseja reenviar e, em seguida, selecione **reenviar**.

     ![Reenviar execuções de aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Para filtrar os resultados, você pode executar a filtragem do lado do cliente e do servidor.

   * **Filtro do lado do cliente**: para cada coluna, selecione os filtros desejados, por exemplo:

     ![Filtros de coluna de exemplo](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtro no lado do servidor**: para selecionar uma janela de tempo específica ou para limitar o número de execuções que aparecem, use o controle de escopo na parte superior da página. Por padrão, somente os registros 1.000 aparecem por vez.

     ![Alterar a janela de tempo](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. Para exibir todas as ações e seus detalhes para uma execução específica, selecione a linha para uma execução de aplicativo lógico.

   Aqui está um exemplo que mostra todas as ações e gatilhos para uma execução de aplicativo lógico específico:

   ![Exibir ações para uma execução de aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. Em qualquer página de resultados, para exibir a consulta por trás dos resultados ou para ver todos os resultados, selecione **Ver todos**, o que abre a página **logs** .

   ![Ver todos os resultados](media/logic-apps-monitor-your-logic-apps-oms/logic-app-see-all.png)

   Na página **logs** , você pode escolher estas opções:

   * Para exibir os resultados da consulta em uma tabela, selecione **tabela**.

   * As consultas usam a [linguagem de consulta Kusto](https://aka.ms/LogAnalyticsLanguageReference), que você pode editar se quiser exibir resultados diferentes. Para alterar a consulta, atualize a cadeia de caracteres de consulta e selecione **executar** para exibir os resultados na tabela. 

     ![Log Analytics-exibição de consulta](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar mensagens B2B](../logic-apps/logic-apps-monitor-b2b-message.md)