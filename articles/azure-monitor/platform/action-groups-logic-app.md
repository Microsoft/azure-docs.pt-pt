---
title: Como disparar ações complexas com alertas de Azure Monitor
description: Saiba como criar uma ação de aplicativo lógico para processar Azure Monitor alertas.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: ad196f52935af4ab4aa7af1b80183161065d4b4b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365027"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Como disparar ações complexas com alertas de Azure Monitor

Este artigo mostra como configurar e disparar um aplicativo lógico para criar uma conversa no Microsoft Teams quando um alerta é disparado.

## <a name="overview"></a>Visão geral
Quando um alerta Azure Monitor é disparado, ele chama um [grupo de ações](../../azure-monitor/platform/action-groups.md). Os grupos de ação permitem disparar uma ou mais ações para notificar outras pessoas sobre um alerta e também corrigi-lo.

O processo geral é:

-   Crie o aplicativo lógico para o respectivo tipo de alerta.

-   Importe um conteúdo de exemplo para o respectivo tipo de alerta para o aplicativo lógico.

-   Defina o comportamento do aplicativo lógico.

-   Copie o ponto de extremidade HTTP do aplicativo lógico em um grupo de ações do Azure.

O processo é semelhante se você quiser que o aplicativo lógico execute uma ação diferente.

## <a name="create-an-activity-log-alert-administrative"></a>Criar um alerta do log de atividades: administrativo

1.  Na portal do Azure, selecione **criar um recurso** no canto superior esquerdo.

2.  Procure e selecione **aplicativo lógico**e, em seguida, selecione **criar**.

3.  Dê um **nome**ao seu aplicativo lógico, escolha um **grupo de recursos**e assim por diante.

    ![Criar uma aplicação lógica](media/action-groups-logic-app/create-logic-app-dialog.png "Criar uma aplicação lógica")

4.  Selecione **criar** para criar o aplicativo lógico. Uma mensagem pop-up indica que o aplicativo lógico é criado. Selecione **Iniciar recurso** para abrir o **Designer de aplicativos lógicos**.

5.  Selecione o gatilho: **quando uma solicitação HTTP é recebida**.

    ![Gatilhos de aplicativo lógico](media/action-groups-logic-app/logic-app-triggers.png "Acionadores da aplicação lógica")

6.  Selecione **Editar** para alterar o gatilho de solicitação HTTP.

    ![Gatilhos de solicitação HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Gatilhos de solicitação HTTP")

7.  Selecione **Utilizar o payload de exemplo para gerar esquema**.

    ![Usar um conteúdo de exemplo](media/action-groups-logic-app/use-sample-payload-button.png "Usar um conteúdo de exemplo")

8.  Copie e cole o seguinte conteúdo de exemplo na caixa de diálogo:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/�"
                    },
                    "channels": "Operation",
                    "claims": "�",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/�",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "�",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. O **designer do aplicativo lógico** exibe uma janela pop-up para lembrá-lo de que a solicitação enviada ao aplicativo lógico deve definir o cabeçalho **Content-Type** como **Application/JSON**. Feche a janela pop-up. O alerta de Azure Monitor define o cabeçalho.

    ![Definir o cabeçalho Content-Type](media/action-groups-logic-app/content-type-header.png "Definir o cabeçalho Content-Type")

10. Selecione **+** **nova etapa** e, em seguida, escolha **Adicionar uma ação**.

    ![Adicionar uma ação](media/action-groups-logic-app/add-action.png "Adicionar uma ação")

11. Procure e selecione o conector do Microsoft Teams. Escolha a ação **postar mensagem da Microsoft Teams** .

    ![Ações do Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "Ações do Microsoft Teams")

12. Configure a ação do Microsoft Teams. O **Designer de aplicativos lógicos** solicita que você se autentique na sua conta do Office 365. Escolha a **ID da equipe** e a **ID do canal** para o qual enviar a mensagem.

13. Configure a mensagem usando uma combinação de texto estático e referências aos campos de \<\> no conteúdo dinâmico. Copie e cole o texto a seguir no campo de **mensagem** :

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Em seguida, pesquise e substitua os campos de \<\> com as marcas de conteúdo dinâmico do mesmo nome.

    > [!NOTE]
    > Há dois campos dinâmicos chamados **status**. Adicione ambos os campos à mensagem. Use o campo que está no recipiente da propriedade **activityLog** e exclua o outro campo. Focalize o cursor sobre o campo **status** para ver a referência de campo totalmente qualificada, conforme mostrado na seguinte captura de tela:

    ![Ação do Microsoft Teams: postar uma mensagem](media/action-groups-logic-app/teams-action-post-message.png "Ação do Microsoft Teams: postar uma mensagem")

14. Na parte superior do **Designer de aplicativos lógicos**, selecione **salvar** para salvar seu aplicativo lógico.

15. Abra o grupo de ação existente e adicione uma ação para fazer referência ao aplicativo lógico. Se você não tiver um grupo de ação existente, consulte [criar e gerenciar grupos de ações no portal do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) para criar um. Don t esquecer de salvar suas alterações.

    ![Atualizar o grupo de ações](media/action-groups-logic-app/update-action-group.png "Atualizar o grupo de ações")

Na próxima vez que um alerta chamar o grupo de ações, seu aplicativo lógico será chamado.

## <a name="create-a-service-health-alert"></a>Criar um alerta de integridade do serviço

As entradas de integridade do serviço do Azure fazem parte do log de atividades. O processo de criação do alerta é semelhante à [criação de um alerta do log de atividades](#create-an-activity-log-alert-administrative), mas com algumas alterações:

- As etapas de 1 a 7 são as mesmas.
- Para a etapa 8, use o seguinte conteúdo de exemplo para o gatilho de solicitação HTTP:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "�",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "�",
                        "service": "�",
                        "region": "Global",
                        "communication": "�",
                        "incidentType": "Incident",
                        "trackingId": "�",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "�",
                        "defaultLanguageContent": "�",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "�",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  As etapas 9 e 10 são as mesmas.
-  Para as etapas 11 a 14, use o seguinte processo:

   1. Selecione **+** **nova etapa** e, em seguida, escolha **Adicionar uma condição**. Defina as seguintes condições para que o aplicativo lógico seja executado somente quando os dados de entrada corresponderem aos valores abaixo.  Ao inserir o valor da versão na caixa de texto, coloque aspas sobre ela ("0.1.1") para garantir que ela seja avaliada como uma cadeia de caracteres e não um tipo numérico.  O sistema não mostrará as aspas se você retornar para a página, mas o código subjacente ainda mantiver o tipo de cadeia de caracteres.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Condição de carga de integridade do serviço"](media/action-groups-logic-app/service-health-payload-condition.png "Condição de carga de integridade do serviço")

   1. Na condição **se verdadeiro** , siga as instruções nas etapas 11 a 13 em [criar um alerta do log de atividades](#create-an-activity-log-alert-administrative) para adicionar a ação do Microsoft Teams.

   1. Defina a mensagem usando uma combinação de HTML e conteúdo dinâmico. Copie e cole o conteúdo a seguir no campo de **mensagem** . Substitua os campos `[incidentType]`, `[trackingID]`, `[title]`e `[communication]` por marcas de conteúdo dinâmico do mesmo nome:

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

       !["Ação post da condição verdadeira de integridade do serviço"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Ação de postagem da condição verdadeira de integridade do serviço")

   1. Para a condição **If false** , forneça uma mensagem útil:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Ação post da condição do Service Health false"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Ação de postagem condição do serviço de integridade falsa")

- A etapa 15 é a mesma. Siga as instruções para salvar seu aplicativo lógico e atualizar seu grupo de ação.

## <a name="create-a-metric-alert"></a>Criar um alerta de métrica

O processo de criação de um alerta de métrica é semelhante à [criação de um alerta do log de atividades](#create-an-activity-log-alert-administrative), mas com algumas alterações:

- As etapas de 1 a 7 são as mesmas.
- Para a etapa 8, use o seguinte conteúdo de exemplo para o gatilho de solicitação HTTP:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "�",
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
        "subscriptionId": "�",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "�",
        "portalLink": "�"
        },
        "properties": {}
    }
    }
    ```

- As etapas 9 e 10 são as mesmas.
- Para as etapas 11 a 14, use o seguinte processo:

  1. Selecione **+** **nova etapa** e, em seguida, escolha **Adicionar uma condição**. Defina as seguintes condições para que o aplicativo lógico seja executado somente quando os dados de entrada corresponderem a esses valores abaixo. Ao inserir o valor da versão na caixa de texto, coloque aspas em volta dele ("2,0") para garantir que ele seja avaliado como uma cadeia de caracteres e não um tipo numérico.  O sistema não mostrará as aspas se você retornar para a página, mas o código subjacente ainda mantiver o tipo de cadeia de caracteres. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Condição da carga do alerta de métrica"](media/action-groups-logic-app/metric-alert-payload-condition.png "Condição de carga de alerta de métrica")

  1. Na condição **se verdadeiro** , adicione um loop **for each** e a ação Microsoft Teams. Defina a mensagem usando uma combinação de HTML e conteúdo dinâmico.

      !["Ação de postagem da condição verdadeira de alerta de métrica"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Ação de postagem da condição verdadeira do alerta de métrica")

  1. Na condição **If false** , defina uma ação do Microsoft Teams para comunicar que o alerta de métrica não corresponde às expectativas do aplicativo lógico. Inclua a carga JSON. Observe como referenciar o `triggerBody` conteúdo dinâmico na expressão `json()`.

      !["Ação de postagem da condição falsa de alerta de métrica"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Ação de postagem da condição falsa de alerta de métrica")

- A etapa 15 é a mesma. Siga as instruções para salvar seu aplicativo lógico e atualizar seu grupo de ação.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Chamando outros aplicativos além do Microsoft Teams
Os aplicativos lógicos têm vários conectores diferentes que permitem disparar ações em uma ampla variedade de aplicativos e bancos de dados. A margem de atraso, SQL Server, Oracle, Salesforce, são apenas alguns exemplos. Para obter mais informações sobre conectores, consulte [conectores de aplicativos lógicos](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Passos seguintes
* Obtenha uma [visão geral dos alertas do log de atividades do Azure](../../azure-monitor/platform/alerts-overview.md) e saiba como receber alertas.  
* Saiba como [configurar alertas quando uma notificação de integridade do serviço do Azure é lançada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
* Saiba mais sobre [grupos de ações](../../azure-monitor/platform/action-groups.md).

