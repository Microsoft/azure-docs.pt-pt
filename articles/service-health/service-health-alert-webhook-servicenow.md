---
title: Envie alertas de saúde de serviço Azure com serviceNow
description: Obtenha notificações personalizadas sobre eventos de saúde de serviço para a sua instância ServiceNow.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 3daae05aabff571010d043cf5602847e95ea29f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654108"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Envie alertas de saúde de serviço Azure com serviceNow usando webhooks

Este artigo mostra-lhe como integrar alertas de saúde de serviço Azure com o ServiceNow usando um webhook. Depois de configurar a integração webhook com a sua instância ServiceNow, recebe alertas através da sua infraestrutura de notificação existente quando os problemas de serviço do Azure o afetam. Sempre que um alerta de serviço azure health dispara, chama um webhook através da API de REST Scripted serviceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Criação de uma API REST scripted em ServiceNow

1.  Certifique-se de que se inscreveu e está inscrito na sua conta [ServiceNow.](https://www.servicenow.com/)

1.  Navegue para a secção **serviços web do sistema** no ServiceNow e selecione **APIs REST scripted**.

    ![A secção "Serviço Web Scripted" no ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Selecione **Novo** para criar um novo serviço Scripted REST.
 
    ![O botão "New Scripted REST API" no ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Adicione um **Nome** à sua API REST `azureservicehealth`e coloque o ID **DaPi** para .

1.  Selecione **Submeter**.

    ![As "Definições REST API" no ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Selecione a API REST que criou e, sob o separador **Recursos,** selecione **New**.

    ![O "Separador de Recursos" no ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Nomeie** `event` o seu novo `POST`recurso e altere o método **HTTP** para .

1.  Na secção **Script,** adicione o seguinte código JavaScript:

    >[!NOTE]
    >Precisa atualizar o `<secret>``<group>`, `<email>` e o valor no script abaixo.
    >* `<secret>`deve ser uma corda aleatória, como um GUID
    >* `<group>`deve ser o grupo ServiceNow que pretende atribuir o incidente a
    >* `<email>`deve ser a pessoa específica a quem pretende atribuir o incidente (opcional)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  No separador de segurança, **desmarcar requer autenticação** e selecione **Enviar**. O `<secret>` conjunto protege esta API.

    ![A caixa de verificação "Requer Autenticação" no ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  De volta à secção APIs de REPOUSO Scripted, você deve encontrar o **Caminho Base API** para a sua nova API REST:

     ![O "Caminho Base API" no ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  O seu URL completo de Integração parece:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Crie um alerta usando o ServiceNow no portal Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga os passos 1 a 8 [deste artigo](../azure-monitor/platform/alerts-activity-log-service-notifications.md) para criar um alerta com um novo grupo de ação.

1. Definir na lista de **Ações:**

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O **URL de Integração** ServiceNow que guardou anteriormente.

    c. **Nome:** O nome do Webhook, pseudónimo, ou identificador.

1. Selecione **Guardar** quando estiver feito para criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal Azure,](https://portal.azure.com/)selecione **Monitor**.

1. Na secção **Definições,** selecione **grupos de ação**.

1. Encontre e selecione o grupo de ação que pretende editar.

1. Adicione à lista de **Ações:**

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O **URL de Integração** ServiceNow que guardou anteriormente.

    c. **Nome:** O nome do Webhook, pseudónimo, ou identificador.

1. Selecione **Guardar** quando for feito para atualizar o grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testar a sua integração webhook através de um pedido HTTP POST
1. Crie a carga de saúde de serviço que pretende enviar. Pode encontrar uma carga útil de webhook de serviço de exemplo em Webhooks para alertas de registo de [atividade sinuosos do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Crie um pedido HTTP POST da seguinte forma:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Deve receber `200 OK` uma resposta com a mensagem "Incidente criado".

1. Vá ao [ServiceNow](https://www.servicenow.com/) para confirmar que a sua integração foi criada com sucesso.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [configurar notificações de webhook para sistemas de gestão de problemas existentes.](service-health-alert-webhook-guide.md)
- Reveja o esquema de alerta de registo de [atividade](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Conheça as notificações de saúde de [serviço.](../azure-monitor/platform/service-notifications.md)
- Saiba mais sobre [grupos de ação.](../azure-monitor/platform/action-groups.md)