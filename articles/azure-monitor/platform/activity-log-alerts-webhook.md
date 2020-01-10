---
title: Entender o esquema de webhook usado em alertas do log de atividades
description: Saiba mais sobre o esquema do JSON que é Postado em uma URL de webhook quando um alerta do log de atividades é ativado.
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 03/31/2017
ms.openlocfilehash: eb43db7a67063622f6a6125178267573cd209471
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748795"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>WebHooks para alertas do log de atividades do Azure
Como parte da definição de um grupo de ações, você pode configurar pontos de extremidade de webhook para receber notificações de alerta do log de atividades. Com WebHooks, você pode rotear essas notificações para outros sistemas para pós-processamento ou ações personalizadas. Este artigo mostra a aparência da carga do HTTP POST para um webhook.

Para obter mais informações sobre alertas do log de atividades, consulte como [criar alertas do log de atividades do Azure](activity-log-alerts.md).

Para obter informações sobre grupos de ações, consulte como [criar grupos de ações](../../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Você também pode usar o [esquema de alerta comum](https://aka.ms/commonAlertSchemaDocs), que fornece a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta no Azure monitor, para suas integrações de webhook. [Saiba mais sobre as definições de esquema de alerta comuns.](https://aka.ms/commonAlertSchemaDefinitions)


## <a name="authenticate-the-webhook"></a>Autenticar o webhook
O webhook pode, opcionalmente, usar a autorização baseada em token para autenticação. O URI do webhook é salvo com uma ID de token, por exemplo, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Esquema de carga
A carga JSON contida na operação POST difere com base no campo Data. Context. activityLog. eventSource do Payload.

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

Para obter detalhes sobre o esquema específico sobre alertas de log de atividades de notificação de integridade do serviço, consulte [notificações de integridade do serviço](../../azure-monitor/platform/service-notifications.md). Além disso, saiba como [configurar notificações de webhook de integridade do serviço com suas soluções de gerenciamento de problemas existentes](../../service-health/service-health-alert-webhook-guide.md).

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
| status |Usado para alertas de métrica. Sempre definido como "ativado" para alertas do log de atividades. |
| noticioso |Contexto do evento. |
| resourceProviderName |O provedor de recursos do recurso afetado. |
| conditionType |Sempre "evento". |
| nome |Nome da regra de alerta. |
| ID |ID de recurso do alerta. |
| descrição |Descrição do alerta definida quando o alerta é criado. |
| subscriptionId |ID da assinatura do Azure. |
| carimbo de data/hora |Hora em que o evento foi gerado pelo serviço do Azure que processou a solicitação. |
| resourceId |ID de recurso do recurso afetado. |
| resourceGroupName |Nome do grupo de recursos para o recurso afetado. |
| propriedades |Conjunto de pares de `<Key, Value>` (ou seja, `Dictionary<String, String>`) que inclui detalhes sobre o evento. |
| event |Elemento que contém metadados sobre o evento. |
| authorization |As propriedades do controle de acesso baseado em função do evento. Essas propriedades geralmente incluem a ação, a função e o escopo. |
| categoria |Categoria do evento. Os valores com suporte incluem administrativo, alerta, segurança, integridade e recomendação. |
| chamado |Endereço de email do usuário que realizou a operação, declaração de UPN ou declaração de SPN com base na disponibilidade. Pode ser NULL para determinadas chamadas do sistema. |
| correlationId |Geralmente um GUID no formato de cadeia de caracteres. Os eventos com CorrelationId pertencem à mesma ação maior e geralmente compartilham uma CorrelationId. |
| eventDescription |Descrição de texto estático do evento. |
| eventDataId |Identificador exclusivo do evento. |
| eventSource |Nome do serviço do Azure ou da infraestrutura que gerou o evento. |
| httpRequest |A solicitação geralmente inclui o método clientRequestId, clientIpAddress e HTTP (por exemplo, PUT). |
| level |Um dos seguintes valores: crítico, erro, aviso e informativo. |
| operationId |Geralmente, um GUID compartilhado entre os eventos correspondentes a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Propriedades do evento. |
| status |Cadeia. Estado da operação. Os valores comuns incluem iniciado, em andamento, com êxito, com falha, ativo e resolvido. |
| subStatus |Geralmente inclui o código de status HTTP da chamada REST correspondente. Ele também pode incluir outras cadeias de caracteres que descrevem um substatus. Valores de substatus comuns incluem OK (código de status HTTP: 200), criado (código de status HTTP: 201), aceito (código de status HTTP: 202), sem conteúdo (código de status HTTP: 204), solicitação incorreta (código de status http: 400), não encontrado (código de status HTTP: 404), conflito (código de status http: 409 ), Erro interno do servidor (código de status HTTP: 500), serviço não disponível (código de status HTTP: 503) e tempo limite do gateway (código de status HTTP: 504). |

Para obter detalhes sobre o esquema específico sobre todos os outros alertas do log de atividades, consulte [visão geral do log de atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md).

## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre o log de atividades](../../azure-monitor/platform/platform-logs-overview.md).
* [Execute os scripts de automação do Azure (Runbooks) nos alertas do Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Use um aplicativo lógico para enviar um SMS por meio do twilio de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este exemplo é para alertas de métrica, mas pode ser modificado para funcionar com um alerta do log de atividades.
* [Use um aplicativo lógico para enviar uma mensagem de margem de atraso de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este exemplo é para alertas de métrica, mas pode ser modificado para funcionar com um alerta do log de atividades.
* [Use um aplicativo lógico para enviar uma mensagem a uma fila do Azure de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este exemplo é para alertas de métrica, mas pode ser modificado para funcionar com um alerta do log de atividades.

