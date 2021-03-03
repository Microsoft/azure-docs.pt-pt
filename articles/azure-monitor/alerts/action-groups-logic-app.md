---
title: Como desencadear ações complexas com alertas do Azure Monitor
description: Saiba como criar uma ação lógica de aplicação para processar alertas do Azure Monitor.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 02/19/2021
ms.subservice: alerts
ms.openlocfilehash: adef1f729cbecd08b2cf99231423287bdc4c6ae0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701183"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Como desencadear ações complexas com alertas do Azure Monitor

Este artigo mostra-lhe como configurar e desencadear uma aplicação lógica para criar uma conversa nas Equipas da Microsoft quando um alerta dispara.

## <a name="overview"></a>Descrição Geral

Quando um alerta do Monitor Azure dispara, chama um [grupo de ação](./action-groups.md). Os grupos de ação permitem-lhe desencadear uma ou mais ações para notificar os outros sobre um alerta e também remediar o mesmo.

O processo geral é:

-   Crie a aplicação lógica para o respetivo tipo de alerta.

-   Importe uma carga útil da amostra para o respetivo tipo de alerta na aplicação lógica.

-   Defina o comportamento da aplicação lógica.

-   Copie o ponto final HTTP da aplicação lógica num grupo de ação Azure.

O processo é semelhante se quiser que a aplicação lógica realize uma ação diferente.

## <a name="create-an-activity-log-alert-administrative"></a>Criar um alerta de registo de atividade: Administrativo

1. [Criar uma app lógica](~/articles/logic-apps/quickstart-create-first-logic-app-workflow.md)

2.  Selecione o gatilho: **Quando um pedido HTTP for recebido**.

1. No diálogo para **quando for recebido um pedido HTTP**, selecione Utilize a carga útil da amostra para gerar **esquema**.

    ![Screenshot que mostra a caixa de diálogo de pedido de H T T P e a carga útil da amostra de utilização para gerar esquema opion selecionado. ](~/articles/app-service/media/tutorial-send-email/generate-schema-with-payload.png)

3.  Copiar e colar a seguinte carga útil da amostra na caixa de diálogo:

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

9. O **Logic App Designer** exibe uma janela pop-up para lembrá-lo que o pedido enviado para a aplicação lógica deve definir o cabeçalho do Tipo de **Conteúdo** para **aplicação/json**. Feche a janela pop-up. O alerta do Monitor Azure define o cabeçalho.

    ![Desente o cabeçalho do tipo de conteúdo](media/action-groups-logic-app/content-type-header.png "Desente o cabeçalho do tipo de conteúdo")

10. Selecione **+** **Novo passo** e, em seguida, escolha **Adicionar uma ação**.

    ![Adicionar uma ação](media/action-groups-logic-app/add-action.png "Adicionar uma ação")

11. Procure e selecione o conector Microsoft Teams. Escolha as Equipas microsoft **- ação de mensagem** postada.

    ![Ações da Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "Ações da Microsoft Teams")

12. Configure a ação do Microsoft Teams. O **Logic Apps Designer** pede-lhe para autenticar no seu trabalho ou na sua conta escolar. Escolha o **ID da equipa** e **o ID do Canal** para enviar a mensagem.

13. Configure a mensagem utilizando uma combinação de texto estático e referências ao \<fields\> conteúdo dinâmico. Copiar e colar o seguinte texto no campo **Mensagem:**

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Em seguida, procure e substitua as \<fields\> etiquetas de conteúdo dinâmicas com o mesmo nome.

    > [!NOTE]
    > Existem dois campos dinâmicos que são nomeados **estatuto.** Adicione ambos os campos à mensagem. Utilize o campo que está no saco de propriedades **activityLog** e elimine o outro campo. Passe o cursor sobre o campo **de estado** para ver a referência de campo totalmente qualificada, como mostra a seguinte imagem:

    ![Ação microsoft Teams: Publicar uma mensagem](media/action-groups-logic-app/teams-action-post-message.png "Ação microsoft Teams: Publicar uma mensagem")

14. No topo do **Logic Apps Designer,** selecione **Save** para guardar a sua aplicação lógica.

15. Abra o seu grupo de ação existente e adicione uma ação para fazer referência à aplicação lógica. Se não tiver um grupo de ação existente, consulte [Criar e gerir grupos de ação no portal Azure](./action-groups.md) para criar um. Não se esqueça de guardar as suas mudanças.

    ![Atualizar o grupo de ação](media/action-groups-logic-app/update-action-group.png "Atualizar o grupo de ação")

Da próxima vez que um alerta chamar o seu grupo de ação, a sua aplicação lógica é chamada.

## <a name="create-a-service-health-alert"></a>Criar um alerta de saúde de serviço

As entradas no Serviço Azure são parte do registo de atividades. O processo de criação do alerta é semelhante à criação de [um alerta de registo de atividade,](#create-an-activity-log-alert-administrative)mas com algumas alterações:

- Os passos 1 a 3 são os mesmos.
- Para o passo 4, utilize a seguinte carga útil da amostra para o gatilho do pedido HTTP:

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
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{\"ImpactedRegions\"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Os passos 5 e 6 são os mesmos.
-  Para os passos 7 a 11, utilize o seguinte processo:

   1. Selecione **+** **Novo passo** e, em seguida, escolha **Adicionar uma condição**. Desaprote as seguintes condições para que a aplicação lógica execute apenas quando os dados de entrada correspondem aos valores abaixo.  Ao introduzir o valor da versão na caixa de texto, coloque aspas à sua volta ("0.1.1") para se certificar de que é avaliada como uma corda e não como um tipo numérico.  O sistema não mostra as cotações se voltar à página, mas o código subjacente ainda mantém o tipo de corda.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Condição de carga útil do serviço de saúde"](media/action-groups-logic-app/service-health-payload-condition.png "Condição de carga útil de serviço saúde")

   1. Na **condição se for verdadeira,** siga as instruções nos passos 11 a 13 em [Criar um alerta de registo de atividade](#create-an-activity-log-alert-administrative) para adicionar a ação do Microsoft Teams.

   1. Defina a mensagem utilizando uma combinação de HTML e conteúdo dinâmico. Copie e cole o seguinte conteúdo no campo **Mensagem.** Substitua os `[incidentType]` `[trackingID]` `[title]` campos, e `[communication]` campos por etiquetas de conteúdo dinâmicas com o mesmo nome:

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

       !["Serviço Saúde verdadeira condição pós ação"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Serviço Saúde verdadeira condição pós ação")

   1. Para a condição **de se for falsa,** forneça uma mensagem útil:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Serviço de saúde condição falsa pós ação"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Serviço Saúde falsa condição pós ação")

- O passo 15 é o mesmo. Siga as instruções para guardar a sua aplicação lógica e atualizar o seu grupo de ação.

## <a name="create-a-metric-alert"></a>Criar um alerta métrico

O processo de criação de um alerta métrico é semelhante à criação de [um alerta de registo de atividade,](#create-an-activity-log-alert-administrative)mas com algumas alterações:

- Os passos 1 a 3 são os mesmos.
- Para o passo 4, utilize a seguinte carga útil da amostra para o gatilho do pedido HTTP:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
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
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- Os passos 5 e 6 são os mesmos.
- Para os passos 7 a 11, utilize o seguinte processo:

  1. Selecione **+** **Novo passo** e, em seguida, escolha **Adicionar uma condição**. Desaprote as seguintes condições para que a aplicação lógica execute apenas quando os dados de entrada correspondem a estes valores abaixo. Ao introduzir o valor da versão na caixa de texto, coloque aspas à sua volta ("2.0") para garantir que é avaliada como uma corda e não como um tipo numérico.  O sistema não mostra as cotações se voltar à página, mas o código subjacente ainda mantém o tipo de corda. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Condição de carga útil de alerta métrico"](media/action-groups-logic-app/metric-alert-payload-condition.png "Condição de carga útil de alerta métrico")

  1. Na **condição se for verdadeira,** adicione um **Para cada** loop e a ação do Microsoft Teams. Defina a mensagem utilizando uma combinação de HTML e conteúdo dinâmico.

      !["Alerta métrico estado real após ação"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Alerta métrico real condição pós ação")

  1. Na condição **de se estar em falso,** defina uma ação da Microsoft Teams para comunicar que o alerta métrico não corresponde às expectativas da aplicação lógica. Inclua a carga útil JSON. Note como fazer referência ao `triggerBody` conteúdo dinâmico na `json()` expressão.

      !["Alerta métrico estado falso após ação"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Alerta métrico falso condição pós ação")

- O passo 15 é o mesmo. Siga as instruções para guardar a sua aplicação lógica e atualizar o seu grupo de ação.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Chamando outras aplicações além de Microsoft Teams
A Logic Apps tem uma série de conectores diferentes que permitem desencadear ações numa vasta gama de aplicações e bases de dados. Slack, SQL Server, Oracle, Salesforce, são apenas alguns exemplos. Para obter mais informações sobre conectores, consulte [os conectores Logic App](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Passos seguintes
* Obtenha uma [visão geral dos alertas de registo de atividades do Azure](./alerts-overview.md) e aprenda a receber alertas.  
* Saiba como [configurar alertas quando for publicada uma notificação de Saúde do Serviço Azure](../../service-health/alerts-activity-log-service-notifications-portal.md).
* Saiba mais sobre [grupos de ação.](./action-groups.md)