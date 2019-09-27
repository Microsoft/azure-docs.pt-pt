---
title: Criar alertas do supervisor do Azure para novas recomendações | Microsoft Docs
description: Criar alertas do supervisor do Azure para nova recomendação
services: advisor
author: sagupt
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/09/2019
ms.author: sagupt
ms.openlocfilehash: d641008a7caf4a006344d886aec945a6e8da2568
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326620"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Criar alertas do supervisor do Azure sobre novas recomendações 

Este artigo mostra como configurar um alerta para novas recomendações do Azure Advisor usando os modelos portal do Azure e Azure Resource Manager. 

Sempre que o Azure Advisor detecta uma nova recomendação para um de seus recursos, um evento é armazenado no [log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview). Você pode configurar alertas para esses eventos do Azure Advisor usando uma experiência de criação de alertas específica de recomendação. Você pode selecionar uma assinatura e, opcionalmente, um grupo de recursos para especificar os recursos nos quais deseja receber alertas. 

Você também pode determinar os tipos de recomendações usando essas propriedades:

* Category
* Nível de impacto
* Tipo de recomendação

Você também pode configurar a ação que ocorrerá quando um alerta for disparado por:  

* Selecionando um grupo de ação existente
* Criando um novo grupo de ação

Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

> [!NOTE] 
> Atualmente, os alertas do Advisor estão disponíveis apenas para alta disponibilidade, desempenho e recomendações de custo. Não há suporte para recomendações de segurança. 

## <a name="in-the-azure-portal"></a>No portal do Azure
1. No **portal**, selecione **Azure Advisor**.

    ![Assistente do Azure no portal](./media/advisor-alerts/create1.png)

2. Na seção **monitoramento** do menu à esquerda, selecione **alertas**. 

    ![Alertas no Advisor](./media/advisor-alerts/create2.png)

3. Selecione **novo alerta do Advisor**.

    ![Novo alerta do Advisor](./media/advisor-alerts/create3.png)

4. Na seção **escopo** , selecione a assinatura e, opcionalmente, o grupo de recursos no qual você deseja ser alertado. 

    ![Escopo do alerta do Advisor](./media/advisor-alerts/create4.png)

5. Na seção **condição** , selecione o método que você deseja usar para configurar o alerta. Se você quiser alertar para todas as recomendações para uma determinada categoria e/ou nível de impacto, selecione **categoria e nível de impacto**. Se você quiser alertar para todas as recomendações de um determinado tipo, selecione **tipo de recomendação**.

    ![Condição de alerta do Azure Advisor](./media/advisor-alerts/create5.png)

6. Dependendo da opção Configurar por que você selecionar, você poderá especificar os critérios. Se você quiser todas as recomendações, apenas deixe os campos restantes em branco. 

    ![Grupo de ações de alerta do Advisor](./media/advisor-alerts/create6.png)

7. Na seção **grupos de ações** , selecione **Adicionar existente** para usar um grupo de ação que você já criou ou selecione **criar novo** para configurar um novo [grupo de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups). 

    ![Alerta do Advisor-adicionar existente](./media/advisor-alerts/create7.png)

8. Na seção detalhes do alerta, dê um nome e uma descrição breve ao seu alerta. Se você quiser que o alerta seja habilitado, deixe **habilitar regra na** seleção de criação definido como **Sim**. Em seguida, selecione o grupo de recursos para salvar o alerta. Isso não afetará o escopo de direcionamento da recomendação. 

    ![Faixa do supervisor do Azure](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Com um modelo de Azure Resource Manager

Esse modelo do Resource Manager cria um alerta de recomendação e um novo grupo de ação.

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

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Configurar alertas de recomendação para usar um webhook
Esta seção mostra como configurar alertas do Azure Advisor para enviar dados de recomendação por meio de WebHooks para seus sistemas existentes. 

Você pode configurar alertas para serem notificados quando tiver uma nova recomendação do Advisor em um de seus recursos. Esses alertas podem notificá-lo por email ou mensagem de texto, mas também podem ser usados para integração com seus sistemas existentes por meio de um webhook. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Usando a carga de alerta de recomendação do Advisor
Se desejar integrar alertas do Advisor em seus próprios sistemas usando um webhook, você precisará analisar a carga JSON que é enviada da notificação. 

Ao configurar o grupo de ações para esse alerta, você seleciona se deseja usar o esquema de alerta comum. Se você selecionar o esquema de alerta comum, sua carga terá a seguinte aparência: 

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

Se você não usar o esquema comum, sua carga será parecida com a seguinte: 

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

Em qualquer esquema, você pode identificar eventos de recomendação do Advisor procurando por EventSource `Recommendation` é e operationName `Microsoft.Advisor/recommendations/available/action`é.

Alguns dos outros campos importantes que você pode querer usar são: 

* *alertTargetIDs* (no esquema comum) ou *ResourceId* (esquema herdado)
* *recomendaçãotype*
* *recomendaçãoname*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>Gerir os alertas 

No Azure Advisor, você pode editar, excluir ou desabilitar e habilitar seus alertas de recomendações. 

1. No **portal**, selecione **Azure Advisor**.

    ![Faixa do supervisor do Azure](./media/advisor-alerts/create1.png)

2. Na seção **monitoramento** do menu à esquerda, selecione **alertas**.

    ![Faixa do supervisor do Azure](./media/advisor-alerts/create2.png)

3. Para editar um alerta, clique no nome do alerta para abrir o alerta e editar os campos que você deseja editar.

4. Para excluir, habilitar ou desabilitar um alerta, clique na elipse no final da linha e selecione a ação que você deseja executar.
 

