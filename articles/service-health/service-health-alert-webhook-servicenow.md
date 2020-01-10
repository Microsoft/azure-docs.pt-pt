---
title: Enviar alertas de integridade do serviço do Azure com o ServiceNow
description: Obtenha notificações personalizadas sobre eventos de integridade do serviço para sua instância do ServiceNow.
ms.topic: article
ms.date: 06/10/2019
ms.openlocfilehash: f332b1e0e188797da172b4ae63f6e5ef1a97e59c
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551612"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Enviar alertas de integridade do serviço do Azure com o ServiceNow usando WebHooks

Este artigo mostra como integrar alertas de integridade do serviço do Azure com o ServiceNow usando um webhook. Depois de configurar a integração de webhook com sua instância do ServiceNow, você obtém alertas por meio de sua infraestrutura de notificação existente quando os problemas de serviço do Azure o afetam. Sempre que um alerta de integridade do serviço do Azure é disparado, ele chama um webhook por meio da API REST com script do ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Criando uma API REST com script no ServiceNow

1.  Verifique se você se inscreveu e entrou em sua conta do [ServiceNow](https://www.servicenow.com/) .

1.  Navegue até a seção **Serviços Web do sistema** no ServiceNow e selecione **APIs REST com script**.

    ![A seção "serviço Web com script" no ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Selecione **novo** para criar um novo serviço REST com script.
 
    ![O botão "nova API REST com script" no ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Adicione um **nome** à sua API REST e defina a **ID da API** como `azureservicehealth`.

1.  Selecione **submeter**.

    ![As "configurações da API REST" no ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Selecione a API REST que você criou e, na guia **recursos** , selecione **novo**.

    ![A "guia de recursos" no ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Nomeie** o novo recurso `event` e altere o **método http** para `POST`.

1.  Na seção **script** , adicione o seguinte código JavaScript:

    >[!NOTE]
    >Você precisa atualizar o `<secret>`,`<group>`e `<email>` valor no script abaixo.
    >* `<secret>` deve ser uma cadeia de caracteres aleatória, como um GUID
    >* `<group>` deve ser o grupo ServiceNow ao qual você deseja atribuir o incidente
    >* `<email>` deve ser a pessoa específica à qual você deseja atribuir o incidente (opcional)
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

1.  Na guia Segurança, desmarque **requer autenticação** e selecione **Enviar**. O `<secret>` definido protege essa API em vez disso.

    ![A caixa de seleção "requer autenticação" no ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  De volta à seção APIs REST com script, você deve encontrar o **caminho da API base** para sua nova API REST:

     ![O "caminho da API base" no ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  A URL de integração completa é semelhante a:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Criar um alerta usando o ServiceNow no portal do Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga as etapas 1 a 8 neste [artigo](../azure-monitor/platform/alerts-activity-log-service-notifications.md) para criar um alerta com um novo grupo de ação.

1. Defina na lista de **ações**:

    a. **Tipo de ação:** *webhook*

    b. **Detalhes:** A **URL de integração** do ServiceNow salva anteriormente.

    c. **Nome:** Nome, alias ou identificador de webhook.

1. Selecione **salvar** quando terminar para criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. Na [portal do Azure](https://portal.azure.com/), selecione **Monitor**.

1. Na seção **configurações** , selecione **grupos de ações**.

1. Localize e selecione o grupo de ações que deseja editar.

1. Adicione à lista de **ações**:

    a. **Tipo de ação:** *webhook*

    b. **Detalhes:** A **URL de integração** do ServiceNow salva anteriormente.

    c. **Nome:** Nome, alias ou identificador de webhook.

1. Selecione **salvar** quando terminar de atualizar o grupo de ações.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testando a integração de webhook por meio de uma solicitação HTTP POST
1. Crie a carga de integridade do serviço que você deseja enviar. Você pode encontrar um conteúdo de webhook de integridade do serviço de exemplo em [WebHooks para alertas do log de atividades do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Crie uma solicitação HTTP POST da seguinte maneira:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Você deve receber uma resposta `200 OK` com a mensagem "incidente criado".

1. Acesse o [ServiceNow](https://www.servicenow.com/) para confirmar que sua integração foi configurada com êxito.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md).
- Examine o [esquema de webhook de alerta do log de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Saiba mais sobre as [notificações de integridade do serviço](../azure-monitor/platform/service-notifications.md).
- Saiba mais sobre [grupos de ações](../azure-monitor/platform/action-groups.md).