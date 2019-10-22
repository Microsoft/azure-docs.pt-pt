---
title: Criar consultas de acompanhamento para mensagens B2B-aplicativos lógicos do Azure
description: Criar consultas que acompanhem mensagens AS2, X12 e EDIFACT no Azure Log Analytics para aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 7d7bb53d24a113ea78b5bac3f9682fbb61ce2de9
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680102"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Criar consultas de acompanhamento de mensagens B2B em logs de Azure Monitor para aplicativos lógicos do Azure

Para localizar mensagens AS2, X12 ou EDIFACT que você está controlando com [os logs de Azure monitor](../log-analytics/log-analytics-overview.md), você pode criar consultas que filtram ações com base em critérios específicos. Por exemplo, você pode encontrar mensagens com base em um número de controle de intercâmbio específico.

> [!NOTE]
> Esta página descreveu anteriormente as etapas para executar essas tarefas com o Microsoft Operations Management Suite (OMS), que está sendo [desativado em janeiro de 2019](../azure-monitor/platform/oms-portal-transition.md), substitui essas etapas com o Azure log Analytics em vez disso. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo lógico que é configurado com o log de diagnóstico. Saiba [como criar um aplicativo lógico](quickstart-create-first-logic-app-workflow.md) e [como configurar o log para esse aplicativo lógico](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Uma conta de integração que é configurada com monitoramento e registro em log. Saiba [como criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [como configurar o monitoramento e o registro em log para essa conta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se você ainda não fez isso, [publique os dados de diagnóstico para Azure monitor logs](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) e [Configure o rastreamento de mensagens nos logs de Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Criar consultas com filtros

Para localizar mensagens com base em Propriedades ou valores específicos, você pode criar consultas que usam filtros. 

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, localize "log Analytics" e selecione **log Analytics**.

   ![Selecionar Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Em **log Analytics**, localize e selecione seu espaço de trabalho do log Analytics. 

   ![Selecionar Log Analytics espaço de trabalho](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. No menu do espaço de trabalho, em **geral**, selecione **logs (clássicos)** ou **logs**. 

   Este exemplo mostra como usar a exibição de logs clássicos. 
   Se você escolher **Exibir logs** na seção **maximizar sua experiência de log Analytics** , em **Pesquisar e analisar logs**, você obterá os **logs (exibição clássica)** . 

   ![Exibir logs clássicos](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. Na caixa de edição de consulta, comece a digitar o nome do campo que você deseja localizar. Quando você começa a digitar, o editor de consultas mostra as possíveis correspondências e operações que podem ser usadas. Depois de criar a consulta, escolha **executar** ou pressione a tecla Enter.

   Este exemplo procura por correspondências em **LogicAppB2B**. 
   Saiba mais sobre [como localizar dados em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md).

   ![Iniciar digitação de cadeia de consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Para alterar o período de tempo que você deseja exibir, no painel esquerdo, selecione na lista duração ou arraste o controle deslizante. 

   ![Alterar período de tempo](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Para adicionar um filtro à sua consulta, escolha **Adicionar**. 

   ![Adicionar filtro à consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. Em **adicionar filtros**, insira o nome do filtro que você deseja localizar. Se você encontrar o filtro, selecione esse filtro. No painel esquerdo, escolha **Adicionar** novamente.

   Por exemplo, aqui está uma consulta diferente que pesquisa nos eventos **Type = = "AzureDiagnostics"** e localiza resultados com base no número de controle de intercâmbio selecionando o filtro **event_record_messageProperties_interchangeControlNumber_s** .

   ![Selecionar valor de filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Depois de escolher **Adicionar**, sua consulta será atualizada com o evento e o valor de filtro selecionado. 
   Os resultados anteriores agora são filtrados também. 

   Por exemplo, essa consulta pesquisa o **tipo = = "AzureDiagnostics"** e localiza os resultados com base em um número de controle de intercâmbio usando o filtro **event_record_messageProperties_interchangeControlNumber_s** .

   ![Resultados filtrados](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Salvar consulta

Para salvar sua consulta no modo de exibição **logs (clássico)** , siga estas etapas:

1. Em sua consulta na página **logs (clássico)** , escolha **análise**. 

   ![Escolha "análise"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Na barra de ferramentas de consulta, escolha **salvar**.

   ![Escolha “Save” (“Guardar”).](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Forneça os detalhes sobre sua consulta, por exemplo, dê um nome à sua consulta, selecione **consulta**e forneça um nome de categoria. Quando tiver terminado, escolha **Save** (Guardar).

   ![Escolha “Save” (“Guardar”).](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Para exibir as consultas salvas, volte para a página consulta. Na barra de ferramentas de consulta, escolha **pesquisas salvas**.

   ![Escolha "pesquisas salvas"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Em **pesquisas salvas**, selecione sua consulta para que você possa exibir os resultados. 

   ![Selecione sua consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Para atualizar a consulta para que você possa encontrar resultados diferentes, edite a consulta.

## <a name="find-and-run-saved-queries"></a>Localizar e executar consultas salvas

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, localize "log Analytics" e selecione **log Analytics**.

   ![Selecionar Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Em **log Analytics**, localize e selecione seu espaço de trabalho do log Analytics. 

   ![Selecionar Log Analytics espaço de trabalho](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. No menu do espaço de trabalho, em **geral**, selecione **logs (clássicos)** ou **logs**. 

   Este exemplo mostra como usar a exibição de logs clássicos. 

1. Depois que a página de consulta for aberta, na barra de ferramentas de consulta, escolha **pesquisas salvas**.

   ![Escolha "pesquisas salvas"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Em **pesquisas salvas**, selecione sua consulta para que você possa exibir os resultados. 

   ![Selecione sua consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   A consulta é executada automaticamente, mas se a consulta não for executada por qualquer motivo, no editor de consultas, escolha **executar**.

## <a name="next-steps"></a>Passos seguintes

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento personalizados](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)