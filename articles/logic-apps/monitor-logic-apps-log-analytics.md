---
title: Monitorize aplicações lógicas utilizando registos do Azure Monitor
description: Aplicativos de lógica de resolução de problemas, criando registos do Azure Monitor e recolhendo dados de diagnóstico para apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 09/24/2020
ms.openlocfilehash: d72e3ffc8698d9f3a2df170bd87f2969475ca9ed
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580688"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Configurar registos do Azure Monitor e recolher dados de diagnóstico para apps Azure Logic

Para obter informações mais ricas sobre as suas aplicações lógicas durante o tempo de execução, pode configurar e utilizar [registos do Azure Monitor](../azure-monitor/logs/data-platform-logs.md) para gravar e armazenar informações sobre dados e eventos de tempo de execução, tais como eventos de desencadeamento, eventos de execução e eventos de ação num [espaço de trabalho do Log Analytics.](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) [O Azure Monitor](../azure-monitor/overview.md) ajuda-o a monitorizar os ambientes da nuvem e do local para que possa manter mais facilmente a sua disponibilidade e desempenho. Ao utilizar registos do Azure Monitor, pode criar [consultas de registo](../azure-monitor/logs/log-query-overview.md) que o ajudam a recolher e rever estas informações. Também pode [utilizar estes dados de diagnóstico com outros serviços Azure](#extend-data), como o Azure Storage e o Azure Event Hubs.

Para configurar o registo para a sua aplicação lógica, pode [ativar o Log Analytics quando criar a sua aplicação lógica](#logging-for-new-logic-apps), ou pode [instalar a solução De Gestão de Aplicações Lógicas](#install-management-solution) no seu espaço de trabalho Log Analytics para aplicações lógicas existentes. Esta solução fornece informações agregadas para a sua aplicação lógica e inclui detalhes específicos como estado, tempo de execução, estado de resubmissão e IDs de correlação. Em seguida, para permitir o registo e a criação de consultas para esta informação, [crie registos do Azure Monitor](#set-up-resource-logs).

Este artigo mostra como ativar o Log Analytics em novas aplicações lógicas e aplicações lógicas existentes, como instalar e configurar a solução de Gestão de Aplicações Lógicas, e como configurar e criar consultas para registos do Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, precisa de um espaço de [trabalho log analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Se não tiver um espaço de trabalho, aprenda [a criar um espaço](../azure-monitor/logs/quick-create-workspace.md)de trabalho log analytics .

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Ativar o Log Analytics para novas aplicações lógicas

Pode ligar o Log Analytics quando criar a sua aplicação lógica.

1. No [portal Azure,](https://portal.azure.com)no painel onde fornece a informação para criar a sua aplicação lógica, siga estes passos:

   1. No **Âmbito do Log Analytics**, selecione **On**.

   1. A partir da lista de **espaços de trabalho do Log Analytics,** selecione o espaço de trabalho onde pretende enviar os dados da sua aplicação lógica.

      ![Indicar as informações da aplicação lógica](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Depois de terminar este passo, o Azure cria a sua aplicação lógica, que está agora associada ao seu espaço de trabalho Log Analytics. Além disso, este passo instala automaticamente a solução De Gestão de Aplicações Lógicas no seu espaço de trabalho.

1. Quando concluir, selecione **Criar**.

1. Depois de executar a sua aplicação lógica, para ver as suas aplicações lógicas, [continue com estes passos.](#view-logic-app-runs)

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Instalar solução de Gestão de Aplicações Lógicas

Se ligou o Log Analytics quando criou a sua aplicação lógica, ignore este passo. Já tem a solução De Gestão de Aplicações Lógicas instalada no seu espaço de trabalho Log Analytics.

1. Na caixa de pesquisa do [portal Azure,](https://portal.azure.com)insira `log analytics workspaces` e, em seguida, selecione **log analytics espaços de trabalho**.

   ![Selecione "Log Analytics workspaces"](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. Nos **espaços de trabalho do Log Analytics,** selecione o seu espaço de trabalho.

   ![Selecione o seu espaço de trabalho Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. No painel **de visão geral,** em Iniciar com soluções de monitorização de configuração **log**  >  **Analytics,** selecione **Ver soluções**.

   ![No painel geral, selecione "Ver soluções"](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. Sob **visão geral**, selecione **Adicionar**.

   ![No painel geral, adicione nova solução](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Depois de o **Marketplace** abrir, na caixa de pesquisa, insira `logic apps management` e selecione **Logic Apps Management**.

   ![A partir do Marketplace, selecione "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. No painel de descrição da solução, **selecione Criar**.

   ![Selecione "Criar" para adicionar solução "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Reveja e confirme o espaço de trabalho Log Analytics onde pretende instalar a solução e selecione **Criar** novamente.

   ![Selecione "Criar" para "Gestão de Aplicações Lógicas"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Depois de o Azure implementar a solução para o grupo de recursos Azure que contém o seu espaço de trabalho Log Analytics, a solução aparece no painel de resumo do seu espaço de trabalho.

   ![Painel de resumo do espaço de trabalho](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Configurar registos do Monitor Azure

Quando armazena informações sobre eventos de tempo de execução e dados nos [registos do Azure Monitor,](../azure-monitor/logs/data-platform-logs.md)pode criar [consultas de registo](../azure-monitor/logs/log-query-overview.md) que o ajudam a encontrar e rever estas informações.

> [!NOTE]
> Depois de ativar as definições de diagnóstico, os dados de diagnóstico podem não fluir até 30 minutos para os registos no destino especificado, como Log Analytics, centro de eventos ou conta de armazenamento. Este atraso significa que os dados de diagnóstico deste período podem não existir para que possa rever. Eventos concluídos e [propriedades rastreadas](#extend-data) podem não aparecer no seu espaço de trabalho Log Analytics durante 10-15 minutos.

1. No [portal Azure,](https://portal.azure.com)encontre e selecione a sua aplicação lógica.

1. No menu de aplicativos logicamente, em **Monitorização,** selecione **Definições de diagnóstico**  >  **Adicione a definição de diagnóstico**.

   ![Em "Monitorização", selecione "Definições de diagnóstico" > "Adicionar definição de diagnóstico"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Para criar a definição, siga estes passos:

   1. Forneça um nome para a definição.

   1. Selecione **Enviar para Registar Análises**.

   1. Para **Subscrição**, selecione a subscrição Azure que está associada ao seu espaço de trabalho Log Analytics.

   1. Para **Log Analytics Workspace,** selecione o espaço de trabalho que pretende utilizar.

   1. No **registo**, selecione a categoria **WorkflowRuntime,** que especifica a categoria de evento que pretende gravar.

   1. Para selecionar todas as métricas, em **métrica,** selecione **AllMetrics**.

   1. Quando tiver terminado, selecione **Guardar**.

   Por exemplo:

   ![Selecione Log Analytics espaço de trabalho e dados para registar](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Ver o estado da aplicação lógica corre

Depois de a sua aplicação lógica ser executado, pode ver os dados sobre essas correções no seu espaço de trabalho Log Analytics.

1. No [portal Azure,](https://portal.azure.com)encontre e abra o seu espaço de trabalho Log Analytics.

1. No menu do seu espaço de trabalho, selecione **Workspace Resumo**  >  **Logic Apps Management**.

   ![Estado de execução de aplicativos lógicos e contagem](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Se o azulejo logic Apps Management não apresentar imediatamente resultados após uma execução, tente selecionar **Refresh** ou aguarde um curto período de tempo antes de tentar novamente.

   Aqui, as suas aplicações lógicas são agrupadas pelo nome ou pelo estado de execução. Esta página também mostra detalhes sobre falhas em ações ou gatilhos para a aplicação lógica executado.

   ![Resumo do estado para a sua aplicação lógica executa](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Para visualizar todas as corridas para uma aplicação ou estado de lógica específica, selecione a linha para essa aplicação lógica ou estado.

   Aqui está um exemplo que mostra todas as corridas para uma aplicação lógica específica:

   ![Ver corre e estado da aplicação lógica](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   > [!NOTE]
   > A capacidade **de Resubmit** nesta página não está disponível neste momento.

   Para ações onde [configura propriedades rastreadas,](#extend-data)também pode ver essas propriedades selecionando **Ver** na coluna **Propriedades Rastreadas.** Para pesquisar as propriedades rastreadas, utilize o filtro da coluna.

   ![Ver propriedades rastreadas para uma aplicação lógica](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

1. Para filtrar os seus resultados, pode efetuar a filtragem do lado do cliente e do servidor.

   * **Filtro do lado do cliente**: Para cada coluna, selecione os filtros que pretende, por exemplo:

     ![Filtros de colunas de exemplo](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Filtro do lado do servidor**: Para selecionar uma janela de tempo específica ou para limitar o número de execuções que aparecem, utilize o controlo de âmbito na parte superior da página. Por padrão, apenas 1.000 registos aparecem de cada vez.

     ![Alterar a janela do tempo](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Para ver todas as ações e os seus detalhes para uma execução específica, selecione a linha para uma aplicação lógica executada.

   Aqui está um exemplo que mostra todas as ações e gatilhos para uma aplicação lógica específica executada:

   ![Ver ações para uma aplicação lógica executada](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Envie dados de diagnóstico para Azure Storage e Azure Event Hubs

Juntamente com os registos do Azure Monitor, pode estender a forma como utiliza os dados de diagnóstico da sua aplicação lógica com outros serviços Azure, por exemplo:

* [Registos de recursos do Archive Azure para a conta de armazenamento](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
* [Stream Azure plataforma registro para Azure Event Hubs](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

Em seguida, pode obter monitorização em tempo real utilizando telemetria e análise de outros serviços, como [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e [Power BI](../azure-monitor/visualize/powerbi.md). Por exemplo:

* [Transmitir dados de Centros de Eventos para Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analise os dados de streaming com o Stream Analytics e crie um dashboard de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Com base nos locais onde pretende enviar dados de diagnóstico, certifique-se de que cria primeiro [uma conta de armazenamento Azure](../storage/common/storage-account-create.md) ou cria um centro de [eventos Azure](../event-hubs/event-hubs-create.md). Em seguida, pode selecionar os destinos para onde pretende enviar esses dados. Os períodos de retenção aplicam-se apenas quando se utiliza uma conta de armazenamento.

![Enviar dados para a conta de armazenamento da Azure ou centro de eventos](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Eventos de diagnóstico do Azure Monitor

Cada evento de diagnóstico tem detalhes sobre a sua aplicação lógica e esse evento, por exemplo, o estado, hora de início, tempo de fim, e assim por diante. Para configurar programáticamente monitorização, rastreio e registo, pode utilizar esta informação com a [API REST para Apps Lógicas Azure](/rest/api/logic) e a [API REST para O Monitor Azure.](../azure-monitor/essentials/metrics-supported.md#microsoftlogicworkflows) Você também pode usar as `clientTrackingId` `trackedProperties` propriedades e propriedades, que aparecem em 

* `clientTrackingId`: Se não for fornecido, o Azure gera automaticamente este ID e correlaciona eventos através de uma aplicação lógica executada, incluindo quaisquer fluxos de trabalho aninhados que são chamados da aplicação lógica. Pode especificar manualmente este ID num gatilho passando um `x-ms-client-tracking-id` cabeçalho com o seu valor de ID personalizado no pedido do gatilho. Pode utilizar um gatilho de pedido, um gatilho HTTP ou um gatilho webhook.

* `trackedProperties`: Para rastrear entradas ou saídas em dados de diagnóstico, pode adicionar uma `trackedProperties` secção a uma ação, quer utilizando o Design de Aplicações Lógicas, quer diretamente na definição JSON da sua aplicação lógica. As propriedades rastreadas podem rastrear apenas as entradas e saídas de uma única ação, mas você pode usar as `correlation` propriedades dos eventos para correlacionar entre ações em uma corrida. Para rastrear mais de uma propriedade, uma ou mais propriedades, adicione a `trackedProperties` secção e as propriedades que deseja à definição de ação.

  Aqui está um exemplo que mostra como a definição de ação **variável Initialize** inclui propriedades rastreadas a partir da entrada da ação onde a entrada é uma matriz, não um registo.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  Este exemplo mostra várias propriedades rastreadas:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

Este exemplo mostra como o `ActionCompleted` evento inclui os `clientTrackingId` atributos e `trackedProperties` atributos:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Passos seguintes

* [Criar consultas de monitorização e rastreio](../logic-apps/create-monitoring-tracking-queries.md)
* [Monitorizar mensagens B2B com os registos do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)
