---
title: Criar consultas de registos para mensagens B2B nos registos do Azure Monitor - Azure Logic Apps | Documentos da Microsoft
description: Criar consultas que controlam o AS2, X12 e EDIFACT as mensagens no Log Analytics do Azure para o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: d4a94e75de34bbafd3bc8f1c1a0d1a6817245e5f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194916"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Criar consultas de registos para mensagens B2B nos registos do Azure Monitor para o Azure Logic Apps

Para encontrar AS2, X12 ou EDIFACT mensagens de que está a controlar com [registos do Azure Monitor](../log-analytics/log-analytics-overview.md), pode criar consultas que filtrarão ações com base em critérios específicos. Por exemplo, pode encontrar mensagens com base num número de controlo de intercâmbio específico.

> [!NOTE]
> Passos para saber como realizar estas tarefas com o Microsoft Operations Management Suite (OMS), que é descrito anteriormente, esta página [extinguir em Janeiro de 2019](../azure-monitor/platform/oms-portal-transition.md), substitui esses passos com o Azure Log Analytics em vez disso. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma aplicação de lógica que está configurada com o registo de diagnósticos. Saiba mais [como criar uma aplicação lógica](quickstart-create-first-logic-app-workflow.md) e [como configurar o registo para essa aplicação lógica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Uma conta de integração que está configurada com a monitorização e registo. Saiba mais [como criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [como configurar a monitorização e registo para essa conta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se ainda não o fez, [publicar dados de diagnóstico para os registos do Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) e [configurar a mensagem de controlo nos registos do Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Criar consultas com filtros

Para localizar mensagens com base nos valores de propriedades específicas ou, pode criar consultas que usam filtros. 

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, encontre "log analytics" e selecione **do Log Analytics**.

   ![Selecione o Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Sob **do Log Analytics**, localize e selecione a sua área de trabalho do Log Analytics. 

   ![Selecione a área de trabalho do Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. No menu do seu espaço de trabalho, sob **gerais**, selecione **registos (clássico)** ou **registos**. 

   Este exemplo mostra como utilizar a vista de registos clássica. 
   Se optar **ver registos** no **maximizar a sua experiência de Log Analytics** secção, em **pesquisa e analisar registos de**, obtém o **registos (vista clássica)** . 

   ![Ver registos de clássicos](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. Caixa de edição na consulta, comece a escrever o nome do campo que pretende localizar. Quando começa a digitar, o editor de consultas mostra a correspondências possíveis e as operações que pode utilizar. Depois de criar a sua consulta, escolha **executar** ou prima a tecla Enter.

   Neste exemplo é procura por correspondências no **LogicAppB2B**. 
   Saiba mais sobre [como encontrar dados nos registos do Azure Monitor](../log-analytics/log-analytics-log-searches.md).

   ![Comece a escrever a cadeia de consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Para alterar o período de tempo que pretende ver, no painel esquerdo, selecione na lista de duração ou arraste o controlo de deslize. 

   ![Período de tempo de alteração](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Para adicionar um filtro à sua consulta, escolha **adicionar**. 

   ![Adicionar filtro à consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. Sob **adicionar filtros**, introduza o nome do filtro que pretende localizar. Se encontrar o filtro, selecione esse filtro. No painel esquerdo, escolha **adicionar** novamente.

   Por exemplo, eis uma consulta diferente que procura no **tipo = = "AzureDiagnostics"** eventos e verifica os resultados com base no número de controlo de intercâmbio, selecionando o **event_record_messageProperties_ interchangeControlNumber_s** filtro.

   ![Selecione o valor do filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Depois de escolher **adicionar**, a consulta é atualizada com os seus eventos de filtro selecionado e o valor. 
   Seus resultados anteriores agora são filtrados demasiado. 

   Por exemplo, esta consulta pesquisa **tipo = = "AzureDiagnostics"** localiza os resultados com base no número de controlo do intercâmbio com o **event_record_messageProperties_interchangeControlNumber_s**filtro.

   ![Resultados filtrados](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Guardar consulta

Para guardar a sua consulta na **registos (clássico)** ver, siga estes passos:

1. Da sua consulta sobre o **registos (clássico)** página, selecione **Analytics**. 

   ![Escolha "Analytics"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Na barra de ferramentas de consulta, escolha **guardar**.

   ![Escolha “Save” (“Guardar”).](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Forneça os detalhes sobre a sua consulta, por exemplo, dar um nome, selecione a sua consulta **consulta**e forneça um nome de categoria. Quando tiver terminado, escolha **Save** (Guardar).

   ![Escolha “Save” (“Guardar”).](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Para ver consultas guardadas, volte para a página de consulta. Na barra de ferramentas de consulta, escolha **pesquisas guardadas**.

   ![Escolha "Pesquisas guardadas"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Sob **pesquisas guardadas**, selecione a sua consulta para que pode exibir os resultados. 

   ![Selecione a sua consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Para atualizar a consulta para que possa encontrar resultados diferentes, edite a consulta.

## <a name="find-and-run-saved-queries"></a>Localizar e executar consultas guardadas

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, encontre "log analytics" e selecione **do Log Analytics**.

   ![Selecione o Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Sob **do Log Analytics**, localize e selecione a sua área de trabalho do Log Analytics. 

   ![Selecione a área de trabalho do Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. No menu do seu espaço de trabalho, sob **gerais**, selecione **registos (clássico)** ou **registos**. 

   Este exemplo mostra como utilizar a vista de registos clássica. 

1. Depois de abre a página de consulta, na barra de ferramentas de consulta, escolha **pesquisas guardadas**.

   ![Escolha "Pesquisas guardadas"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Sob **pesquisas guardadas**, selecione a sua consulta para que pode exibir os resultados. 

   ![Selecione a sua consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   A consulta é executada automaticamente, mas se a consulta não é executado por qualquer motivo, no editor de consultas, escolha **executar**.

## <a name="next-steps"></a>Passos Seguintes

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de controlo personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)