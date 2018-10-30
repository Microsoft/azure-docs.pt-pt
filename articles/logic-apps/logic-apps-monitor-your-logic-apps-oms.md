---
title: Monitorizar aplicações lógicas com o Log Analytics - Azure Logic Apps | Documentos da Microsoft
description: Obter informações e dados para resolução de problemas e diagnosticar as execuções de aplicação lógica com o Azure Log Analytics de depuração
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: c65cc24f9b0083e9c873465008490bf00ea83852
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232381"
---
# <a name="monitor-logic-apps-with-azure-log-analytics"></a>Monitorizar aplicações lógicas com o Azure Log Analytics

Para monitorizar e obter os detalhes de depuração mais avançadas sobre as aplicações lógicas, ative [do Azure Log Analytics](../log-analytics/log-analytics-overview.md) quando criar a sua aplicação lógica. O log Analytics disponibiliza diagnósticos de registo e monitorização para as aplicações lógicas ao instalar a solução de gestão do Logic Apps no portal do Azure. Esta solução também fornece informações agregadas para a aplicação lógica é executado com detalhes específicos, tais como Estado, o tempo de execução, o estado de ressubmissão e o IDs de correlação. Este artigo mostra como ativar o Log Analytics, para que pode ver eventos de tempo de execução e dados para a aplicação lógica é executada.

Para ativar o Azure Log Analytics para o logic apps existentes, siga estes passos para [ativar o registo de diagnósticos e enviar dados de tempo de execução de aplicação lógica para o Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Passos para saber como realizar estas tarefas com o Microsoft Operations Management Suite (OMS), que é descrito anteriormente, esta página [extinguir em Janeiro de 2019](../log-analytics/log-analytics-oms-portal-transition.md), substitui esses passos com o Azure Log Analytics em vez disso. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, terá de uma área de trabalho do Log Analytics. Saiba mais [como criar uma área de trabalho do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Ativar o registo de diagnóstico durante a criação de aplicações lógicas

1. Na [portal do Azure](https://portal.azure.com), criar uma aplicação lógica. Escolher **criar um recurso** > **integração** > **aplicação lógica**.

   ![Criar uma aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. Sob **Criar aplicação lógica**, realizar estas tarefas, conforme mostrado:

   1. Forneça um nome para a aplicação lógica e selecione a sua subscrição do Azure. 

   1. Crie ou selecione um grupo de recursos do Azure.

   1. Definir **do Log Analytics** ao **no**. 

   1. Na lista de área de trabalho do Log Analytics de lista, selecione a área de trabalho onde pretende enviar dados para a aplicação lógica é executada. 

      ![Criar uma aplicação lógica](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Depois de concluir este passo, o Azure cria a aplicação lógica, que agora é associado à sua área de trabalho do Log Analytics. 
      Além disso, este passo instala também automaticamente a solução de gestão do Logic Apps em sua área de trabalho.

   1. Quando tiver terminado, escolha **Create** (Criar).

1. Para ver a sua aplicação lógica é executada, [continuar com estes passos](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Instalar a solução de gestão do Logic Apps

Se já ativou o Log Analytics quando criou a sua aplicação lógica, ignore este passo. Já tem a solução de gestão do Logic Apps instalada.

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, encontre "log analytics" e selecione **do Log Analytics**.

   ![Selecione "Do Log Analytics"](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. Sob **do Log Analytics**, localize e selecione a sua área de trabalho do Log Analytics. 

   ![Selecione a sua área de trabalho do Log Analytics](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Sob **introdução ao Log Analytics** > **configurar soluções de monitorização**, escolha **ver soluções**.

   ![Escolha "Ver soluções"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Na página Descrição geral, escolha **Add**, que abre o **soluções de gestão** lista. A partir dessa lista, selecione **gestão do Logic Apps**. 

   ![Selecione "Gestão do Logic Apps"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Se não conseguir encontrar a solução, na parte inferior da lista, escolha **carregar mais** até que apareça a solução.

1. Escolher **Create**, confirme a área de trabalho do Log Analytics onde pretende instalar a solução e, em seguida, escolha **criar** novamente.   

   ![Escolha "Criar" para "Gestão do Logic Apps"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

   Se não pretender utilizar uma área de trabalho existente, também pode criar uma nova área de trabalho neste momento.

   Quando tiver terminado, a solução de gestão do Logic Apps é apresentada na página de descrição geral. 

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Aplicação de lógica de exibição executar informações

Após a execução da sua aplicação lógica, pode ver o estado e a contagem para essas execuções no **gestão do Logic Apps** mosaico. 

1. Vá para a área de trabalho do Log Analytics e abrir a página de descrição geral. Escolher **gestão do Logic Apps**. 

   ![Status e contagem de execução da aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   Aqui, as execuções de aplicação lógica são agrupadas por nome ou por Estado de execução. 
   Esta página também mostra detalhes sobre falhas de ações ou acionadores para execuções de aplicação lógica.

   ![Estado de resumo para a aplicação lógica é executada](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
1. Para ver todas as execuções de aplicação de lógica específica ou num Estado, selecione a linha para um Estado ou de uma aplicação lógica.

   Eis um exemplo que mostra todas as execuções para uma aplicação de lógica específica:

   ![Ver execuções de para um Estado ou de uma aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Esta página tem estas opções avançadas:

   * **Propriedades controladas:**

     Esta coluna mostra propriedades controladas, as quais estão agrupadas por ações, a aplicação lógica. Para ver as propriedades controladas, escolha **vista**. 
     Para procurar as propriedades controladas, utilize o filtro de coluna.
   
     ![Ver propriedades controladas para uma aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Quaisquer propriedades controladas adicionadas recentemente poderão demorar 10 a 15 minutos antes de serem apresentados pela primeira vez. Saiba mais [como adicionar propriedades controladas à sua aplicação lógica](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Volte a submeter:** pode submeter novamente um ou mais execuções da aplicação lógica que falhou, foi concluída com êxito, ou são ainda em execução. Selecione as caixas de verificação para as execuções que pretende submeter novamente e escolha **volte a submeter**. 

     ![Submeter novamente execuções da aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Para filtrar esses resultados, pode realizar a filtragem do lado do cliente e servidor.

   * **Filtro do lado do cliente**: para cada coluna, selecione os filtros que pretende, por exemplo:

     ![Filtros de coluna de exemplo](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtro do lado do servidor**: escolher uma janela de tempo específico ou para limitar o número de execuções que aparecem, utilize o controlo de âmbito na parte superior da página. Por predefinição, apenas 1000 registos são apresentados ao mesmo tempo.
   
     ![Alterar a janela de tempo](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
1. Para ver todas as ações e os respetivos detalhes para uma execução específica, selecione uma linha para uma execução da aplicação lógica.

   Eis um exemplo que mostra todas as ações de uma execução da aplicação lógica específica:

   ![Executam as ações de modo de exibição de uma aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
1. Em qualquer página de resultados, para ver a consulta por trás os resultados ou para ver todos os resultados, escolha **ver todos os**, que abre a página de pesquisa de registos.
   
   ![Ver tudo nas páginas de resultados](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   Na página de pesquisa de registos,

   * Para ver os resultados da consulta numa tabela, escolha **tabela**.

   * Para alterar a consulta, pode editar a cadeia de consulta na barra de pesquisa. 
   Para uma melhor experiência, escolha **Advanced Analytics**.

     ![Ver ações e os detalhes de uma execução da aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Na página do Log Analytics do Azure, pode atualizar consultas e ver os resultados da tabela. Esta consulta utiliza [linguagem de consulta de Kusto](https://aka.ms/LogAnalyticsLanguageReference), que pode editar se desejar exibir resultados diferentes. 

     ![O Azure Log Analytics - vista de consulta](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Passos Seguintes

* [Monitorizar mensagens B2B](../logic-apps/logic-apps-monitor-b2b-message.md)