---
title: Esquema de eventos de registo de atividades do Azure
description: Compreender o esquema de eventos para dados emitidos para o registo de atividades
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 1/16/2019
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 63c649f0850c4ffc60ce2087e91f3f69917e4837
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868549"
---
# <a name="azure-activity-log-event-schema"></a>Esquema de eventos de registo de atividades do Azure
O **registo de atividades do Azure** é um registo que fornece informações sobre quaisquer eventos de nível de assinatura que ocorreram no Azure. Este artigo descreve o esquema de eventos por categoria de dados. O esquema dos dados é diferente dependendo se estiver lendo os dados no portal, PowerShell, CLI, ou diretamente através da API de REST versus [os dados para armazenamento ou Hubs de eventos com um perfil de registo de transmissão em fluxo](./../../azure-monitor/platform/activity-logs-overview.md#export-the-activity-log-with-a-log-profile). Os exemplos abaixo mostram o esquema, disponibilizada através do portal, PowerShell, CLI e REST API. Um mapeamento dessas propriedades para o [esquema de registos de diagnóstico do Azure](./diagnostic-logs-schema.md) é fornecido no final do artigo.

## <a name="administrative"></a>Administrativa
Esta categoria contém o registo de todos os criar, operações de atualização, eliminação e ação executada através do Resource Manager. Os exemplos dos tipos de eventos que veria nesta categoria incluem "Criar máquina virtual" e "Eliminar grupo de segurança rede" todas as ações executadas por um utilizador ou aplicação com o Resource Manager é modelada como uma operação num tipo de recurso específico. Se o tipo de operação de escrita, eliminação ou ação, os registos de início e de êxito ou falha dessa operação é registada na categoria administrativa. A categoria administrativa também inclui todas as alterações ao controlo de acesso baseado em funções numa subscrição.

### <a name="sample-event"></a>Evento de exemplo
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Descrições das propriedades
| Nome do elemento | Descrição |
| --- | --- |
| Autorização |Blob de propriedades RBAC do evento. Normalmente, inclui as propriedades de "action", "função" e "escopo". |
| chamador |Endereço de e-mail do utilizador que efetuou a operação, a afirmação UPN ou a afirmação SPN com base na disponibilidade. |
| canais |Um dos seguintes valores: "Admin", "Operação" |
| afirmações |O token JWT utilizado pelo Active Directory para autenticar o utilizador ou aplicação para efetuar esta operação no Resource Manager. |
| correlationId |Normalmente, um GUID no formato de cadeia de caracteres. Eventos que partilham uma correlationId pertencem à mesma ação de uber. |
| descrição |Descrição de texto estático de um evento. |
| eventDataId |Identificador exclusivo de um evento. |
| eventName | Nome amigável do evento administrativo. |
| categoria | Sempre "administrativo" |
| httpRequest |BLOB que descreve o pedido de Http. Normalmente inclui o "clientRequestId", "clientIpAddress" e "método" (método HTTP. Por exemplo, colocar). |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Error", "Aviso" e "Informativo" |
| resourceGroupName |Nome do grupo de recursos para o recurso afetado. |
| resourceProviderName |Nome do fornecedor de recursos para o recurso afetado |
| resourceType | O tipo de recurso que foi afetado por um evento administrativo. |
| resourceId |ID de recurso do recurso afetado. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto de `<Key, Value>` pares (ou seja, um dicionário), que descreve os detalhes do evento. |
| status |A cadeia de caracteres que descreve o estado da operação. Alguns valores comuns são: Iniciado, em curso, foi concluída com êxito, falha, Active Directory, resolvido. |
| subStatus |Normalmente, o código de estado HTTP do resto correspondente chamar, mas também pode incluir outras cadeias de caracteres que descreve um subestado, por exemplo, estes valores comuns: OK (código de estado HTTP: 200), criado (código de estado HTTP: 201), aceite (código de estado HTTP: 202), não existe conteúdo (código de estado HTTP: 204), pedido incorreto (código de estado HTTP: 400), não encontrado (código de estado HTTP: 404), conflito (código de estado HTTP: 409), erro de servidor interno (código de estado HTTP: 500), serviço indisponível (código de estado HTTP: 503), tempo limite do gateway (código de estado HTTP: 504). |
| eventTimestamp |Timestamp quando o evento foi gerado pelo processamento do pedido correspondente o evento de serviço do Azure. |
| submissionTimestamp |Timestamp quando o evento se tornou disponível para consulta. |
| subscriptionId |ID de subscrição do Azure. |

## <a name="service-health"></a>Estado de funcionamento do serviço
Esta categoria contém o registo de qualquer incidentes de estado de funcionamento do serviço que ocorreram no Azure. Um exemplo do tipo de evento, que veria nesta categoria é "do SQL Azure na região E.U.A. Leste está a sofrer um período de indisponibilidade." Eventos de estado de funcionamento do serviço têm cinco variedades: É necessária qualquer ação, recuperação assistido, incidente, manutenção, informações ou segurança e só serão apresentadas se tiver um recurso na subscrição que seria afetada pelo evento.

### <a name="sample-event"></a>Evento de exemplo
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Consulte a [notificações de estado de funcionamento de serviço](./../../azure-monitor/platform/service-notifications.md) artigo de documentação sobre os valores nas propriedades.

## <a name="resource-health"></a>Estado de funcionamento de recursos
Esta categoria contém o registo de quaisquer eventos de estado de funcionamento do recurso que ocorreram aos recursos do Azure. Um exemplo do tipo de evento, que veria nesta categoria é o "Estado de funcionamento estado da Máquina Virtual foi alterado para indisponível." Eventos de estado de funcionamento de recursos podem representar um dos quatro Estados de estado de funcionamento: Disponível, indisponível, degradado e desconhecido. Além disso, os eventos de estado de funcionamento de recursos podem ser categorizados como sendo iniciado de plataforma ou utilizador.

### <a name="sample-event"></a>Evento de exemplo

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription Id>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Descrições das propriedades
| Nome do elemento | Descrição |
| --- | --- |
| canais | Sempre "Admin, operação" |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| descrição |Descrição de texto estático do evento de alerta. |
| eventDataId |Identificador exclusivo do evento de alerta. |
| categoria | Sempre "ResourceHealth" |
| eventTimestamp |Timestamp quando o evento foi gerado pelo processamento do pedido correspondente o evento de serviço do Azure. |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Error", "Aviso", "Informativo" e "Verbose" |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| resourceGroupName |Nome do grupo de recursos que contém o recurso. |
| resourceProviderName |Sempre "Microsoft.Resourcehealth/healthevent/action". |
| resourceType | O tipo de recurso que foi afetado por um evento de estado de funcionamento do recurso. |
| resourceId | Nome do ID do recurso para o recurso afetado. |
| status |A cadeia de caracteres que descreve o estado do evento de estado de funcionamento. Os valores podem ser: Active Directory, resolvidos, InProgress, atualizado. |
| subStatus | Normalmente, null para alertas. |
| submissionTimestamp |Timestamp quando o evento se tornou disponível para consulta. |
| subscriptionId |ID de subscrição do Azure. |
| propriedades |Conjunto de `<Key, Value>` pares (ou seja, um dicionário), que descreve os detalhes do evento.|
| properties.title | Uma cadeia de caracteres amigável que descreve o estado de funcionamento do recurso. |
| properties.details | Uma cadeia de caracteres amigável que descreve mais detalhes sobre o evento. |
| properties.currentHealthStatus | O estado de funcionamento atual do recurso. Um dos seguintes valores: "Disponível", "Indisponível", "Degradado" e "Desconhecido". |
| properties.previousHealthStatus | O estado de funcionamento anterior do recurso. Um dos seguintes valores: "Disponível", "Indisponível", "Degradado" e "Desconhecido". |
| properties.type | Uma descrição do tipo de evento de estado de funcionamento de recursos. |
| properties.cause | Uma descrição da causa do evento de estado de funcionamento do recurso. "UserInitiated" e "PlatformInitiated". |


## <a name="alert"></a>Alerta
Esta categoria contém o registo de todas as ativações de alertas do Azure. Um exemplo do tipo de evento, que veria nesta categoria é "% da CPU no myVM foi mais de 80 durante os últimos 5 minutos." Uma variedade de sistemas do Azure tem um conceito de alerta – pode definir uma regra de algum tipo e receber uma notificação quando as condições corresponderem essa regra. Sempre que um tipo de alerta do Azure suportado 'ativa,' ou as condições são cumpridas para gerar uma notificação, um registo da ativação também é emitidos via push para esta categoria de registo de atividades.

### <a name="sample-event"></a>Evento de exemplo

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Descrições das propriedades
| Nome do elemento | Descrição |
| --- | --- |
| chamador | Sempre Microsoft.Insights/alertRules |
| canais | Sempre "Admin, operação" |
| afirmações | Blob JSON com o tipo SPN (nome principal de serviço) ou recurso, de mecanismo de alerta. |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| descrição |Descrição de texto estático do evento de alerta. |
| eventDataId |Identificador exclusivo do evento de alerta. |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Error", "Aviso" e "Informativo" |
| resourceGroupName |Nome do grupo de recursos para o recurso afetado se se trata de um alerta de métrica. Para outros tipos de alerta, é o nome do grupo de recursos que contém o alerta em si. |
| resourceProviderName |Nome do fornecedor de recursos para o recurso afetado se se trata de um alerta de métrica. Para outros tipos de alerta, é o nome do fornecedor de recursos para o alerta em si. |
| resourceId | Nome do ID do recurso para o recurso afetado se se trata de um alerta de métrica. Para outros tipos de alerta, é o ID de recurso do próprio recurso do alerta. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto de `<Key, Value>` pares (ou seja, um dicionário), que descreve os detalhes do evento. |
| status |A cadeia de caracteres que descreve o estado da operação. Alguns valores comuns são: Iniciado, em curso, foi concluída com êxito, falha, Active Directory, resolvido. |
| subStatus | Normalmente, null para alertas. |
| eventTimestamp |Timestamp quando o evento foi gerado pelo processamento do pedido correspondente o evento de serviço do Azure. |
| submissionTimestamp |Timestamp quando o evento se tornou disponível para consulta. |
| subscriptionId |ID de subscrição do Azure. |

### <a name="properties-field-per-alert-type"></a>Campo de propriedades por tipo de alerta
O campo de propriedades irá conter valores diferentes dependendo da origem do evento de alerta. Dois provedores de eventos de alertas comuns são alertas de registo de atividades e alertas de métricas.

#### <a name="properties-for-activity-log-alerts"></a>Propriedades de alertas de registo de atividades
| Nome do elemento | Descrição |
| --- | --- |
| properties.subscriptionId | O ID de subscrição do evento de registo de atividade que causou esta regra de alerta de registo de atividade a ser ativado. |
| properties.eventDataId | O ID de dados de eventos do evento de registo de atividade que causou esta regra de alerta de registo de atividade a ser ativado. |
| properties.resourceGroup | O grupo de recursos do evento de registo de atividade que causou esta regra de alerta de registo de atividade a ser ativado. |
| properties.resourceId | O ID de recurso do evento de registo de atividade que causou esta regra de alerta de registo de atividade a ser ativado. |
| properties.eventTimestamp | O evento carimbo de hora do evento de registo de atividade que causou esta regra de alerta de registo de atividade a ser ativado. |
| properties.operationName | O nome da operação do evento de registo de atividade que causou esta regra de alerta de registo de atividade a ser ativado. |
| properties.status | O estado do evento de registo de atividade que causou esta regra de alerta de registo de atividade a ser ativado.|

#### <a name="properties-for-metric-alerts"></a>Propriedades de alertas de métricas
| Nome do elemento | Descrição |
| --- | --- |
| properties.RuleUri | ID de recurso da regra de alerta de métrica em si. |
| properties.RuleName | O nome da regra de alerta de métrica. |
| properties.RuleDescription | A descrição do que a regra de alerta de métrica (conforme definido na regra de alerta). |
| properties.Threshold | O valor de limiar utilizado na avaliação de regra de alerta de métrica. |
| properties.WindowSizeInMinutes | O tamanho da janela usado na avaliação de regra de alerta de métrica. |
| Propriedades. Agregação | O tipo de agregação definido na regra de alerta de métrica. |
| Propriedades. Operador | O operador condicional utilizado na avaliação de regra de alerta de métrica. |
| properties.MetricName | O nome da métrica da métrica utilizado na avaliação de regra de alerta de métrica. |
| properties.MetricUnit | A unidade de métrica para a métrica utilizada na avaliação de regra de alerta de métrica. |

## <a name="autoscale"></a>Dimensionamento Automático
Esta categoria contém o registo de quaisquer eventos relacionados com a operação do mecanismo de dimensionamento automático com base em quaisquer definições de dimensionamento automático que definiu na sua subscrição. Um exemplo do tipo de evento, que veria nesta categoria é "Aumento do dimensionamento automático a ação falhou." Utilizo o dimensionamento automático, pode automaticamente aumentar ou reduzir horizontalmente o número de instâncias num tipo de recurso suportados com base na hora do dia e/ou carga dados (métricas) através de uma definição de dimensionamento automático. Quando as condições são cumpridas para aumentar ou reduzir verticalmente, o início e foi concluída com êxito ou falhados de eventos serão registados nesta categoria.

### <a name="sample-event"></a>Evento de exemplo
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Descrições das propriedades
| Nome do elemento | Descrição |
| --- | --- |
| chamador | Always Microsoft.Insights/autoscaleSettings |
| canais | Sempre "Admin, operação" |
| afirmações | Blob JSON com o tipo SPN (nome principal de serviço) ou recurso, do motor de dimensionamento automático. |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| descrição |Descrição de texto estático do evento de dimensionamento automático. |
| eventDataId |Identificador exclusivo do evento de dimensionamento automático. |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Error", "Aviso" e "Informativo" |
| resourceGroupName |Nome do grupo de recursos para a definição de dimensionamento automático. |
| resourceProviderName |Nome do fornecedor de recursos para a definição de dimensionamento automático. |
| resourceId |ID de recurso da definição de dimensionamento automático. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto de `<Key, Value>` pares (ou seja, um dicionário), que descreve os detalhes do evento. |
| properties.Description | Descrição detalhada do que estava fazendo o mecanismo de dimensionamento automático. |
| properties.ResourceName | ID de recurso do recurso afetado (o recurso no qual estava a ser efetuada a ação de dimensionamento) |
| properties.OldInstancesCount | O número de instâncias antes da ação de dimensionamento automático demorou efeito. |
| properties.NewInstancesCount | O número de instâncias após a ação de dimensionamento automático demorou efeito. |
| properties.LastScaleActionTime | O carimbo de hora de quando ocorreu a ação de dimensionamento automático. |
| status |A cadeia de caracteres que descreve o estado da operação. Alguns valores comuns são: Iniciado, em curso, foi concluída com êxito, falha, Active Directory, resolvido. |
| subStatus | Normalmente, null para dimensionamento automático. |
| eventTimestamp |Timestamp quando o evento foi gerado pelo processamento do pedido correspondente o evento de serviço do Azure. |
| submissionTimestamp |Timestamp quando o evento se tornou disponível para consulta. |
| subscriptionId |ID de subscrição do Azure. |

## <a name="security"></a>Segurança
Esta categoria contém o registo de todos os alertas gerados pelo centro de segurança do Azure. Um exemplo do tipo de evento, que veria nesta categoria é "ficheiro de extensão dupla suspeito executado".

### <a name="sample-event"></a>Evento de exemplo
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Descrições das propriedades
| Nome do elemento | Descrição |
| --- | --- |
| canais | Sempre "operação" |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| descrição |Descrição de texto estático do evento de segurança. |
| eventDataId |Identificador exclusivo do evento de segurança. |
| eventName |Nome amigável do evento de segurança. |
| ID |Identificador de recurso exclusivo do evento de segurança. |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Error", "Aviso" ou "Informativo" |
| resourceGroupName |Nome do grupo de recursos para o recurso. |
| resourceProviderName |Nome do fornecedor de recursos para o Centro de segurança do Azure. Sempre "Microsoft.Security". |
| resourceType |O tipo de recurso que gerou o evento de segurança, tais como "Microsoft.Security/locations/alerts" |
| resourceId |ID de recurso do alerta de segurança. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto de `<Key, Value>` pares (ou seja, um dicionário), que descreve os detalhes do evento. Estas propriedades irão variar consoante o tipo de alerta de segurança. Ver [esta página](../../security-center/security-center-alerts-type.md) para obter uma descrição dos tipos de alertas do Centro de segurança. |
| Propriedades. Gravidade |O nível de gravidade. Os valores possíveis são "Alta", "Média" ou "Baixa". |
| status |A cadeia de caracteres que descreve o estado da operação. Alguns valores comuns são: Iniciado, em curso, foi concluída com êxito, falha, Active Directory, resolvido. |
| subStatus | Normalmente, null para eventos de segurança. |
| eventTimestamp |Timestamp quando o evento foi gerado pelo processamento do pedido correspondente o evento de serviço do Azure. |
| submissionTimestamp |Timestamp quando o evento se tornou disponível para consulta. |
| subscriptionId |ID de subscrição do Azure. |

## <a name="recommendation"></a>Recomendação
Esta categoria contém o registo de quaisquer novas recomendações que são gerados para os seus serviços. Um exemplo de uma recomendação seria "conjuntos de disponibilidade de utilização para tolerância de falhas melhorada." Existem quatro tipos de eventos de recomendação que podem ser gerados: Elevada disponibilidade, desempenho, segurança e otimização de custo. 

### <a name="sample-event"></a>Evento de exemplo
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Descrições das propriedades
| Nome do elemento | Descrição |
| --- | --- |
| canais | Sempre "operação" |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| descrição |Descrição de texto estático do evento de recomendação |
| eventDataId | Identificador exclusivo do evento de recomendação. |
| categoria | Sempre "recomendação" |
| ID |Identificador de recurso exclusivo do evento de recomendação. |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Error", "Aviso" ou "Informativo" |
| operationName |Nome da operação.  Sempre "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |Nome do grupo de recursos para o recurso. |
| resourceProviderName |Nome do fornecedor de recursos para o recurso que esta recomendação aplica-se, por exemplo, "Microsoft. Compute" |
| resourceType |Nome do tipo de recurso para o recurso que esta recomendação aplica-se, por exemplo, "MICROSOFT.COMPUTE/virtualmachines" |
| resourceId |ID de recurso do recurso que a recomendação aplica-se a |
| status | Sempre ativo"" |
| submissionTimestamp |Timestamp quando o evento se tornou disponível para consulta. |
| subscriptionId |ID de subscrição do Azure. |
| propriedades |Conjunto de `<Key, Value>` pares (ou seja, um dicionário), que descreve os detalhes da recomendação.|
| properties.recommendationSchemaVersion| Versão do esquema das propriedades da recomendação publicado na entrada de registo de atividades |
| properties.recommendationCategory | Categoria da recomendação. Os valores possíveis são "Alta disponibilidade", "Desempenho", "Segurança" e "Custo" |
| properties.recommendationImpact| Impacto da recomendação. Os valores possíveis são "Alta", "Medium", "Baixa" |
| properties.recommendationRisk| Risco da recomendação. Os valores possíveis são "Error", "Aviso", "None" |

## <a name="policy"></a>Política

Esta categoria contém registos de todas as operações de ação de efeito realizadas por [do Azure Policy](../../governance/policy/overview.md). Os exemplos dos tipos de eventos que veria nesta categoria incluem _auditoria_ e _negar_. Cada ação tomada pela política é modelada como uma operação num recurso.

### <a name="sample-policy-event"></a>Evento de diretiva de exemplo

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Descrições de propriedade de evento de política

| Nome do elemento | Descrição |
| --- | --- |
| Autorização | Matriz de propriedades RBAC do evento. Para novos recursos, esta é a ação e o âmbito do pedido que disparou a avaliação. Para obter recursos existentes, a ação é "Microsoft.Resources/checkPolicyCompliance/read". |
| chamador | Para novos recursos, a identidade que iniciou uma implementação. Para os recursos existentes, o GUID da RP de informações de política do Microsoft Azure. |
| canais | Eventos de política utilizam apenas o canal de "Operação". |
| afirmações | O token JWT utilizado pelo Active Directory para autenticar o utilizador ou aplicação para efetuar esta operação no Resource Manager. |
| correlationId | Normalmente, um GUID no formato de cadeia de caracteres. Eventos que partilham uma correlationId pertencem à mesma ação de uber. |
| descrição | Este campo está em branco para eventos de política. |
| eventDataId | Identificador exclusivo de um evento. |
| eventName | "BeginRequest" ou "EndRequest". "BeginRequest" é utilizado para as avaliações auditIfNotExists e deployIfNotExists atrasadas e quando um efeito de deployIfNotExists é iniciada uma implementação do modelo. Todas as outras operações devolvem "EndRequest". |
| categoria | Declara o registo de eventos de atividade como pertencentes ao "Policy". |
| eventTimestamp | Timestamp quando o evento foi gerado pelo processamento do pedido correspondente o evento de serviço do Azure. |
| ID | Identificador exclusivo do evento no recurso específico. |
| nível | Nível do evento. Auditoria utiliza "Aviso" e negar utiliza "Erro". Um erro de auditIfNotExists ou deployIfNotExists pode gerar "Aviso" ou "Erro", dependendo da gravidade. Todos os outros eventos de política utilizam "Informativo". |
| operationId | Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName | Nome da operação e diretamente está correlacionada com o efeito de política. |
| resourceGroupName | Nome do grupo de recursos para o recurso avaliado. |
| resourceProviderName | Nome do fornecedor de recursos para o recurso avaliado. |
| resourceType | Para novos recursos, é o tipo a ser avaliado. Para obter recursos existentes, devolve "Microsoft.Resources/checkPolicyCompliance". |
| resourceId | ID de recurso do recurso avaliado. |
| status | A cadeia de caracteres que descreve o estado do resultado da avaliação da política. A maioria das avaliações de política retornam "Com êxito", mas um efeito de recusa devolve "Falhado". Erros no auditIfNotExists ou deployIfNotExists também retornam "Falhado". |
| subStatus | O campo está em branco, eventos de políticas. |
| submissionTimestamp | Timestamp quando o evento se tornou disponível para consulta. |
| subscriptionId | ID de subscrição do Azure. |
| properties.isComplianceCheck | Devolve "False" quando um novo recurso está implementado ou propriedades do Gestor de recursos de um recurso existente são atualizadas. Todos os outros [acionadores de avaliação](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) resultar em "True". |
| properties.resourceLocation | A região do Azure do recurso a ser avaliada. |
| Properties.ancestors | Pediu uma lista separada por vírgulas dos grupos de gestão principal do principal direto para avô mais distante. |
| Properties.Policies | Inclui detalhes sobre a definição de política, atribuição, em vigor e parâmetros que essa avaliação de política é o resultado do. |
| relatedEvents | Este campo está em branco para eventos de política. |

## <a name="mapping-to-diagnostic-logs-schema"></a>Mapeamento de esquema de registos de diagnóstico

Quando o registo de atividades do Azure para uma conta de armazenamento ou o espaço de nomes de Hubs de eventos de transmissão em fluxo, os dados seguem os [esquema de registos de diagnóstico do Azure](./diagnostic-logs-schema.md). Este é o mapeamento das propriedades do esquema acima para o esquema de registos de diagnóstico:

| Propriedade de esquema de registos de diagnóstico | Propriedade de esquema da API REST do Log de atividade | Notas |
| --- | --- | --- |
| hora | eventTimestamp |  |
| resourceId | resourceId | subscriptionId, resourceType, resourceGroupName são inferidos do resourceId. |
| operationName | operationName.value |  |
| categoria | Parte do nome da operação | Abertas do tipo de operação - "Escrever" / "eliminar" / "Action" |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | descrição |  |
| durationMs | N/A | Sempre 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identidade | Propriedades de autorização e afirmações |  |
| Nível | Nível |  |
| localização | N/A | Localização de onde o evento foi processado. *Esta não é a localização do recurso, mas em vez disso, em que o evento foi processado. Esta propriedade será removida numa atualização futura.* |
| Propriedades | properties.eventProperties |  |
| properties.eventCategory | categoria | Se properties.eventCategory não estiver presente, a categoria é "Administração" |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | propriedades |  |


## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre o registo de Atividades (anteriormente conhecido como registos de auditoria)](../../azure-monitor/platform/activity-logs-overview.md)
* [O registo de atividades do Azure para os Hubs de eventos do Stream](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)

