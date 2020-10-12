---
title: Crie alertas do Azure Advisor para novas recomendações usando o portal Azure
description: Crie alertas do Azure Advisor para nova recomendação
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 837f12a12e532902ee76e345afa430f758f79c10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90973657"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations-using-the-azure-portal"></a>Criar alertas do Azure Advisor sobre novas recomendações através do portal Azure 

Este artigo mostra-lhe como configurar um alerta para novas recomendações do Azure Advisor usando o portal Azure. 

Sempre que o Azure Advisor deteta uma nova recomendação para um dos seus recursos, um evento é armazenado no [diário de atividades Azure](../azure-monitor/platform/platform-logs-overview.md). Pode configurar alertas para estes eventos do Azure Advisor utilizando uma experiência de criação de alertas específicos de recomendação. Pode selecionar uma subscrição e opcionalmente um grupo de recursos para especificar os recursos em que deseja receber alertas. 

Também pode determinar os tipos de recomendações utilizando estas propriedades:

* Categoria
* Nível de impacto
* Tipo de recomendação

Também pode configurar a ação que ocorrerá quando um alerta é desencadeado por:  

* Selecionando um grupo de ação existente
* Criação de um novo grupo de ação

Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

> [!NOTE] 
> Atualmente, os alertas de aconselhamento só estão disponíveis para recomendações de Alta Disponibilidade, Desempenho e Custo. Recomendações de segurança não são apoiadas. 

## <a name="create-alert-rule"></a>Criar regra de alerta
1. No **portal,** selecione **Azure Advisor**.

    ![Azure Advisor no portal](./media/advisor-alerts/create1.png)

2. Na secção **de Monitorização** do menu esquerdo, selecione **Alertas**. 

    ![Alertas no Conselheiro](./media/advisor-alerts/create2.png)

3. Selecione **Novo Aviso de Conselheiro**.

    ![Novo alerta de conselheiro](./media/advisor-alerts/create3.png)

4. Na secção **Âmbito,** selecione a subscrição e opcionalmente o grupo de recursos em que pretende ser alertado. 

    ![Âmbito de alerta de conselheiro](./media/advisor-alerts/create4.png)

5. Na secção **Condição,** selecione o método que pretende utilizar para configurar o seu alerta. Se quiser alertar para todas as recomendações para uma determinada categoria e/ou nível de impacto, selecione **categoria e nível de impacto**. Se quiser alertar para todas as recomendações de um determinado tipo, selecione **o tipo de Recomendação**.

    ![Condição de alerta do Conselheiro Azure](./media/advisor-alerts/create5.png)

6. Dependendo da configuração por opção que selecionar, poderá especificar os critérios. Se quiser todas as recomendações, deixe os campos restantes em branco. 

    ![Grupo de ação de alerta de conselheiro](./media/advisor-alerts/create6.png)

7. Na secção **grupos de ação,** selecione **Adicionar a existência** para utilizar um grupo de ação que já criou ou selecione Criar **novo** para criar um novo grupo [de ação](../azure-monitor/platform/action-groups.md). 

    ![Alerta de aviso adicionar existente](./media/advisor-alerts/create7.png)

8. Na secção de detalhes do Alerta, forneça ao seu alerta um nome e uma breve descrição. Se quiser que o seu alerta esteja ativado, deixe **a regra de ativação na** seleção da criação definida para **Sim**. Em seguida, selecione o grupo de recursos para guardar o seu alerta para. Isto não afetará o âmbito de aplicação da recomendação. 

    :::image type="content" source="./media/advisor-alerts/create8.png" alt-text="Screenshot da secção de detalhes do alerta.":::


## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Configure alertas de recomendação para usar um webhook
Esta secção mostra-lhe como configurar alertas do Azure Advisor para enviar dados de recomendação através de webhooks para os seus sistemas existentes. 

Pode configurar alertas para ser notificado quando tiver uma nova recomendação do Advisor sobre um dos seus recursos. Estes alertas podem notificá-lo através de e-mail ou mensagem de texto, mas também podem ser utilizados para integrar-se com os sistemas existentes através de um webhook. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Utilização da carga útil de alerta de recomendação do Advisor
Se pretender integrar alertas Advisor nos seus próprios sistemas utilizando um webhook, terá de analisar a carga útil JSON que é enviada a partir da notificação. 

Quando configurar o seu grupo de ação para este alerta, selecione se pretender utilizar o esquema de alerta comum. Se selecionar o esquema de alerta comum, a sua carga útil será: 

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

Se não utilizar o esquema comum, a sua carga útil parece ser o seguinte: 

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

Em qualquer esquema, pode identificar eventos de recomendação do Advisor procurando **eventoSource** `Recommendation` é e **operationName**  é `Microsoft.Advisor/recommendations/available/action` .

Alguns dos outros campos importantes que pode querer utilizar são: 

* *alertTargetIDs* (no esquema comum) ou *recursosId* (esquema legado)
* *recomendaçãoType*
* *nome de recomendação*
* *recomendaçãoCategoria*
* *recomendaçãoImpact*
* *recomendaçãoResourceLink*


## <a name="manage-your-alerts"></a>Gerir os alertas 

A partir do Azure Advisor, pode editar, eliminar ou desativar e ativar os alertas de recomendações. 

1. No **portal,** selecione **Azure Advisor**.

    :::image type="content" source="./media/advisor-alerts/create1.png" alt-text="Screenshot da secção de detalhes do alerta.":::

2. Na secção **de Monitorização** do menu esquerdo, selecione **Alertas**.

    :::image type="content" source="./media/advisor-alerts/create2.png" alt-text="Screenshot da secção de detalhes do alerta.":::

3. Para editar um alerta, clique no nome Alerta para abrir o alerta e editar os campos que pretende editar.

4. Para eliminar, ativar ou desativar um alerta, clique na elipse no final da linha e, em seguida, selecione a ação que pretende tomar.
 

## <a name="next-steps"></a>Passos seguintes
- Obtenha uma [visão geral dos alertas de registo de atividades](../azure-monitor/platform/alerts-overview.md)e aprenda a receber alertas.
- Saiba mais sobre [grupos de ação.](../azure-monitor/platform/action-groups.md)
