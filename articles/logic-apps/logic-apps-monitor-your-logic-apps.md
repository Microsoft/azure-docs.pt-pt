---
title: Verificar o estado, configurar o registo e obter alertas - Azure Logic Apps | Documentos da Microsoft
description: Monitorizar o estado, registos de dados de diagnóstico e configurar alertas para o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.date: 07/21/2017
ms.openlocfilehash: 6a8c4583d86b41e767e8a2057bae4cef8943a1c1
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193423"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Monitorizar o estado, configurar o registo de diagnóstico e ative alertas para o Azure Logic Apps

Depois de [criar e executar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md), pode verificar o histórico de execuções, o histórico do acionador, o estado e o desempenho. Para a monitorização de eventos em tempo real e a depuração mais avançada, configure [registo de diagnósticos](#azure-diagnostics) para a aplicação lógica. Dessa forma, pode [localizar e ver eventos](#find-events), como eventos de Acionador, eventos de execução e eventos de ação. Também pode utilizá-lo [dados de diagnóstico com outros serviços](#extend-diagnostic-data), como o armazenamento do Azure e os Hubs de eventos do Azure. 

Para obter notificações sobre falhas ou outros problemas possíveis, configure [alertas](#add-azure-alerts). Por exemplo, pode criar um alerta que Deteta "quando é executada mais do que cinco falha numa hora." Também pode definir a monitorização, controlo e registo através de programação, utilizando [definições de eventos de diagnóstico do Azure e propriedades](#diagnostic-event-properties).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Ver execuções e histórico de acionadores para a aplicação lógica

1. Para localizar a sua aplicação lógica no [portal do Azure](https://portal.azure.com), no menu principal do Azure, escolha **todos os serviços**. Na caixa de pesquisa, escreva "logic apps" e escolha **Logic apps**.

   ![Encontre a sua aplicação lógica](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   O portal do Azure mostra todas as aplicações lógicas que estão associadas a sua subscrição do Azure. 

2. Selecione a sua aplicação lógica, em seguida, escolha **descrição geral**.

   O portal do Azure mostra o histórico de execuções e o histórico de acionadores para a aplicação lógica. Por exemplo:

   ![Histórico de Acionador e histórico de execuções de aplicação lógica](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Histórico de execuções** mostra todas as execuções da aplicação lógica. 
   * **Acionar histórico** mostra todas as atividades de Acionador para a aplicação lógica.

   Para descrições de estado, consulte [resolver problemas da aplicação lógica](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Se não encontrar os dados que esperava, na barra de ferramentas, escolha **atualizar**.

3. Para ver os passos de uma execução específica, em **histórico de execuções**, selecione que são executados. 

   O modo de exibição de monitor mostra cada etapa em que são executados. Por exemplo:

   ![Ações para uma execução específica](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Para obter mais detalhes sobre a mesma, escolha **detalhes da execução**. Estas informações resume os passos, estado, entradas e saídas da execução. 

   ![Escolha "Detalhes de execução"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Por exemplo, pode obter a execução **ID de correlação**, que talvez precise quando utiliza o [API de REST para o Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Para obter detalhes sobre um passo específico, escolha esse passo. Agora pode rever os detalhes como entradas, saídas e erros que ocorreram para esse passo. Por exemplo:

   ![Detalhes do passo](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Todos os detalhes do tempo de execução e eventos são encriptados no serviço de aplicações lógicas. Eles são desencriptados apenas quando um utilizador pede para ver esses dados. Também pode controlar o acesso a esses eventos com [controlo de acesso de controlo (RBAC)](../role-based-access-control/overview.md).

6. Para obter detalhes sobre um evento de Acionador específico, volte para o **descrição geral** painel. Sob **acionar histórico**, selecione o evento acionador. Agora pode rever detalhes como entradas e saídas, por exemplo:

   ![Detalhes de resultado do evento de Acionador](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Ativar diagnósticos de registo para a aplicação lógica

Para depuração mais avançada com detalhes de tempo de execução e eventos, pode configurar com o registo de diagnósticos [registos do Azure Monitor](../log-analytics/log-analytics-overview.md). O Azure Monitor é um serviço no Azure que monitoriza a sua cloud e ambientes para o ajudar a manter a disponibilidade e desempenho no local. 

Antes de começar, tem de ter uma área de trabalho do Log Analytics. Saiba mais [como criar uma área de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. Na [portal do Azure](https://portal.azure.com), localize e selecione a sua aplicação lógica. 

2. No menu de painel de aplicação lógica, sob **monitorização**, escolha **diagnóstico** > **definições de diagnóstico**.

   ![Vá para a monitorização, diagnóstico, definições de diagnóstico](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. Sob **as definições de diagnóstico**, escolha **no**.

   ![Ativar registos de diagnóstico](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Agora, selecione a categoria de eventos e de área de trabalho do Log Analytics para o registo conforme mostrado:

   1. Selecione **enviar para o Log Analytics**. 
   2. Sob **do Log Analytics**, escolha **configurar**. 
   3. Sob **áreas de trabalho do OMS**, selecione a área de trabalho para utilizar para o registo.
   > [!NOTE]
   > As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.
   4. Sob **Log**, selecione a **WorkflowRuntime** categoria.
   5. Escolha o intervalo de métrica.
   6. Quando tiver terminado, escolha **Save** (Guardar).

   ![Selecione a área de trabalho do Log Analytics e dados de registo](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Agora, pode encontrar eventos e outros dados para eventos de Acionador, execute os eventos e eventos de ação.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Encontrar eventos e os dados para a aplicação lógica

Para localizar e ver eventos na sua aplicação lógica, como acionar eventos, executados os eventos e eventos de ação, siga estes passos.

1. Na [portal do Azure](https://portal.azure.com), escolha **todos os serviços**. Procure "log analytics", em seguida, escolha **do Log Analytics** conforme mostrado aqui:

   ![Escolha "Log Analytics"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. Sob **do Log Analytics**, localize e selecione a sua área de trabalho do Log Analytics. 

   ![Selecione a sua área de trabalho do Log Analytics](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. Sob **gerenciamento**, escolha **pesquisa de registos**.

   ![Escolha "Pesquisa de registos"](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. Na caixa de pesquisa, especifique um campo que pretende localizar e prima **Enter**. Quando começa a digitar, verá correspondências possíveis e operações que pode utilizar. 

   Por exemplo, para localizar os eventos de 10 principais que aconteceram, introduza e selecione a consulta de pesquisa: **pesquisar categoria = = "WorkflowRuntime" | limitar 10**

   ![Introduza a cadeia de procura](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Saiba mais sobre [como encontrar dados nos registos do Azure Monitor](../log-analytics/log-analytics-log-searches.md).

5. Na página de resultados, na barra esquerda, escolha o período de tempo que pretende ver.
Para refinar a sua consulta por adicionar um filtro, escolha **+ adicionar**.

   ![Escolha o período de tempo para os resultados da consulta](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. Sob **adicionar filtros**, introduza o nome do filtro para que possa encontrar o filtro que pretende. Selecione o filtro e escolha **+ adicionar**.

   Este exemplo utiliza a palavra "status" para encontrar eventos de falha sob **AzureDiagnostics**.
   Aqui o filtro para **status_s** já está selecionada.

   ![Selecione um filtro](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. Na barra esquerda, selecione o valor do filtro que pretende utilizar e escolha **aplicar**.

   ![Selecione o valor do filtro, selecione "Aplicar"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Agora é volte para a consulta que está a criar. A consulta é atualizada com o filtro selecionado e o valor. Seus resultados anteriores agora são filtrados demasiado.

   ![Voltar à sua consulta com resultados filtrados](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Para guardar a consulta para utilização futura, escolha **guardar**. Saiba mais [como pode guardar a sua consulta](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Expandir como e onde utiliza dados de diagnóstico com outros serviços

Juntamente com os registos do Azure Monitor, pode expandir como utilizar dados de diagnóstico da sua aplicação lógica com outros serviços do Azure, por exemplo: 

* [Arquivar registos do diagnóstico do Azure no armazenamento do Azure](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Registos de diagnóstico do Azure Stream para os Hubs de eventos do Azure](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

Pode, em seguida, a monitorização utilizando a telemetria e de análise a partir de outros serviços, como get em tempo real [do Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e [Power BI](../azure-monitor/platform/powerbi.md). Por exemplo:

* [Dados de Stream dos Hubs de eventos para o Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analisar dados de transmissão em fluxo com o Stream Analytics e criar um dashboard de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Com base nas opções que pretende configurar, certifique-se de que primeiro [criar uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md) ou [criar um hub de eventos do Azure](../event-hubs/event-hubs-create.md). Em seguida, selecione as opções para onde pretende enviar dados de diagnóstico:

![Enviar dados para o hub de evento ou a conta de armazenamento do Azure](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Os períodos de retenção aplicam-se apenas quando optar por utilizar uma conta de armazenamento.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Configurar alertas para a aplicação lógica

Para monitorizar métricas específicas ou em limites excedidos para a aplicação lógica, configure [alertas no Azure](../azure-monitor/platform/alerts-overview.md). Saiba mais sobre [métricas no Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Posso configurar alertas sem [registos do Azure Monitor](../log-analytics/log-analytics-overview.md), siga estes passos. Para os critérios de alertas e ações, mais avançados [configurar os registos do Azure Monitor](#azure-diagnostics) demasiado.

1. No menu de painel de aplicação lógica, sob **monitorização**, escolha **diagnóstico** > **regras de alerta** > **Adicionar alerta**conforme mostrado aqui:

   ![Adicionar um alerta para a aplicação lógica](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. Sobre o **adicionar uma regra de alerta** painel, criar o alerta, conforme mostrado:

   1. Sob **recursos**, selecione a sua aplicação lógica, se não estiver já selecionada. 
   2. Atribua um nome e descrição para o alerta.
   3. Selecione um **métrica** ou o evento que pretende controlar.
   4. Selecione um **condição**, especifique um **limiar** para a métrica e selecione o **período** para monitorizar esta métrica.
   5. Selecione se pretende enviar correio para o alerta. 
   6. Especifique quaisquer outros endereços de e-mail de envio do alerta. 
   Também pode especificar um URL do webhook onde pretende enviar o alerta.

   Por exemplo, esta regra envia um alerta quando cinco ou mais execuções falharem numa hora:

   ![Criar regra de alerta de métrica](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Para executar uma aplicação lógica a partir de um alerta, pode incluir a [acionador de pedido](../connectors/connectors-native-reqres.md) no fluxo de trabalho, que lhe permite realizar tarefas, como nestes exemplos:
> 
> * [Mensagem para Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Enviar uma mensagem de texto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Adicione uma mensagem numa fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Definições de eventos de diagnóstico do Azure e os detalhes

Cada evento do diagnóstico tem detalhes sobre esse evento, por exemplo, o estado e a sua aplicação lógica, hora de início, hora de fim e assim por diante. Para configurar, por meio de programação, a monitorização, o controlo e o registo, pode utilizar estes detalhes com o [API REST para o Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) e o [API REST para o diagnóstico do Azure](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows).

Por exemplo, o `ActionCompleted` evento tem o `clientTrackingId` e `trackedProperties` propriedades que pode utilizar para monitorização e controlo:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
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
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
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
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: Se não for fornecido, automaticamente, o Azure gera este ID e correlaciona eventos numa execução da aplicação lógica, incluindo quaisquer fluxos de trabalho aninhados que são chamados a partir da aplicação lógica. Pode especificar manualmente este ID a partir de um acionador, passando um `x-ms-client-tracking-id` cabeçalho com o valor de ID personalizado no pedido de Acionador. Pode utilizar um acionador de pedido, o acionador HTTP ou o acionador de webhook.

* `trackedProperties`: Para controlar as entradas ou saídas nos dados de diagnóstico, pode adicionar propriedades controladas para ações na definição de JSON da sua aplicação lógica. Propriedades controladas podem controlar apenas uma única ação entradas e saídas, mas pode usar o `correlation` propriedades de eventos para correlacionar entre ações numa execução.

  Para controlar uma ou mais propriedades, adicione o `trackedProperties` secção e as propriedades que pretende que a definição de ação. Por exemplo, suponha que pretende controlar dados como um "ID da ordem" na sua telemetria:

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```

## <a name="next-steps"></a>Passos Seguintes

* [Criar modelos para a implementação da aplicação lógica e a gestão de versões](../logic-apps/logic-apps-create-deploy-template.md)
* [Cenários B2B com Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Monitorizar mensagens B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
