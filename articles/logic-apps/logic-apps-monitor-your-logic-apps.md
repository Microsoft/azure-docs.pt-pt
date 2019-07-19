---
title: Verificar o status, configurar o registro em log e obter alertas-aplicativos lógicos do Azure | Microsoft Docs
description: Monitorar status, registrar dados de diagnóstico e configurar alertas para aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.date: 07/21/2017
ms.openlocfilehash: 851943ad967a79a6800a899b31d0cf9f90a6889b
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312247"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Monitorar o status, configurar o log de diagnóstico e ativar alertas para aplicativos lógicos do Azure

Depois de [criar e executar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md), você pode verificar seu histórico de execuções, o histórico de gatilho, o status e o desempenho. Para monitoramento de eventos em tempo real e depuração mais avançada, configure o [log de diagnóstico](#azure-diagnostics) para seu aplicativo lógico. Dessa forma, você pode [encontrar e exibir eventos](#find-events), como eventos de gatilho, eventos de execução e eventos de ação. Você também pode usar esses [dados de diagnóstico com outros serviços](#extend-diagnostic-data), como o armazenamento do Azure e os hubs de eventos do Azure. 

Para obter notificações sobre falhas ou outros possíveis problemas, configure [alertas](#add-azure-alerts). Por exemplo, você pode criar um alerta que detecta "quando mais de cinco execuções falham em uma hora". Você também pode configurar o monitoramento, o rastreamento e o registro em log de forma programática usando [diagnóstico do Azure configurações de evento e propriedades](#diagnostic-event-properties).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Exibir execuções e o histórico de gatilho para seu aplicativo lógico

1. Para localizar seu aplicativo lógico no [portal do Azure](https://portal.azure.com), no menu principal do Azure, escolha **todos os serviços**. Na caixa de pesquisa, digite "aplicativos lógicos" e escolha **aplicativos lógicos**.

   ![Localizar seu aplicativo lógico](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   O portal do Azure mostra todos os aplicativos lógicos associados à sua assinatura do Azure. 

2. Selecione seu aplicativo lógico e, em seguida, escolha **visão geral**.

   O portal do Azure mostra o histórico de execuções e o histórico de gatilhos para seu aplicativo lógico. Por exemplo:

   ![Histórico de execuções de aplicativo lógico e histórico de gatilho](media/logic-apps-monitor-your-logic-apps/overview.png)

   * O **histórico** de execuções mostra todas as execuções para seu aplicativo lógico. 
   * O **histórico de gatilho** mostra toda a atividade de gatilho para seu aplicativo lógico.

   Para obter descrições de status, consulte [solucionar problemas do seu aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Se você não encontrar os dados esperados, na barra de ferramentas, escolha **Atualizar**.

3. Para exibir as etapas de uma execução específica, em **histórico**de execuções, selecione essa execução. 

   A exibição monitor mostra cada etapa nessa execução. Por exemplo:

   ![Ações para uma execução específica](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Para obter mais detalhes sobre a execução, escolha **executar detalhes**. Essas informações resumem as etapas, o status, as entradas e as saídas da execução. 

   ![Escolha "detalhes da execução"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Por exemplo, você pode obter a ID de **correlação**da execução, que pode ser necessária ao usar a [API REST para aplicativos lógicos](https://docs.microsoft.com/rest/api/logic).

5. Para obter detalhes sobre uma etapa específica, escolha essa etapa. Agora você pode examinar detalhes como entradas, saídas e erros que ocorreram para essa etapa. Por exemplo:

   ![Detalhes da etapa](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Todos os detalhes e eventos de tempo de execução são criptografados no serviço de aplicativos lógicos. Eles são descriptografados somente quando um usuário solicita a exibição desses dados. Você também pode controlar o acesso a esses eventos com o [RBAC (controle de acesso baseado em função) do Azure](../role-based-access-control/overview.md).

6. Para obter detalhes sobre um evento de gatilho específico, volte para o painel **visão geral** . Em **histórico de gatilho**, selecione o evento de gatilho. Agora você pode examinar detalhes como entradas e saídas, por exemplo:

   ![Detalhes da saída do evento de gatilho](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Ativar o log de diagnóstico para seu aplicativo lógico

Para uma depuração mais rica com detalhes e eventos de tempo de execução, você pode configurar o log de diagnóstico com [logs de Azure monitor](../log-analytics/log-analytics-overview.md). Azure Monitor é um serviço no Azure que monitora seus ambientes locais e de nuvem para ajudá-lo a manter a disponibilidade e o desempenho. 

Antes de começar, você precisa ter um espaço de trabalho Log Analytics. Saiba [como criar um espaço de trabalho log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. Na [portal do Azure](https://portal.azure.com), localize e selecione seu aplicativo lógico. 

2. No menu folha do aplicativo lógico, em **monitoramento**, escolha **configurações de diagnóstico**de **diagnóstico** > .

   ![Vá para monitoramento, diagnóstico, configurações de diagnóstico](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. Em **configurações de diagnóstico**, escolha **ativado**.

   ![Ativar registos de diagnóstico](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Agora, selecione o espaço de trabalho Log Analytics e a categoria de evento para registro em log, conforme mostrado:

   1. Selecione **Enviar para log Analytics**. 
   2. Em **log Analytics**, escolha **Configurar**. 
   3. Em **espaços de trabalho do OMS**, selecione o espaço de trabalho a ser usado para registro em log.
      > [!NOTE]
      > As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.
   4. Em **log**, selecione a categoria **WorkflowRuntime** .
   5. Escolha o intervalo de métrica.
   6. Quando tiver terminado, escolha **Save** (Guardar).

   ![Selecione Log Analytics espaço de trabalho e dados para registro em log](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Agora, você pode encontrar eventos e outros dados para eventos de gatilho, eventos de execução e eventos de ação.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Localizar eventos e dados para seu aplicativo lógico

Para localizar e exibir eventos em seu aplicativo lógico, como eventos de gatilho, eventos de execução e eventos de ação, siga estas etapas.

1. Na [portal do Azure](https://portal.azure.com), escolha **todos os serviços**. Pesquise "log Analytics" e escolha **log Analytics** , conforme mostrado aqui:

   ![Escolha "Log Analytics"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. Em **log Analytics**, localize e selecione seu espaço de trabalho do log Analytics. 

   ![Selecione seu espaço de trabalho Log Analytics](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. Em **Gerenciamento**, escolha **pesquisa de logs**.

   ![Escolha "pesquisa de logs"](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. Na caixa de pesquisa, especifique um campo que você deseja localizar e pressione **Enter**. Quando você começar a digitar, verá possíveis correspondências e operações que podem ser usadas. 

   Por exemplo, para localizar os 10 principais eventos que ocorreram, insira e selecione esta consulta de pesquisa: **Pesquisar categoria = = "WorkflowRuntime" | limite 10**

   ![Inserir a cadeia de pesquisa](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Saiba mais sobre [como localizar dados em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md).

5. Na página resultados, na barra à esquerda, escolha o período de tempo que você deseja exibir.
Para refinar sua consulta adicionando um filtro, escolha **+ Adicionar**.

   ![Escolher período de tempo para resultados da consulta](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. Em **adicionar filtros**, insira o nome do filtro para que você possa encontrar o filtro desejado. Selecione o filtro e escolha **+ Adicionar**.

   Este exemplo usa a palavra "status" para encontrar eventos com falha em **AzureDiagnostics**.
   Aqui, o filtro para **status_s** já está selecionado.

   ![Selecionar filtro](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. Na barra à esquerda, selecione o valor de filtro que você deseja usar e escolha **aplicar**.

   ![Selecione o valor do filtro, escolha "aplicar"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Agora, retorne à consulta que você está criando. Sua consulta é atualizada com o filtro e o valor selecionados. Os resultados anteriores agora são filtrados também.

   ![Retornar à sua consulta com resultados filtrados](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Para salvar sua consulta para uso futuro, escolha **salvar**. Saiba [como salvar sua consulta](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Estenda como e onde você usa os dados de diagnóstico com outros serviços

Juntamente com os logs de Azure Monitor, você pode estender como usar os dados de diagnóstico do aplicativo lógico com outros serviços do Azure, por exemplo: 

* [Arquivar logs de Diagnóstico do Azure no armazenamento do Azure](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Transmitir logs de Diagnóstico do Azure para os hubs de eventos do Azure](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

Em seguida, você pode obter o monitoramento em tempo real usando a telemetria e a análise de outros serviços, como [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e [Power bi](../azure-monitor/platform/powerbi.md). Por exemplo:

* [Transmitir dados de hubs de eventos para Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analise dados de streaming com Stream Analytics e crie um painel de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Com base nas opções que você deseja configurar, certifique-se de primeiro [criar uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md) ou [criar um hub de eventos do Azure](../event-hubs/event-hubs-create.md). Em seguida, selecione as opções para onde deseja enviar os dados de diagnóstico:

![Enviar dados para a conta de armazenamento do Azure ou Hub de eventos](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Os períodos de retenção se aplicam somente quando você opta por usar uma conta de armazenamento.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Configurar alertas para seu aplicativo lógico

Para monitorar métricas específicas ou limites excedidos para seu aplicativo lógico, configure [alertas no Azure](../azure-monitor/platform/alerts-overview.md). Saiba mais sobre as [métricas no Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Para configurar alertas sem [logs de Azure monitor](../log-analytics/log-analytics-overview.md), siga estas etapas. Para critérios e ações de alertas mais avançados, [configure Azure monitor logs](#azure-diagnostics) também.

1. No menu folha do aplicativo lógico, em **monitoramento**, escolha **diagnóstico** > **regras** > de alerta**adicionar alerta** , conforme mostrado aqui:

   ![Adicionar um alerta para seu aplicativo lógico](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. Na folha **Adicionar uma regra de alerta** , crie seu alerta conforme mostrado:

   1. Em **recurso**, selecione seu aplicativo lógico, se ainda não estiver selecionado. 
   2. Forneça um nome e uma descrição para o alerta.
   3. Selecione uma **métrica** ou um evento que você deseja rastrear.
   4. Selecione uma **condição**, especifique um **limite** para a métrica e selecione o **período** para monitorar essa métrica.
   5. Selecione se deseja enviar email para o alerta. 
   6. Especifique qualquer outro endereço de email para enviar o alerta. 
   Você também pode especificar uma URL de webhook para a qual deseja enviar o alerta.

   Por exemplo, essa regra envia um alerta quando cinco ou mais execuções falham em uma hora:

   ![Criar regra de alerta de métrica](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Para executar um aplicativo lógico de um alerta, você pode incluir o [gatilho de solicitação](../connectors/connectors-native-reqres.md) em seu fluxo de trabalho, o que permite executar tarefas como estes exemplos:
> 
> * [Postar na margem de atraso](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Enviar um texto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Adicionar uma mensagem a uma fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Diagnóstico do Azure de configurações e detalhes do evento

Cada evento de diagnóstico tem detalhes sobre seu aplicativo lógico e esse evento, por exemplo, o status, a hora de início, a hora de término e assim por diante. Para configurar o monitoramento, o rastreamento e o log de forma programática, você pode usar esses detalhes com a [API REST para aplicativos lógicos do Azure](https://docs.microsoft.com/rest/api/logic) e a [api REST para diagnóstico do Azure](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows).

Por exemplo, o `ActionCompleted` evento tem as `clientTrackingId` propriedades `trackedProperties` e que você pode usar para rastreamento e monitoramento:

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

* `clientTrackingId`: Se não for fornecido, o Azure gera automaticamente essa ID e correlaciona eventos em uma execução de aplicativo lógico, incluindo quaisquer fluxos de trabalho aninhados que são chamados do aplicativo lógico. Você pode especificar manualmente essa ID de um gatilho passando um `x-ms-client-tracking-id` cabeçalho com seu valor de ID personalizado na solicitação do gatilho. Você pode usar um gatilho de solicitação, um gatilho HTTP ou um gatilho de webhook.

* `trackedProperties`: Para controlar entradas ou saídas em dados de diagnóstico, você pode adicionar propriedades rastreadas a ações na definição de JSON do aplicativo lógico. As propriedades rastreadas podem rastrear apenas entradas e saídas de uma única ação, mas você pode `correlation` usar as propriedades de eventos para correlacionar entre ações em uma execução.

  Para rastrear uma ou mais propriedades, adicione a `trackedProperties` seção e as propriedades que você deseja para a definição de ação. Por exemplo, suponha que você deseja controlar dados como uma "ID do pedido" em sua telemetria:

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
  Aqui está outro exemplo que usa a ação **inicializar variável** . O exemplo adiciona propriedades rastreadas da entrada da ação em que a entrada é uma matriz, não um registro.  

  ``` json
  "actions": { 
   "Initialize_variable": { 
      "inputs": { 
         "variables": [{ 
            "name": "ConnectorName", 
            "type": "String", 
            "value": "SFTP-SSH" 
         }]
      },
      "runAfter": {},
      "trackedProperties": { 
         "Track1": "@action().inputs.variables[0].value"
      },
      "type": "InitializeVariable"
   } 
  }
  ```

## <a name="next-steps"></a>Passos seguintes

* [Criar modelos para implantação de aplicativo lógico e gerenciamento de liberação](../logic-apps/logic-apps-create-deploy-template.md)
* [Cenários B2B com Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Monitorizar mensagens B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
