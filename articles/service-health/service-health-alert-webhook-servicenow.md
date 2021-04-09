---
title: Envie alertas de saúde do serviço Azure com o ServiceNow
description: Receba notificações personalizadas sobre eventos de saúde de serviço para o seu caso ServiceNow.
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: devx-track-js
ms.openlocfilehash: df6596dd9853a792b5bfdb333361a2b9cd02a347
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588186"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Envie alertas de saúde do serviço Azure com o ServiceNow usando webhooks

Este artigo mostra-lhe como integrar alertas de saúde do serviço Azure com o ServiceNow usando um webhook. Depois de configurar a integração do webhook com o seu caso ServiceNow, recebe alertas através da sua infraestrutura de notificação existente quando os problemas de serviço do Azure o afetam. Sempre que um alerta de Saúde do Serviço Azure dispara, chama um webhook através da API escrita do ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Criar uma API DE REST scripted no ServiceNow

1.  Certifique-se de que se inscreveu e está inscrito na sua conta [ServiceNow.](https://www.servicenow.com/)

1.  Navegue para a secção **de Serviços Web do Sistema** no ServiceNow e selecione **APIs de REST Scripted**.

    ![A secção "Serviço Web Scripted" no ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Selecione **Novo** para criar um novo serviço SCRIPTED REST.
 
    ![O botão "New Scripted REST API" no ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Adicione um **Nome** à sua API REST e desemque o ID da **API** para `azureservicehealth` .

1.  Selecione **Submeter**.

    ![As "Definições DE API REST" no ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Selecione a API REST que criou e no separador **Recursos** selecione **Novo**.

    ![O "Separador de Recursos" no ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Nomeie** o seu novo recurso `event` e altere o **método HTTP** para `POST` .

1.  Na secção **Script,** adicione o seguinte código JavaScript:

    >[!NOTE]
    >Tem de atualizar o `<secret>` , e valorizar no script `<group>` `<email>` abaixo.
    >* `<secret>` deve ser uma corda aleatória, como um GUID
    >* `<group>` deve ser o grupo ServiceNow que quer atribuir o incidente a
    >* `<email>` deve ser a pessoa específica a quem pretende atribuir o incidente (opcional)
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
                var short_description = "Azure Service Health&quot;;
                if (event.data.context.activityLog.properties.incidentType == &quot;Incident") {
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

1.  No separador de segurança, **desmarca-se requer autenticação** e selecione **Enviar por isso.** Em `<secret>` vez disso, o conjunto protege esta API.

    ![A caixa de verificação "Requer autenticação" no ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  De volta à secção APIs do REST Scripted, deverá encontrar o **Caminho base da API** para a sua nova API REST:

     ![O "Caminho base da API" no ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  O seu URL de integração completo parece:

    ```http
    https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>
    ```

## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Criar um alerta usando o ServiceNow no portal Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga os passos 1 a 8 [neste artigo](./alerts-activity-log-service-notifications-portal.md) para criar um alerta com um novo grupo de ação.

1. Definir na lista de **ações:**

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O **URL de Integração serviceNow** que guardou anteriormente.

    c. **Nome:** Nome de Webhook, pseudónimo ou identificador.

1. **Selecione Guardar** quando for feito para criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal Azure,](https://portal.azure.com/)selecione **Monitor**.

1. Na secção **Definições,** selecione **Grupos de Ação**.

1. Encontre e selecione o grupo de ação que pretende editar.

1. Adicione à lista de **Ações:**

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O **URL de Integração serviceNow** que guardou anteriormente.

    c. **Nome:** Nome de Webhook, pseudónimo ou identificador.

1. **Selecione Guardar** quando for feito para atualizar o grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testar a sua integração webhook através de um pedido HTTP POST
1. Crie a carga útil de saúde do serviço que pretende enviar. Você pode encontrar um serviço de exemplo webhook payload em [Webhooks para alertas de registo de atividade Azure](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Crie um pedido HTTP POST da seguinte forma:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Deve receber uma `200 OK` resposta com a mensagem "Incidente criado".

1. Vá ao [ServiceNow](https://www.servicenow.com/) confirmar que a sua integração foi configurada com sucesso.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [configurar notificações webhook para os sistemas de gestão de problemas existentes](service-health-alert-webhook-guide.md).
- Reveja o [esquema de alerta de registo de atividade webhook](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Saiba mais sobre [as notificações de saúde do serviço.](./service-notifications.md)
- Saiba mais sobre [grupos de ação.](../azure-monitor/alerts/action-groups.md)
