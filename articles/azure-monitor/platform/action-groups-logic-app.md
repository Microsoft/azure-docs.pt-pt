---
title: Como desencadear ações complexas com alertas do Monitor Azure
description: Aprenda a criar uma ação lógica de aplicações para processar alertas do Monitor Azure.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 07/18/2018
ms.subservice: alerts
ms.openlocfilehash: 655a3acc44a1418778b37fbef85e5df75d042317
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78206241"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Como desencadear ações complexas com alertas do Monitor Azure

Este artigo mostra-lhe como configurar e desencadear uma aplicação lógica para criar uma conversa em Microsoft Teams quando um alerta dispara.

## <a name="overview"></a>Descrição geral

Quando um alerta de Monitor Azure dispara, chama um grupo de [ação](../../azure-monitor/platform/action-groups.md). Os grupos de ação permitem-lhe desencadear uma ou mais ações para notificar os outros sobre um alerta e também remedia-lo.

O processo geral é:

-   Crie a aplicação lógica para o respetivo tipo de alerta.

-   Importar uma carga útil da amostra para o respetivo tipo de alerta na aplicação lógica.

-   Defina o comportamento da aplicação lógica.

-   Copie o ponto final http da aplicação lógica num grupo de ação Azure.

O processo é semelhante se quiser que a aplicação lógica realize uma ação diferente.

## <a name="create-an-activity-log-alert-administrative"></a>Criar um alerta de registo de atividade: Administrativo

1.  No portal Azure, selecione **Criar um recurso** no canto superior esquerdo.

2.  Procure e selecione **Logic App,** em seguida, selecione **Criar**.

3.  Dê um **nome**à sua aplicação lógica, escolha um **grupo de Recursos,** e assim por diante.

    ![Criar uma aplicação lógica](media/action-groups-logic-app/create-logic-app-dialog.png "Criar uma aplicação lógica")

4.  Selecione **Criar** para criar a aplicação lógica. Uma mensagem pop-up indica que a aplicação lógica é criada. Selecione **Recurso de Lançamento** para abrir o Designer de **Aplicações Lógicas**.

5.  Selecione o gatilho: **Quando um pedido HTTP for recebido**.

    ![Acionadores da aplicação lógica](media/action-groups-logic-app/logic-app-triggers.png "Acionadores da aplicação lógica")

6.  Selecione **Editar** para alterar o gatilho de pedido HTTP.

    ![GATILHOS de pedido http](media/action-groups-logic-app/http-request-trigger-shape.png "GATILHOS de pedido http")

7.  Selecione **Utilizar o payload de exemplo para gerar esquema**.

    ![Utilize uma carga útil da amostra](media/action-groups-logic-app/use-sample-payload-button.png "Utilize uma carga útil da amostra")

8.  Copiar e colar a seguinte carga útil da amostra na caixa de diálogo:

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

9. O **Logic App Designer** exibe uma janela pop-up para lembrá-lo que o pedido enviado para a aplicação lógica deve definir o cabeçalho tipo **conteúdo** para **aplicação/json**. Feche a janela pop-up. O alerta Do Monitor Azure define o cabeçalho.

    ![Definir o cabeçalho do tipo conteúdo](media/action-groups-logic-app/content-type-header.png "Definir o cabeçalho do tipo conteúdo")

10. Selecione **+** **novo passo** e, em seguida, escolha Adicionar uma **ação**.

    ![Adicionar uma ação](media/action-groups-logic-app/add-action.png "Adicionar uma ação")

11. Procure e selecione o conector Microsoft Teams. Escolha as **Equipas microsoft - post message** action.

    ![Ações das Equipas microsoft](media/action-groups-logic-app/microsoft-teams-actions.png "Ações das Equipas microsoft")

12. Configure a ação das Equipas microsoft. O Designer de **Aplicações Lógicas** pede-lhe para autenticar a sua conta Office 365. Escolha o ID da **equipa** e o ID do **canal** para enviar a mensagem para.

13. Configure a mensagem utilizando uma combinação de \<texto\> estático e referências aos campos no conteúdo dinâmico. Copiar e colar o seguinte texto no campo **Mensagem:**

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Em seguida, procure \<\> e substitua os campos por etiquetas de conteúdo dinâmicas com o mesmo nome.

    > [!NOTE]
    > Há dois campos dinâmicos que são nomeados **estatuto.** Adicione estes dois campos à mensagem. Use o campo que está no saco de propriedade **activityLog** e elimine o outro campo. Passe o cursor sobre o campo de **estado** para ver a referência de campo totalmente qualificada, como mostra a seguinte imagem:

    ![Ação das Equipas microsoft: Postar uma mensagem](media/action-groups-logic-app/teams-action-post-message.png "Ação das Equipas microsoft: Postar uma mensagem")

14. No topo do **Logic Apps Designer**, selecione **Save** para salvar a sua aplicação lógica.

15. Abra o seu grupo de ação existente e adicione uma ação para referenciar a aplicação lógica. Se não tiver um grupo de ação existente, consulte [Create e gereos grupos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) de ação no portal Azure para criar um. Não se esqueça de guardar as suas mudanças.

    ![Atualizar o grupo de ação](media/action-groups-logic-app/update-action-group.png "Atualizar o grupo de ação")

Da próxima vez que um alerta chamar o seu grupo de ação, a sua aplicação lógica é chamada.

## <a name="create-a-service-health-alert"></a>Criar um alerta de saúde de serviço

As entradas de Saúde do Serviço Azure fazem parte do registo de atividade. O processo de criação do alerta é semelhante à criação de um alerta de registo de [atividade,](#create-an-activity-log-alert-administrative)mas com algumas alterações:

- Os passos 1 a 7 são os mesmos.
- Para o passo 8, utilize a seguinte carga útil da amostra para o gatilho de pedido HTTP:

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
                        "impactedServices": "[{"ImpactedRegions"}]",
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

-  Os passos 9 e 10 são os mesmos.
-  Para os passos 11 a 14, utilize o seguinte processo:

   1. Selecione **+** **novo passo** e, em seguida, escolha Adicionar uma **condição**. Defina as seguintes condições para que a aplicação lógica execute apenas quando os dados de entrada correspondam aos valores abaixo.  Ao introduzir o valor da versão na caixa de texto, coloque as pascantes à sua volta ("0.1.1") para se certificar de que é avaliada como uma corda e não como um tipo numérico.  O sistema não mostra as cotações se voltar à página, mas o código subjacente mantém o tipo de corda.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Serviço De saúde condição de carga útil"](media/action-groups-logic-app/service-health-payload-condition.png "Serviço Condição de carga útil para a saúde")

   1. Em condições **verdadeiras,** siga as instruções nos passos 11 a 13 em [Criar um alerta](#create-an-activity-log-alert-administrative) de registo de atividade para adicionar a ação das Equipas Microsoft.

   1. Defina a mensagem utilizando uma combinação de HTML e conteúdo dinâmico. Copiar e colar o seguinte conteúdo no campo **Mensagem.** Substitua `[incidentType]` `[trackingID]`os `[title]`campos `[communication]` e os campos por etiquetas de conteúdo dinâmicas com o mesmo nome:

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

   1. Para a condição **falsa,** forneça uma mensagem útil:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Serviço Saúde falsa condição pós ação"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Serviço Saúde falsa condição pós ação")

- Passo 15 é o mesmo. Siga as instruções para salvar a sua aplicação lógica e atualize o seu grupo de ação.

## <a name="create-a-metric-alert"></a>Criar um alerta métrico

O processo de criação de um alerta métrico é semelhante à criação de um alerta de registo de [atividade,](#create-an-activity-log-alert-administrative)mas com algumas alterações:

- Os passos 1 a 7 são os mesmos.
- Para o passo 8, utilize a seguinte carga útil da amostra para o gatilho de pedido HTTP:

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

- Os passos 9 e 10 são os mesmos.
- Para os passos 11 a 14, utilize o seguinte processo:

  1. Selecione **+** **novo passo** e, em seguida, escolha Adicionar uma **condição**. Defina as seguintes condições para que a aplicação lógica execute apenas quando os dados de entrada correspondem a estes valores abaixo. Ao introduzir o valor da versão na caixa de texto, coloque as pascitões à sua volta ("2.0") para se certificar de que é avaliada como uma corda e não como um tipo numérico.  O sistema não mostra as cotações se voltar à página, mas o código subjacente mantém o tipo de corda. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Condição de carga de alerta métrico"](media/action-groups-logic-app/metric-alert-payload-condition.png "Condição de carga de alerta métrico")

  1. Em condições **verdadeiras,** adicione um **Para cada** loop e a ação das Equipas Microsoft. Defina a mensagem utilizando uma combinação de HTML e conteúdo dinâmico.

      !["Alerta métrico situação real pós ação"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Alerta métrico verdadeira condição pós ação")

  1. Na condição **falsa,** defina uma ação do Microsoft Teams para comunicar que o alerta métrico não corresponde às expectativas da aplicação lógica. Inclua a carga útil da JSON. Note como `triggerBody` referenciar o `json()` conteúdo dinâmico na expressão.

      !["Alerta métrico de falsas condições pós ação"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Alerta métrico de falsacondição pós ação")

- Passo 15 é o mesmo. Siga as instruções para salvar a sua aplicação lógica e atualize o seu grupo de ação.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Chamar outras aplicações para além das Equipas microsoft
As Aplicações Lógicas têm uma série de conectores diferentes que permitem desencadear ações numa vasta gama de aplicações e bases de dados. Slack, SQL Server, Oracle, Salesforce, são apenas alguns exemplos. Para obter mais informações sobre conectores, consulte [conectores de Aplicações Lógicas](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Passos seguintes
* Obtenha uma [visão geral dos alertas de registo de atividade](../../azure-monitor/platform/alerts-overview.md) do Azure e aprenda a receber alertas.  
* Saiba [configurar alertas quando é publicada uma notificação de Saúde de Serviço Azure](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
* Saiba mais sobre [grupos de ação.](../../azure-monitor/platform/action-groups.md)

