---
title: Configurar alertas de estado de funcionamento do serviço do Azure com o ServiceNow | Documentos da Microsoft
description: Obtenha notificações personalizadas sobre eventos de estado de funcionamento de serviço à sua instância do ServiceNow.
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 11/14/2017
ms.openlocfilehash: f17215a5695128bf2ea507efa0c12fdbba9467d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620945"
---
# <a name="configure-service-health-alerts-with-servicenow"></a>Configurar alertas de estado de funcionamento do serviço com ServiceNow

Este artigo mostra-lhe como integrar alertas de estado de funcionamento do serviço do Azure com ServiceNow através de um webhook. Depois de configurar a integração de webhook com a instância do ServiceNow, receber alertas por meio de sua infraestrutura de notificação existente quando é afetado por problemas de serviço do Azure. Sempre que é acionado um alerta de Azure Service Health, ele chama um webhook através da API de REST de script do ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Criar uma API de REST com script no ServiceNow
1.  Certifique-se de que se inscreveram cópia de segurança e iniciado sessão na sua [ServiceNow](https://www.servicenow.com/) conta.

1.  Navegue para o **serviços Web do System** secção ServiceNow e selecione **APIs de REST com script**.

    ![A secção de "script serviço Web" em ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Selecione **New** para criar um novo serviço de REST com script.
 
    ![O botão "Novo com script REST API" no ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Adicionar uma **Name** à sua REST API e o conjunto a **ID de API** para `azureservicehealth`.

1.  Selecione **submeter**.

    ![As "definições do API de REST" na ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Selecione a API de REST que criou, e, no **recursos** separador select **New**.

    ![O "Guia de recursos" no ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Nome** seu novo recurso `event` e altere a **método HTTP** para `POST`.

1.  Na **Script** secção, adicione o seguinte código JavaScript:

    >[!NOTE]
    >Tem de atualizar o `<secret>`,`<group>`, e `<email>` valor no script abaixo.
    >* `<secret>` deve ser uma cadeia de caracteres aleatória, como um GUID
    >* `<group>` deve ser o grupo do ServiceNow que pretende atribuir o incidente para
    >* `<email>` deve ser a pessoa específica que pretende atribuir o incidente para (opcional)
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

1.  No separador de segurança, desmarque **requer autenticação** e selecione **submeter**. O `<secret>` conjunto protege esta API em vez disso.

    ![A caixa de verificação "Requer autenticação" na ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Novamente na secção de APIs de REST com script, deve encontrar o **caminho de API de Base** para a nova API de REST:

     ![A "sobre o caminho de API" do ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  O URL de integração completa é semelhante a:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Criar um alerta com ServiceNow no portal do Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga os passos 1 a 8 na [este artigo](../azure-monitor/platform/alerts-activity-log-service-notifications.md) para criar um alerta com um novo grupo de ação.

1. Definir na lista de **ações**:

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O ServiceNow **URL de integração** tinha guardado.

    c. **Nome:** Do Webhook nome, alias ou identificador.

1. Selecione **guardar** quando terminar de criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. Na [portal do Azure](https://portal.azure.com/), selecione **Monitor**.

1. Na **configurações** secção, selecione **grupos de ação**.

1. Localize e selecione o grupo de ação que pretende editar.

1. Adicionar à lista de **ações**:

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O ServiceNow **URL de integração** tinha guardado.

    c. **Nome:** Do Webhook nome, alias ou identificador.

1. Selecione **guardar** quando terminar de atualizar o grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Teste a sua integração de webhook através de um pedido POST de HTTP
1. Crie o payload de estado de funcionamento do serviço que pretende enviar. Pode encontrar um payload de webhook do Estado de funcionamento do serviço do exemplo na [alertas de registo de Webhooks para atividades do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Crie um pedido POST de HTTP da seguinte forma:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Deverá receber um `200 OK` resposta com a mensagem "Incidente criado".

1. Aceda a [ServiceNow](https://www.servicenow.com/) para confirmar a sua integração foi configurada com êxito.

## <a name="next-steps"></a>Passos Seguintes
- Saiba como [configurar notificações de webhook para sistemas de gestão existentes do problema](service-health-alert-webhook-guide.md).
- Reveja os [esquema de webhook de alerta de registo de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Saiba mais sobre [notificações de estado de funcionamento de serviço](../azure-monitor/platform/service-notifications.md).
- Saiba mais sobre [grupos de ação](../azure-monitor/platform/action-groups.md).