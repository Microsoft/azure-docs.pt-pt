---
title: Monitorize aplicações lógicas utilizando registos do Monitor Azure
description: Aplicações lógicas de resolução de problemas através da criação de registos do Monitor Do Azure e da recolha de dados de diagnóstico para aplicações lógicas do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 1948151dfa07e565a929b4506273e3f88f5408c4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907828"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Configurar registos do Monitor Azure e recolher dados de diagnóstico para aplicações lógicas do Azure

Para obter informações mais ricas sobre as suas aplicações lógicas durante o tempo de funcionamento, pode configurar e utilizar [registos do Monitor Azure](../azure-monitor/platform/data-platform-logs.md) para registar e armazenar informações sobre dados e eventos de tempo de execução, tais como eventos de desencadeamento, eventos de execução e eventos de ação num [espaço de trabalho de Log Analytics.](../azure-monitor/platform/resource-logs-collect-workspace.md) [O Azure Monitor](../azure-monitor/overview.md) ajuda-o a monitorizar os ambientes de nuvem e no local para que possa manter mais facilmente a sua disponibilidade e desempenho. Ao utilizar registos do Monitor Azure, pode criar consultas de [registo](../azure-monitor/log-query/log-query-overview.md) que o ajudam a recolher e rever esta informação. Também pode utilizar estes dados de [diagnóstico com outros serviços Azure,](#extend-data)como o Azure Storage e o Azure Event Hubs.

Para configurar o loglog para a sua aplicação lógica, pode ativar o Log Analytics quando criar a [sua aplicação lógica](#logging-for-new-logic-apps), ou pode instalar a [solução logicapps Management](#install-management-solution) no seu espaço de trabalho Log Analytics para aplicações lógicas existentes. Esta solução fornece informações agregadas para as suas aplicações lógicas e inclui detalhes específicos como estado, tempo de execução, estado de resubmissão e IDs de correlação. Em seguida, para permitir a exploração madeireira e criar consultas para estas informações, [crie registos do Monitor Azure](#set-up-resource-logs).

Este artigo mostra como ativar o Log Analytics quando cria aplicações lógicas, como instalar e configurar a solução logicapps Management, e como configurar e criar consultas para registos do Monitor Do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, precisa de um espaço de [trabalho log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md). Se não tiver um espaço de trabalho, aprenda [a criar um espaço](../azure-monitor/learn/quick-create-workspace.md)de trabalho log Analytics .

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Ativar o Log Analytics para novas aplicações lógicas

Pode ligar o Log Analytics quando criar a sua aplicação lógica.

1. No [portal Azure,](https://portal.azure.com)no painel onde fornece as informações para criar a sua aplicação lógica, siga estes passos:

   1. Em **Log Analytics,** selecione **On**.

   1. A partir da lista de **espaço de trabalho do Log Analytics,** selecione o espaço de trabalho onde pretende enviar os dados da sua aplicação lógica.

      ![Indicar as informações da aplicação lógica](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Depois de terminar este passo, o Azure cria a sua aplicação lógica, que está agora associada ao seu espaço de trabalho Log Analytics. Além disso, este passo instala automaticamente a solução de Gestão de Aplicações Lógicas no seu espaço de trabalho.

1. Quando terminar, selecione **Criar**.

1. Depois de executar a sua aplicação lógica, para ver as suas aplicações lógicas, [continue com estes passos.](#view-logic-app-runs)

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Instalar solução de gestão de apps lógicas

Se ligou o Log Analytics quando criou a sua aplicação lógica, ignore este passo. Já tem a solução de Gestão de Aplicações Lógicas instalada no seu espaço de trabalho Log Analytics.

1. Na caixa de pesquisa do [portal Azure,](https://portal.azure.com)introduza `log analytics workspaces`e, em seguida, selecione espaços de **trabalho Log Analytics**.

   ![Selecione "Log Analytics workspaces"](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. Em espaços de **trabalho Log Analytics,** selecione o seu espaço de trabalho.

   ![Selecione seu espaço de trabalho Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. No painel **overview,** em **Iniciar-se com log analytics** > **Configurar soluções de monitorização,** selecione **soluções de visualização**.

   ![No painel de visão geral, selecione "Ver soluções"](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. Em **termos de visão geral,** selecione **Adicionar**.

   ![No painel de visão geral, adicione nova solução](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Depois do **Marketplace** abrir, na caixa de pesquisa, insira `logic apps management`e selecione **Logic Apps Management**.

   ![Do Marketplace, selecione "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. No painel de descrição da solução, selecione **Criar**.

   ![Selecione "Create" para adicionar solução "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Reveja e confirme o espaço de trabalho do Log Analytics onde pretende instalar a solução e selecione **Criar** novamente.

   ![Selecione "Create" para "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Depois de o Azure implementar a solução para o grupo de recursos Azure que contém o seu espaço de trabalho Log Analytics, a solução aparece no painel de resumo do seu espaço de trabalho.

   ![Painel de resumo do espaço de trabalho](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Configurar logs de Azure Monitor

Quando armazena informações sobre eventos e dados em registos do [Monitor Descontrolado,](../azure-monitor/platform/data-platform-logs.md)pode criar consultas de [registo](../azure-monitor/log-query/log-query-overview.md) que o ajudam a encontrar e rever esta informação.

1. Na [portal do Azure](https://portal.azure.com), localize e selecione seu aplicativo lógico.

1. No menu de aplicações lógicas, sob **monitorização,** selecione **definições** de diagnóstico > **Adicione a definição de diagnóstico**.

   ![Em "Monitorização", selecione "Definições de diagnóstico" > "Adicionar definição de diagnóstico"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Para criar a definição, siga estes passos:

   1. Forneça um nome para a definição.

   1. Selecione **Enviar para log Analytics**.

   1. Para **subscrição,** selecione a subscrição Azure que esteja associada ao seu espaço de trabalho Log Analytics.

   1. Para **log analytics workspace,** selecione o espaço de trabalho que pretende utilizar.

   1. Em **diário,** selecione a categoria **WorkflowRuntime,** que especifica a categoria de evento que pretende gravar.

   1. Para selecionar todas as métricas, sob **métrica,** **selecione AllMetrics**.

   1. Quando tiver terminado, selecione **Guardar**.

   Por exemplo:

   ![Selecione Log Analytics espaço de trabalho e dados para registro em log](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Ver app lógica corre o estado

Depois de a sua aplicação lógica ser executado, pode ver os dados sobre essas correções no seu espaço de trabalho Log Analytics.

1. No [portal Azure,](https://portal.azure.com)encontre e abra o seu espaço de trabalho Log Analytics.

1. No menu do seu espaço de trabalho, selecione **workspace resumo** > **Logic Apps Management**.

   ![Estado de execução de aplicativológico lógico e contagem](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Se o azulejo logic apps Management não mostrar imediatamente os resultados após uma corrida, tente selecionar **Refresh** ou esperar por um curto período de tempo antes de tentar novamente.

   Aqui, as suas aplicações lógicas são agruparadas pelo nome ou pelo estado de execução. Esta página também mostra detalhes sobre falhas em ações ou gatilhos para a aplicação lógica executado.

   ![Resumo de estado para a sua aplicação lógica executa](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Para ver todas as corridas para uma aplicação ou estado de lógica específica, selecione a linha para essa aplicação lógica ou estado.

   Aqui está um exemplo que mostra todas as corridas para uma aplicação lógica específica:

   ![Ver aplicações lógicas e estado](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   Para ações onde [configura propriedades rastreadas,](#extend-data)também pode ver essas propriedades selecionando **View** na coluna **Tracked Properties.** Para pesquisar as propriedades rastreadas, utilize o filtro da coluna.

   ![Ver propriedades rastreadas para uma aplicação lógica](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > Propriedades rastreadas ou eventos concluídos podem sofrer atrasos de 10 a 15 minutos antes de aparecer no seu espaço de trabalho Log Analytics.
   > Além disso, a capacidade de **Reenviar** nesta página não está disponível.

1. Para filtrar os seus resultados, pode efetuar a filtragem do lado do cliente e do servidor.

   * **Filtro do lado do cliente**: Para cada coluna, selecione os filtros que deseja, por exemplo:

     ![Filtros de colunas de exemplo](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Filtro do lado do servidor**: Para selecionar uma janela de tempo específica ou para limitar o número de execuções que aparecem, utilize o controlo de âmbito na parte superior da página. Por padrão, apenas 1.000 registos aparecem de cada vez.

     ![Mude a janela do tempo](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Para ver todas as ações e detalhes para uma execução específica, selecione a linha para uma aplicação lógica executada.

   Aqui está um exemplo que mostra todas as ações e gatilhos para uma aplicação lógica específica executada:

   ![Ver ações para uma aplicação lógica executada](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Envie dados de diagnóstico para Azure Storage e Azure Event Hubs

Juntamente com os logs de Azure Monitor, você pode estender como usar os dados de diagnóstico do aplicativo lógico com outros serviços do Azure, por exemplo:

* [Registos de recursos do Archive Azure para conta de armazenamento](../azure-monitor/platform/resource-logs-collect-storage.md)
* [Stream Azure logs plataforma para Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Em seguida, você pode obter o monitoramento em tempo real usando a telemetria e a análise de outros serviços, como [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e [Power bi](../azure-monitor/platform/powerbi.md). Por exemplo:

* [Transmitir dados de hubs de eventos para Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analise dados de streaming com Stream Analytics e crie um painel de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Com base nos locais onde pretende enviar dados de diagnóstico, certifique-se de que cria primeiro uma conta de [armazenamento Azure](../storage/common/storage-create-storage-account.md) ou cria um hub de [eventos Azure](../event-hubs/event-hubs-create.md). Em seguida, pode selecionar os destinos onde pretende enviar esses dados. Os períodos de retenção só se aplicam quando se utiliza uma conta de armazenamento.

![Enviar dados para a conta de armazenamento do Azure ou Hub de eventos](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Eventos de diagnóstico do Azure Monitor

Cada evento de diagnóstico tem detalhes sobre seu aplicativo lógico e esse evento, por exemplo, o status, a hora de início, a hora de término e assim por diante. Para configurar programáticamente a monitorização, o rastreio e o registo, pode utilizar esta informação com a [API REST para Aplicações Lógicas Azure](https://docs.microsoft.com/rest/api/logic) e a [API REST para O Monitor Azure](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows). Você também pode usar as propriedades `clientTrackingId` e `trackedProperties`, que aparecem em 

* `clientTrackingId`: se não for fornecido, o Azure gera automaticamente essa ID e correlaciona eventos em uma execução de aplicativo lógico, incluindo quaisquer fluxos de trabalho aninhados que são chamados do aplicativo lógico. Pode especificar manualmente este ID num gatilho, passando um cabeçalho `x-ms-client-tracking-id` com o seu valor de identificação personalizado no pedido de gatilho. Você pode usar um gatilho de solicitação, um gatilho HTTP ou um gatilho de webhook.

* `trackedProperties`: Para rastrear inputs ou saídas em dados de diagnóstico, pode adicionar uma secção `trackedProperties` a uma ação, quer utilizando o Logic App Designer ou diretamente na definição JSON da sua aplicação lógica. As propriedades rastreadas podem rastrear apenas entradas e saídas de uma única ação, mas você pode usar as propriedades `correlation` de eventos para correlacionar entre ações em uma execução. Para rastrear mais de uma propriedade, uma ou mais propriedades, adicione a secção `trackedProperties` e as propriedades que pretende para a definição de ação.

  Aqui está um exemplo que mostra como a definição de ação **variável Inicialize** inclui propriedades rastreadas a partir da entrada da ação onde a entrada é uma matriz, não um registo.

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

  Este exemplo mostra múltiplas propriedades rastreadas:

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

Este exemplo mostra como o evento `ActionCompleted` inclui os atributos `clientTrackingId` e `trackedProperties`:

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
* [Monitor de mensagens B2B com registos do Monitor Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)