---
title: Como realizar ações complexos com alertas do Azure Monitor
description: Saiba como criar uma ação de aplicação lógica para processar os alertas do Azure Monitor.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: e69158a6ee4d8415f52cf458c028cab56f481d8b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121133"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Como realizar ações complexos com alertas do Azure Monitor

Este artigo mostra-lhe como configurar e acionar uma aplicação lógica para criar uma conversa no Microsoft Teams quando um alerta é acionado.

## <a name="overview"></a>Descrição geral
Quando aciona um alerta de Monitor do Azure, ele chama um [grupo de ação](../../azure-monitor/platform/action-groups.md). Grupos de ação permitem-lhe acionar uma ou mais ações para notificar os utilizadores sobre um alerta e também resolvê-lo.

O processo geral é:

-   Crie a aplicação de lógica para o respetivo tipo de alerta.

-   Importe o esquema para o respetivo tipo de alerta para a aplicação lógica.

-   Defina o comportamento de aplicação lógica.

-   Copie o ponto final de HTTP da aplicação lógica para um grupo de ação do Azure.

O processo é semelhante, se pretender que a aplicação lógica para executar uma ação diferente.

## <a name="create-an-activity-log-alert-administrative"></a>Crie um alerta de registo de atividade: Administrativa

1.  No portal do Azure, selecione **criar um recurso** no canto superior esquerdo.

2.  Procure e selecione **aplicação lógica**, em seguida, selecione **criar**.

3.  Dê à sua aplicação lógica uma **Name**, escolha um **grupo de recursos**e assim por diante.

    ![Criar uma aplicação lógica](media/action-groups-logic-app/create-logic-app-dialog.png "criar uma aplicação lógica")

4.  Selecione **criar** para criar a aplicação lógica. Uma mensagem de pop-up indica que a aplicação lógica é criada. Selecione **recursos inicie** para abrir o **estruturador de aplicações lógicas**.

5.  Selecione o acionador: **Quando é recebido um pedido HTTP**.

    ![Acionadores da aplicação lógica](media/action-groups-logic-app/logic-app-triggers.png "acionadores da aplicação lógica")

6.  Selecione **editar** para alterar o acionador de pedido HTTP.

    ![Acionadores de pedido HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "acionadores de pedido HTTP")

7.  Selecione **Utilizar o payload de exemplo para gerar esquema**.

    ![Utilizar um payload de exemplo](media/action-groups-logic-app/use-sample-payload-button.png "utilizar um payload de exemplo")

8.  Copie e cole o esquema de exemplo seguinte na caixa de diálogo:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. O **Estruturador da aplicação lógica** exibe uma janela de pop-up para lembrá-lo de que o pedido enviado para a aplicação lógica tem de definir o **Content-Type** cabeçalho para **application/json**. Feche a janela de pop-up. O alerta de Azure Monitor define o cabeçalho.

    ![Definir o cabeçalho Content-Type](media/action-groups-logic-app/content-type-header.png "definir o cabeçalho Content-Type")

10. Selecione **+** **novo passo** e, em seguida, escolha **adicionar uma ação**.

    ![Adicionar uma ação](media/action-groups-logic-app/add-action.png "adicionar uma ação")

11. Procure e seleccione o conector do Microsoft Teams. Escolha o **Microsoft Teams – publicar mensagem** ação.

    ![Ações do Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "ações do Microsoft Teams")

12. Configure a ação do Microsoft Teams. O **estruturador de aplicações lógicas** pede-lhe para autenticar a sua conta Office 365. Escolha o **ID da equipa** e **ID do canal** para enviar a mensagem.

13. Configurar a mensagem com uma combinação de texto estático e referências para o \<campos\> no conteúdo dinâmico. Copie e cole o seguinte texto para o **mensagem** campo:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Em seguida, procure e substitua a \<campos\> com etiquetas conteúdas dinâmicas, o mesmo nome.

    > [!NOTE]
    > Existem dois campos dinâmicos que são nomeados **estado**. Adicione ambos estes campos à mensagem. Utilize o campo que está a **activityLog** recipiente de propriedades e elimine o outro campo. Coloque o cursor sobre o **estado** campo para ver a referência de campo completamente qualificado, conforme mostrado na captura de ecrã seguinte:

    ![Ação do Microsoft Teams: Publicar uma mensagem](media/action-groups-logic-app/teams-action-post-message.png "ação do Microsoft Teams: Publicar uma mensagem")

14. Na parte superior a **estruturador de aplicações lógicas**, selecione **guardar** para guardar a aplicação lógica.

15. Abra o seu grupo de ação existente e adicionar uma ação para referenciar a aplicação lógica. Se não tiver um grupo de ação existente, consulte [criar e gerir grupos de ação no portal do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) para criar um. Não se esqueça de guardar as alterações.

    ![Atualizar o grupo de ação](media/action-groups-logic-app/update-action-group.png "atualizar o grupo de ação")

Da próxima vez que um alerta chama seu grupo de ação, a sua aplicação lógica é chamada.

## <a name="create-a-service-health-alert"></a>Criar um alerta de estado de funcionamento do serviço

Entradas de estado de funcionamento do serviço do Azure fazem parte do registo de atividades. O processo de criação do alerta é semelhante à [criar um alerta de registo de atividade](#create-an-activity-log-alert-administrative), mas com algumas alterações:

- Passos 1 a 7 são os mesmos.
- Para o passo 8, utilize o esquema de exemplo seguintes para o acionador de pedido HTTP:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  As etapas 9 e 10 são os mesmos.
-  Para obter os passos 11 por meio de 14, utilize o seguinte processo:

   1. Selecione **+** **novo passo** e, em seguida, escolha **adicionar uma condição**. Defina as condições a seguir para que a aplicação lógica é executado apenas quando os dados de entrada corresponda os valores abaixo.  Ao introduzir o valor de versão na caixa de texto, coloca entre aspas ("0.1.1")-lo para se certificar de que é avaliada como uma cadeia de caracteres e não um tipo numérico.  O sistema não mostra as aspas se retornar à página, mas o código subjacente ainda mantém o tipo de cadeia de caracteres.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Condição de payload de estado de funcionamento do serviço"](media/action-groups-logic-app/service-health-payload-condition.png "condição de payload de estado de funcionamento do serviço")

   1. Na **se for true** condição, siga as instruções nos passos 11 por meio de 13 na [criar um alerta de registo de atividade](#create-an-activity-log-alert-administrative) para adicionar a ação do Microsoft Teams.

   1. Defina a mensagem com uma combinação de HTML e de conteúdo dinâmico. Copie e cole o seguinte conteúdo para o **mensagem** campo. Substitua a `[incidentType]`, `[trackingID]`, `[title]`, e `[communication]` campos com as etiquetas dinâmicas de conteúdo do mesmo nome:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Ação de postagem de verdadeira condição de estado de funcionamento do serviço"](media/action-groups-logic-app/service-health-true-condition-post-action.png "ação de estado de funcionamento do serviço de publicação de condição verdadeira")

   1. Para o **se for FALSO** de condição, fornecer uma mensagem útil:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Ação de publicação do Estado de funcionamento do serviço condição falso"](media/action-groups-logic-app/service-health-false-condition-post-action.png "ação de estado de funcionamento do serviço de publicação de condição falsa")

- Passo 15 é o mesmo. Siga as instruções para guardar a aplicação lógica e atualizar o seu grupo de ação.

## <a name="create-a-metric-alert"></a>Criar um alerta de métrica

O processo para criar um alerta de métrica é semelhante à [criar um alerta de registo de atividade](#create-an-activity-log-alert-administrative), mas com algumas alterações:

- Passos 1 a 7 são os mesmos.
- Para o passo 8, utilize o esquema de exemplo seguintes para o acionador de pedido HTTP:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

- As etapas 9 e 10 são os mesmos.
- Para obter os passos 11 por meio de 14, utilize o seguinte processo:

  1. Selecione **+** **novo passo** e, em seguida, escolha **adicionar uma condição**. Defina as condições a seguir para que a aplicação lógica é executado apenas quando os dados de entrada corresponde a estes valores abaixo. Quando introduzir o valor de versão na caixa de texto, colocá-lo ("2.0") entre aspas para certifica-se de que é avaliada como uma cadeia de caracteres e não um tipo numérico.  O sistema não mostra as aspas se retornar à página, mas o código subjacente ainda mantém o tipo de cadeia de caracteres. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Condição de payload de alerta de métrica"](media/action-groups-logic-app/metric-alert-payload-condition.png "condição de payload de alerta de métrica")

  1. Na **se for true** condição, adicione um **para cada** loop e a ação do Microsoft Teams. Defina a mensagem com uma combinação de HTML e de conteúdo dinâmico.

      !["Ação de postagem de métrica condição verdadeiro do alerta"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "ação de postagem de condições verdadeiro alerta de métricas")

  1. Na **se for FALSO** de condição, defina uma ação do Microsoft Teams para comunicar que o alerta de métrica não corresponde às expectativas da aplicação lógica. Inclua JSON payload. Observe como fazer referência a `triggerBody` conteúdo dinâmico no `json()` expressão.

      !["Ação de postagem de condições de Falso alerta métricas"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "ação de postagem de condições falso alerta de métricas")

- Passo 15 é o mesmo. Siga as instruções para guardar a aplicação lógica e atualizar o seu grupo de ação.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Chamar outros aplicativos além do Microsoft Teams
O Logic Apps tem uma série de conectores diferentes que permitem-lhe realizar ações numa ampla variedade de aplicativos e bancos de dados. Slack, SQL Server, Oracle, Salesforce, são apenas alguns exemplos. Para obter mais informações sobre conectores, consulte [conectores da aplicação lógica](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Passos Seguintes
* Obter um [descrição geral dos alertas de registo de atividades do Azure](../../azure-monitor/platform/alerts-overview.md) e saiba como receber alertas.  
* Saiba como [configurar alertas quando for lançada uma notificação do Azure Service Health](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
* Saiba mais sobre [grupos de ação](../../azure-monitor/platform/action-groups.md).

