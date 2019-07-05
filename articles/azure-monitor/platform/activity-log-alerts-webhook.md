---
title: Compreender o esquema de webhook usado nos alertas de registo de atividade
description: Saiba mais sobre o esquema do JSON que será lançado para um URL do webhook quando um alerta de registo de atividade é ativado.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: johnkem
ms.subservice: alerts
ms.openlocfilehash: c91c1badaa4b1bc055859d700857cfd4d062babd
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491504"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooks para alertas de registo de atividades do Azure
Como parte da definição de um grupo de ação, pode configurar pontos finais de webhook para receber notificações de alerta de registo de atividade. Com webhooks, pode encaminhar o estas notificações para outros sistemas para ações de pós-processamento ou personalizados. Este artigo mostra o payload para o HTTP POST para um webhook como fica.

Para obter mais informações sobre alertas de registo de atividade, consulte como [criar alertas do registo de atividades do Azure](activity-log-alerts.md).

Para obter informações sobre grupos de ação, veja como [criar grupos de ação](../../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Também pode utilizar o [esquema comum do alerta](https://aka.ms/commonAlertSchemaDocs), que fornece a vantagem de ter um único extensível e payload de alerta unificada em todas as o alerta no Monitor do Azure, para serviços suas integrações do webhook. [Saiba mais sobre as definições de esquema de alerta comuns.](https://aka.ms/commonAlertSchemaDefinitions)


## <a name="authenticate-the-webhook"></a>Autenticar o webhook
O webhook, opcionalmente, pode utilizar a autorização baseada em tokens para autenticação. O webhook URI é guardado com um ID de token, por exemplo, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Esquema do payload
O payload JSON contido na operação POST difere com base no campo de data.context.activityLog.eventSource o payload.

### <a name="common"></a>Common

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>Administrativa

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Segurança

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>Recomendação

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
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>ServiceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

Para detalhes de esquema específico sobre alertas de registo de atividade de notificação de estado de funcionamento de serviço, consulte [notificações de estado de funcionamento de serviço](../../azure-monitor/platform/service-notifications.md). Além disso, saiba como [configurar notificações de webhook do Estado de funcionamento de serviço com as suas soluções de gerenciamento existente do problema](../../service-health/service-health-alert-webhook-guide.md).

### <a name="resourcehealth"></a>ResourceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| Nome do elemento | Descrição |
| --- | --- |
| status |Utilizado para alertas de métricas. Sempre definido como "ativado" para os alertas de registo de atividade. |
| context |Contexto do evento. |
| resourceProviderName |O fornecedor de recursos do recurso afetado. |
| conditionType |Sempre "Event". |
| name |Nome da regra de alerta. |
| id |ID de recurso do alerta. |
| description |Descrição do alerta definida quando é criado o alerta. |
| subscriptionId |ID de subscrição do Azure. |
| timestamp |Tempo em que o evento foi gerado pelo serviço do Azure que processou o pedido. |
| resourceId |ID de recurso do recurso afetado. |
| resourceGroupName |Nome do grupo de recursos para o recurso afetado. |
| properties |Conjunto de `<Key, Value>` pares (ou seja, `Dictionary<String, String>`) que inclui detalhes sobre o evento. |
| event |Elemento que contém metadados sobre o evento. |
| authorization |As propriedades de controlo de acesso baseado em funções do evento. Essas propriedades incluem, geralmente, a ação, a função e o âmbito. |
| category |Categoria do evento. Valores suportados incluem a administração, alerta, segurança, ServiceHealth e recomendação. |
| Autor da chamada |Endereço de e-mail do utilizador que executou a operação, a afirmação UPN ou a afirmação SPN com base na disponibilidade. Pode ser nulo para certas chamadas de sistema. |
| correlationId |Normalmente, um GUID no formato de cadeia de caracteres. Eventos com correlationId pertencem à mesma ação maior e normalmente partilham uma correlationId. |
| eventDescription |Descrição de texto estático do evento. |
| eventDataId |Identificador exclusivo para o evento. |
| eventSource |Nome do serviço do Azure ou infra-estrutura que gerou o evento. |
| httpRequest |O pedido normalmente inclui o clientRequestId clientIpAddress e método HTTP (por exemplo, COLOQUE). |
| level |Um dos seguintes valores: Crítico, erro, aviso e informativas. |
| operationId |Normalmente, um GUID compartilhado entre os eventos correspondentes a única operação. |
| operationName |Nome da operação. |
| properties |Propriedades do evento. |
| status |cadeia de caracteres. Estado da operação. Os valores comuns incluem a introdução, em curso, com êxito, falha, Active Directory e resolvido. |
| subStatus |Normalmente, inclui o código de estado HTTP da chamada REST correspondente. Também pode incluir outras cadeias de caracteres que descrevem um subestado. Os valores de subestado comuns incluem OK (código de estado HTTP: 200), criado (código de estado HTTP: 201), aceite (código de estado HTTP: 202), não existe conteúdo (código de estado HTTP: 204), pedido incorreto (código de estado HTTP: 400), não encontrado (código de estado HTTP: 404), conflito (código de estado HTTP: 409), erro de servidor interno (código de estado HTTP: 500), serviço indisponível (código de estado HTTP: 503) e o tempo limite do Gateway (código de estado HTTP: 504). |

Para obter detalhes de esquema específico em todos os outros alertas de registo de atividade, consulte [descrição geral do registo de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md).

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre o registo de atividades](../../azure-monitor/platform/activity-logs-overview.md).
* [Executar scripts de automatização do Azure (Runbooks) nos alertas do Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Utilizar uma aplicação lógica para enviar um SMS através do Twilio a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Neste exemplo é para alertas de métricas, mas pode ser modificado para funcionar com um alerta de registo de atividades.
* [Utilizar uma aplicação lógica para enviar uma mensagem Slack a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Neste exemplo é para alertas de métricas, mas pode ser modificado para funcionar com um alerta de registo de atividades.
* [Utilizar uma aplicação lógica para enviar uma mensagem numa fila do Azure a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Neste exemplo é para alertas de métricas, mas pode ser modificado para funcionar com um alerta de registo de atividades.

