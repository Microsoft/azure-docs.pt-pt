---
title: Criar alertas azure advisor para novas recomendações
description: Criar alertas azure advisor para nova recomendação
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 07cbc57ef718b6cac104d2b5238ff4e3196f197a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75443161"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Create Azure Advisor alerta sobre novas recomendações 

Este artigo mostra-lhe como configurar um alerta para novas recomendações do Azure Advisor utilizando os modelos do portal Azure e do Gestor de Recursos Azure. 

Sempre que o Azure Advisor deteta uma nova recomendação para um dos seus recursos, um evento é armazenado no [registo da Atividade Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) Pode configurar alertas para estes eventos a partir do Azure Advisor utilizando uma experiência de criação de alertas específico supéris. Pode selecionar uma subscrição e, opcionalmente, um grupo de recursos para especificar os recursos que pretende receber alertas. 

Também pode determinar os tipos de recomendações utilizando estas propriedades:

* Categoria
* Nível de impacto
* Tipo de recomendação

Também pode configurar a ação que ocorrerá quando um alerta for desencadeado por:  

* Selecionando um grupo de ação existente
* Criação de um novo grupo de ação

Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

> [!NOTE] 
> Atualmente, os alertas de aconselhamento só estão disponíveis para recomendações de Alta Disponibilidade, Desempenho e Custos. As recomendações de segurança não são apoiadas. 

## <a name="in-the-azure-portal"></a>No portal do Azure
1. No **portal,** selecione **Azure Advisor**.

    ![Azure Advisor no portal](./media/advisor-alerts/create1.png)

2. Na secção **de Monitorização** do menu esquerdo, selecione **Alertas**. 

    ![Alertas em Assessor](./media/advisor-alerts/create2.png)

3. Selecione **Novo Alerta De Conselheiro**.

    ![Novo alerta de conselheiro](./media/advisor-alerts/create3.png)

4. Na secção **Scope,** selecione a subscrição e opcionalmente o grupo de recursos que pretende alertar. 

    ![Âmbito de alerta de aconselhamento](./media/advisor-alerts/create4.png)

5. Na secção **Condição,** selecione o método que pretende utilizar para configurar o seu alerta. Se pretender alertar para todas as recomendações para uma determinada categoria e/ou nível de impacto, selecione **categoria e nível**de impacto . Se quiser alertar para todas as recomendações de um determinado tipo, selecione **o tipo de Recomendação**.

    ![Estado de alerta do Azure Advisor](./media/advisor-alerts/create5.png)

6. Dependendo da Configuração por opção que selecionar, poderá especificar os critérios. Se quiser todas as recomendações, deixe os restantes campos em branco. 

    ![Grupo de ação de alerta de aconselhamento](./media/advisor-alerts/create6.png)

7. Na secção grupos de **ação,** selecione **Adicionar existente** para utilizar um grupo de ação que já criou ou selecione Criar **novo** para criar um novo grupo de [ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups). 

    ![Alerta de conselheiro adicionar existente](./media/advisor-alerts/create7.png)

8. Na secção de detalhes do Alerta, forneça ao seu alerta um nome e uma descrição curta. Se quiser ativar o seu alerta, deixe a regra Enable sobre a seleção de **criação** definida para **Sim**. Em seguida, selecione o grupo de recursos para guardar o seu alerta para. Isto não afetará o âmbito de aplicação da recomendação. 

    ![Banner de conselheiro azure](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Com um modelo de Gestor de Recursos Azure

Este modelo de Gestor de Recursos cria um alerta de recomendação e um novo grupo de ação.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest2",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
  ```

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Configure alertas de recomendação para usar um webhook
Esta secção mostra-lhe como configurar alertas do Azure Advisor para enviar dados de recomendação através de webhooks para os seus sistemas existentes. 

Pode configurar alertas para serem notificados quando tiver uma nova recomendação do Advisor sobre um dos seus recursos. Estes alertas podem notificá-lo através de e-mail ou mensagem de texto, mas também podem ser usados para integrar com os seus sistemas existentes através de um webhook. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Utilização da carga útil de alerta de recomendação do Advisor
Se pretender integrar alertas Advisor nos seus próprios sistemas utilizando um webhook, terá de analisar a carga útil JSON que é enviada a partir da notificação. 

Quando configurar o seu grupo de ação para este alerta, selecione se quiser utilizar o esquema de alerta comum. Se selecionar o esquema de alerta comum, a sua carga útil será como: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Se não utilizar o esquema comum, a sua carga útil parece ser a seguinte: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

Em qualquer um dos esquemas, pode identificar eventos `Recommendation` de recomendação do Advisor procurando **eventoSSource** is and **operationName** is `Microsoft.Advisor/recommendations/available/action`.

Alguns dos outros campos importantes que pode querer usar são: 

* *alertaTargetIDs* (no esquema comum) ou *resourceId* (esquema legado)
* *recomendaçãoTipo*
* *nome de recomendação*
* *categoria de recomendação*
* *recomendaçãoImpacto*
* *recomendaçãoResourceLink*


## <a name="manage-your-alerts"></a>Gerir os alertas 

A partir do Azure Advisor, pode editar, eliminar ou desativar e ativar os seus alertas de recomendações. 

1. No **portal,** selecione **Azure Advisor**.

    ![Banner de conselheiro azure](./media/advisor-alerts/create1.png)

2. Na secção **de Monitorização** do menu esquerdo, selecione **Alertas**.

    ![Banner de conselheiro azure](./media/advisor-alerts/create2.png)

3. Para editar um alerta, clique no nome Alerta para abrir o alerta e editar os campos que pretende editar.

4. Para eliminar, ativar ou desativar um alerta, clique na elipse no final da linha e, em seguida, selecione a ação que gostaria de tomar.
 

