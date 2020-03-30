---
title: Esquema de evento de log de atividade azure
description: Descreve o esquema do evento para cada categoria no registo da Atividade Azure.
author: bwren
services: azure-monitor
ms.topic: reference
ms.date: 12/04/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c2f171c79423e0cfe8b57c05b8248679f9ada9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472746"
---
# <a name="azure-activity-log-event-schema"></a>Esquema de evento de log de atividade azure
O [registo da Atividade Azure](platform-logs-overview.md) fornece informações sobre quaisquer eventos de nível de subscrição que tenham ocorrido em Azure. Este artigo descreve o esquema do evento para cada categoria. 

Os exemplos abaixo mostram o esquema quando acede ao registo de Atividade sacerdote, PowerShell, CLI e REST API. O esquema é diferente quando transmite o registo de atividade para armazenamento ou Centros de [Eventos](resource-logs-stream-event-hubs.md). Um mapeamento das propriedades para o esquema de [registos](diagnostic-logs-schema.md) de recursos é fornecido no final do artigo.

## <a name="administrative"></a>Administrativa
Esta categoria contém o registo de todas as operações de criação, atualização, eliminação e ação realizadas através do Gestor de Recursos. Exemplos dos tipos de eventos que você veria nesta categoria incluem "criar máquina virtual" e "eliminar grupo de segurança de rede" Cada ação tomada por um utilizador ou aplicação usando O Gestor de Recursos é modelada como uma operação num determinado tipo de recurso. Se o tipo de operação for Write, Delete ou Action, os registos do início e do sucesso ou da falha dessa operação são registados na categoria Administrativa. A categoria Administrativa inclui igualmente quaisquer alterações ao controlo de acesso baseado seletiva numa subscrição.

### <a name="sample-event"></a>Evento de amostra
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
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.microsoft.com/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
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

### <a name="property-descriptions"></a>Descrições de propriedades
| Nome do Elemento | Descrição |
| --- | --- |
| autorização |Blob das propriedades RBAC do evento. Normalmente inclui as propriedades de "ação", "papel" e "âmbito". |
| chamador |Endereço de e-mail do utilizador que realizou a operação, reivindicação UPN ou reclamação SPN com base na disponibilidade. |
| canais |Um dos seguintes valores: "Administrador", "Operação" |
| reivindicações |O símbolo JWT utilizado pelo Ative Directory para autenticar o utilizador ou aplicação para realizar esta operação no Gestor de Recursos. |
| correlationId |Normalmente um GUID no formato de cordas. Eventos que partilham uma correlação Deide pertencem à mesma ação uber. |
| descrição |Descrição de texto estático de um evento. |
| eventoDataId |Identificador único de um evento. |
| nome de evento | Nome amigável do evento administrativo. |
| categoria | Sempre "Administrativo" |
| httpRequest |Blob descrevendo o Pedido de Http. Normalmente inclui o "clientRequestId", "clientIpAddress" e "method" (método HTTP. Por exemplo, PUT). |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Erro", "Aviso" e "Informacional" |
| resourceGroupName |Nome do grupo de recursos para o recurso impactado. |
| nome fornecedor de recursos |Nome do fornecedor de recursos para o recurso impactado |
| resourceType | O tipo de recurso que foi afetado por um evento administrativo. |
| resourceId |Identificação de recursos do recurso impactado. |
| operationId |Um GUID partilhou entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto `<Key, Value>` de pares (isto é, um Dicionário) descrevendo os detalhes do evento. |
| status |Cordas descrevendo o estado da operação. Alguns valores comuns são: Iniciado, em progresso, bem sucedido, falhado, ativo, resolvido. |
| subEstatuto |Normalmente, o código de estado HTTP da chamada REPOUSA correspondente, mas também pode incluir outras cordas descrevendo um subestatuto, tais como estes valores comuns: OK (Código de Estado HTTP: 200), Criado (Código de Estado HTTP: 201), Aceito (Código de Estado HTTP: 202), Sem Conteúdo (Http Status: 200), Código: 204), Pedido De Mau Pedido (Código de Estado HTTP: 400), Não Encontrado (Código de Estado HTTP: 404), Conflito (Código de Estado HTTP: 409), Erro do Servidor Interno (Código de Estado HTTP: 500), Serviço Indisponível (Código de Estado HTTP: 503), Timeout gateway (Código de Estado HTTP: 504). |
| eventoTimestamp |Carimbo de tempo quando o evento foi gerado pelo serviço Azure processando o pedido correspondente ao evento. |
| submissãoTimestamp |Hora do tempo quando o evento ficou disponível para consulta. |
| subscriptionId |ID de subscrição azure. |

## <a name="service-health"></a>Estado de funcionamento dos serviços
Esta categoria contém o registo de quaisquer incidentes de saúde de serviço ocorridos em Azure. Um exemplo do tipo de evento que você veria nesta categoria é "SQL Azure no Leste dos EUA está experimentando tempo de inatividade." Os eventos de saúde de serviço vêm em cinco variedades: Ação Necessária, Recuperação Assistida, Incidente, Manutenção, Informação ou Segurança, e só aparecem se tiver um recurso na subscrição que seria impactado pelo evento.

### <a name="sample-event"></a>Evento de amostra
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
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Consulte o artigo de notificações de [saúde](./../../azure-monitor/platform/service-notifications.md) de serviço para documentação sobre os valores dos imóveis.

## <a name="resource-health"></a>Estado de funcionamento de recursos
Esta categoria contém o registo de quaisquer eventos de saúde de recursos que tenham ocorrido aos seus recursos Azure. Um exemplo do tipo de evento que você veria nesta categoria é "Estado de saúde da Máquina Virtual alterado para indisponível." Os eventos de saúde de recursos podem representar um dos quatro estados de saúde: Disponível, Indisponível, Degradado e Desconhecido. Além disso, os eventos de saúde de recursos podem ser categorizados como sendo iniciados pela Plataforma ou Iniciados pelo Utilizador.

### <a name="sample-event"></a>Evento de amostra

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
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
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
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
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

### <a name="property-descriptions"></a>Descrições de propriedades
| Nome do Elemento | Descrição |
| --- | --- |
| canais | Sempre "Administrador, Operação" |
| correlationId | Um GUID no formato de cordas. |
| descrição |Descrição estática do texto do evento de alerta. |
| eventoDataId |Identificador único do evento de alerta. |
| categoria | Sempre "ResourceHealth" |
| eventoTimestamp |Carimbo de tempo quando o evento foi gerado pelo serviço Azure processando o pedido correspondente ao evento. |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Erro", "Aviso", "Informacional" e "Verbose" |
| operationId |Um GUID partilhou entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| resourceGroupName |Nome do grupo de recursos que contém o recurso. |
| nome fornecedor de recursos |Sempre "Microsoft.Resourcehealth/healthevent/action". |
| resourceType | O tipo de recurso que foi afetado por um evento de Saúde de Recursos. |
| resourceId | Nome do ID de recurso para o recurso impactado. |
| status |Cordas descrevendo o estado do evento de saúde. Os valores podem ser: Ativo, Resolvido, InProgress, Atualizado. |
| subEstatuto | Normalmente nulo para alertas. |
| submissãoTimestamp |Hora do tempo quando o evento ficou disponível para consulta. |
| subscriptionId |ID de subscrição azure. |
| propriedades |Conjunto `<Key, Value>` de pares (isto é, um Dicionário) descrevendo os detalhes do evento.|
| propriedades.título | Uma cadeia que descreve o estado de saúde do recurso. |
| propriedades.detalhes | Uma cadeia fácil de usar que descreve mais detalhes sobre o evento. |
| propriedades.currentHealthStatus | O estado de saúde atual do recurso. Um dos seguintes valores: "Disponível", "Indisponível", "Degradado" e "Desconhecido". |
| propriedades.anteriorHealthStatus | O estado de saúde anterior do recurso. Um dos seguintes valores: "Disponível", "Indisponível", "Degradado" e "Desconhecido". |
| propriedades.tipo | Uma descrição do tipo de evento de saúde de recursos. |
| propriedades.causa | Uma descrição da causa do evento de saúde dos recursos. Ou "UserIniciadod" e "PlatformIniciado". |


## <a name="alert"></a>Alerta
Esta categoria contém o registo de todas as ativações de alertas Azure. Um exemplo do tipo de evento que se veria nesta categoria é "cpU % no myVM foi superior a 80 nos últimos 5 minutos." Uma variedade de sistemas Azure têm um conceito de alerta - você pode definir uma regra de algum tipo e receber uma notificação quando as condições correspondem a essa regra. Sempre que um tipo de alerta Azure suportado 'ativa', ou as condições são satisfeitas para gerar uma notificação, um registo da ativação também é empurrado para esta categoria do Registo de Atividade.

### <a name="sample-event"></a>Evento de amostra

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

### <a name="property-descriptions"></a>Descrições de propriedades
| Nome do Elemento | Descrição |
| --- | --- |
| chamador | Sempre Microsoft.Insights/alertRules |
| canais | Sempre "Administrador, Operação" |
| reivindicações | JSON blob com o SPN (nome principal de serviço), ou tipo de recurso, do motor de alerta. |
| correlationId | Um GUID no formato de cordas. |
| descrição |Descrição estática do texto do evento de alerta. |
| eventoDataId |Identificador único do evento de alerta. |
| categoria | Sempre "Alerta" |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Erro", "Aviso" e "Informacional" |
| resourceGroupName |Nome do grupo de recursos para o recurso impactado se for um alerta métrico. Para outros tipos de alerta, é o nome do grupo de recursos que contém o próprio alerta. |
| nome fornecedor de recursos |Nome do fornecedor de recursos para o recurso impactado se for um alerta métrico. Para outros tipos de alerta, é o nome do fornecedor de recursos para o próprio alerta. |
| resourceId | Nome do ID do recurso para o recurso impactado se for um alerta métrico. Para outros tipos de alerta, é a identificação de recursos do próprio recurso de alerta. |
| operationId |Um GUID partilhou entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto `<Key, Value>` de pares (isto é, um Dicionário) descrevendo os detalhes do evento. |
| status |Cordas descrevendo o estado da operação. Alguns valores comuns são: Iniciado, em progresso, bem sucedido, falhado, ativo, resolvido. |
| subEstatuto | Normalmente nulo para alertas. |
| eventoTimestamp |Carimbo de tempo quando o evento foi gerado pelo serviço Azure processando o pedido correspondente ao evento. |
| submissãoTimestamp |Hora do tempo quando o evento ficou disponível para consulta. |
| subscriptionId |ID de subscrição azure. |

### <a name="properties-field-per-alert-type"></a>Campo de propriedades por tipo de alerta
O campo de propriedades conterá valores diferentes dependendo da origem do evento de alerta. Dois fornecedores comuns de eventos de alerta são alertas de Registo de Atividades e alertas métricos.

#### <a name="properties-for-activity-log-alerts"></a>Propriedades para alertas de Registo de Atividades
| Nome do Elemento | Descrição |
| --- | --- |
| propriedades.subscriçãoId | O ID de subscrição do evento de registo de atividade que fez com que esta regra de alerta de registo de atividade fosse ativado. |
| propriedades.eventDataId | O ID de dados do evento do evento de registo de atividade que fez com que esta regra de alerta de registo de atividade fosse ativado. |
| propriedades.recursosGroup | O grupo de recursos do evento de registo de atividade que fez com que esta regra de alerta de registo de atividade fosse ativada. |
| propriedades.resourceId | O ID de recurso do evento de registo de atividade que fez com que esta regra de alerta de registo de atividade fosse ativado. |
| propriedades.eventTimestamp | O carimbo temporal do evento de registo de atividade que fez com que esta regra de alerta de registo de atividade fosse ativada. |
| propriedades.operationName | O nome de funcionamento do evento de registo de atividade que fez com que esta regra de alerta de registo de atividade fosse ativado. |
| propriedades.status | O estado do evento de registo de atividade que fez com que esta regra de alerta de registo de atividade fosse ativado.|

#### <a name="properties-for-metric-alerts"></a>Propriedades para alertas métricos
| Nome do Elemento | Descrição |
| --- | --- |
| propriedades. Ruleuri | Identificação de recursos da regra de alerta métrico em si. |
| propriedades. Nome de regras | O nome da regra de alerta métrico. |
| propriedades. Descrição da regra | A descrição da regra de alerta métrico (conforme definido na regra de alerta). |
| propriedades. Limiar | O valor-limiar utilizado na avaliação da regra de alerta métrico. |
| propriedades. WindowSizeInMinutes | O tamanho da janela utilizado na avaliação da regra de alerta métrico. |
| propriedades. Agregação | O tipo de agregação definido na regra de alerta métrico. |
| propriedades. Operador | O operador condicional utilizado na avaliação da regra de alerta métrico. |
| propriedades. Nome métrico | O nome métrico da métrica utilizada na avaliação da regra de alerta métrico. |
| propriedades. Unidade Métrica | A unidade métrica para a métrica utilizada na avaliação da regra de alerta métrico. |

## <a name="autoscale"></a>Dimensionamento Automático
Esta categoria contém o registo de quaisquer eventos relacionados com o funcionamento do motor de escala automática com base em quaisquer definições de escala automática que tenha definido na sua subscrição. Um exemplo do tipo de evento que você veria nesta categoria é "A ação de escala automática falhou." Utilizando a escala automática, pode automaticamente escalar ou escalar o número de instâncias num tipo de recurso suportado baseado na hora do dia e/ou dados de carga (métrica) utilizando uma definição de escala automática. Quando as condições forem satisfeitas para escalar para cima ou para baixo, os eventos de início e sucesso ou falhados serão registados nesta categoria.

### <a name="sample-event"></a>Evento de amostra
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

### <a name="property-descriptions"></a>Descrições de propriedades
| Nome do Elemento | Descrição |
| --- | --- |
| chamador | Sempre Microsoft.Insights/autoscaleSettings |
| canais | Sempre "Administrador, Operação" |
| reivindicações | JSON blob com o SPN (nome principal de serviço), ou tipo de recurso, do motor de escala automática. |
| correlationId | Um GUID no formato de cordas. |
| descrição |Descrição estática do texto do evento de escala automática. |
| eventoDataId |Identificador único do evento de escala automática. |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Erro", "Aviso" e "Informacional" |
| resourceGroupName |Nome do grupo de recursos para a definição de escala automática. |
| nome fornecedor de recursos |Nome do fornecedor de recursos para a definição de escala automática. |
| resourceId |Identificação do recurso da definição de escala automática. |
| operationId |Um GUID partilhou entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto `<Key, Value>` de pares (isto é, um Dicionário) descrevendo os detalhes do evento. |
| propriedades. Descrição | Descrição detalhada do que o motor de escala automática estava a fazer. |
| propriedades. Nome dos Recursos | Identificação do recurso impactado (o recurso em que estava a ser realizada a ação de escala) |
| propriedades. OldInstancesCount | O número de casos antes da ação de escala automática entrou em vigor. |
| propriedades. NewInstancesCount | O número de casos após a ação de escala automática entrou em vigor. |
| propriedades. LastScaleActionTime | O carimbo de quando ocorreu a ação à escala automática. |
| status |Cordas descrevendo o estado da operação. Alguns valores comuns são: Iniciado, em progresso, bem sucedido, falhado, ativo, resolvido. |
| subEstatuto | Normalmente nulo para escala automática. |
| eventoTimestamp |Carimbo de tempo quando o evento foi gerado pelo serviço Azure processando o pedido correspondente ao evento. |
| submissãoTimestamp |Hora do tempo quando o evento ficou disponível para consulta. |
| subscriptionId |ID de subscrição azure. |

## <a name="security"></a>Segurança
Esta categoria contém o registo de quaisquer alertas gerados pelo Azure Security Center. Um exemplo do tipo de evento que você veria nesta categoria é "Ficheiro de extensão dupla suspeita executado."

### <a name="sample-event"></a>Evento de amostra
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

### <a name="property-descriptions"></a>Descrições de propriedades
| Nome do Elemento | Descrição |
| --- | --- |
| canais | Sempre "Operação" |
| correlationId | Um GUID no formato de cordas. |
| descrição |Descrição estática do texto do evento de segurança. |
| eventoDataId |Identificador único do evento de segurança. |
| nome de evento |Nome amigável do evento de segurança. |
| categoria | Sempre "Segurança" |
| ID |Identificador de recursos exclusivo do evento de segurança. |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Erro", "Aviso" ou "Informacional" |
| resourceGroupName |Nome do grupo de recursos para o recurso. |
| nome fornecedor de recursos |Nome do fornecedor de recursos para o Azure Security Center. Sempre "Microsoft.Security". |
| resourceType |O tipo de recurso que gerou o evento de segurança, como "Microsoft.Security/locations/alerts" |
| resourceId |Identificação do recurso do alerta de segurança. |
| operationId |Um GUID partilhou entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto `<Key, Value>` de pares (isto é, um Dicionário) descrevendo os detalhes do evento. Estas propriedades variarão dependendo do tipo de alerta de segurança. Consulte [esta página](../../security-center/security-center-alerts-overview.md) para obter uma descrição dos tipos de alertas que vêm do Centro de Segurança. |
| propriedades. Gravidade |O nível de gravidade. Os valores possíveis são "Alto", "Médio" ou "Baixo". |
| status |Cordas descrevendo o estado da operação. Alguns valores comuns são: Iniciado, em progresso, bem sucedido, falhado, ativo, resolvido. |
| subEstatuto | Normalmente nulo para eventos de segurança. |
| eventoTimestamp |Carimbo de tempo quando o evento foi gerado pelo serviço Azure processando o pedido correspondente ao evento. |
| submissãoTimestamp |Hora do tempo quando o evento ficou disponível para consulta. |
| subscriptionId |ID de subscrição azure. |

## <a name="recommendation"></a>Recomendação
Esta categoria contém o registo de quaisquer novas recomendações geradas para os seus serviços. Um exemplo de uma recomendação seria "Utilizar conjuntos de disponibilidade para uma melhor tolerância à falha." Existem quatro tipos de eventos de Recomendação que podem ser gerados: Alta Disponibilidade, Desempenho, Segurança e Otimização de Custos. 

### <a name="sample-event"></a>Evento de amostra
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
### <a name="property-descriptions"></a>Descrições de propriedades
| Nome do Elemento | Descrição |
| --- | --- |
| canais | Sempre "Operação" |
| correlationId | Um GUID no formato de cordas. |
| descrição |Descrição do texto estático do evento de recomendação |
| eventoDataId | Identificador único do evento de recomendação. |
| categoria | Sempre "Recomendação" |
| ID |Identificador de recursos exclusivo do evento de recomendação. |
| nível |Nível do evento. Um dos seguintes valores: "Crítico", "Erro", "Aviso" ou "Informacional" |
| operationName |Nome da operação.  Sempre "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |Nome do grupo de recursos para o recurso. |
| nome fornecedor de recursos |Nome do fornecedor de recursos para o recurso a que esta recomendação se aplica, como "MICROSOFT.COMPUTE" |
| resourceType |Nome do tipo de recurso para o recurso a que esta recomendação se aplica, como "MICROSOFT.COMPUTE/virtualmachines" |
| resourceId |Identificação de recursos do recurso a que a recomendação se aplica |
| status | Sempre "Ativo" |
| submissãoTimestamp |Hora do tempo quando o evento ficou disponível para consulta. |
| subscriptionId |ID de subscrição azure. |
| propriedades |Conjunto `<Key, Value>` de pares (isto é, um Dicionário) descrevendo os detalhes da recomendação.|
| propriedades.recommendationSchemaVersion| Versão schema das propriedades de recomendação publicadas na entrada do Registo de Atividades |
| propriedades.recomendaçãoCategoria | Categoria da recomendação. Os valores possíveis são "Alta Disponibilidade", "Desempenho", "Segurança" e "Custo" |
| propriedades.recomendaimpacto| Impacto da recomendação. Os valores possíveis são "Altos", "Médios", "Baixos" |
| propriedades.recomendaçãoRisco| Risco da recomendação. Os valores possíveis são "Erro", "Aviso", "Nenhum" |

## <a name="policy"></a>Política

Esta categoria contém registos de todas as operações de ação de efeito realizadas pela [Política Azure.](../../governance/policy/overview.md) Exemplos dos tipos de eventos que você veria nesta categoria incluem _Auditoria_ e _Deny_. Todas as medidas tomadas pela Policy são modeladas como uma operação sobre um recurso.

### <a name="sample-policy-event"></a>Evento política de amostras

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

### <a name="policy-event-property-descriptions"></a>Descrições de propriedade de eventos de política

| Nome do Elemento | Descrição |
| --- | --- |
| autorização | Conjunto de propriedades RBAC do evento. Para novos recursos, esta é a ação e o âmbito do pedido que desencadeou a avaliação. Para os recursos existentes, a ação é "Microsoft.Resources/checkPolicyCompliance/read". |
| chamador | Para novos recursos, a identidade que iniciou uma implantação. Para os recursos existentes, o GUID do Microsoft Azure Policy Insights RP. |
| canais | Os eventos políticos usam apenas o canal "Operação". |
| reivindicações | O símbolo JWT utilizado pelo Ative Directory para autenticar o utilizador ou aplicação para realizar esta operação no Gestor de Recursos. |
| correlationId | Normalmente um GUID no formato de cordas. Eventos que partilham uma correlação Deide pertencem à mesma ação uber. |
| descrição | Este campo está em branco para eventos políticos. |
| eventoDataId | Identificador único de um evento. |
| nome de evento | Ou "Iniciar Pedido" ou "EndRequest". "StartRequest" é utilizado para auditorias atrasadasIfNotExists e implementam avaliações IfNotExists e quando um efeito implementadoIfNotExists inicia uma implementação do modelo. Todas as outras operações devolvem "EndRequest". |
| categoria | Declara o evento de registo de atividades como pertencente à "Política". |
| eventoTimestamp | Carimbo de tempo quando o evento foi gerado pelo serviço Azure processando o pedido correspondente ao evento. |
| ID | Identificador único do evento sobre o recurso específico. |
| nível | Nível do evento. A auditoria utiliza "Aviso" e a Deny utiliza "Error". Uma auditoriaIfNotExists ou implementaErroIfNotExists erro pode gerar "Aviso" ou "Erro" dependendo da gravidade. Todos os outros eventos de política usam "Informational". |
| operationId | Um GUID partilhou entre os eventos que correspondem a uma única operação. |
| operationName | O nome da operação e correlaciona diretamente com o efeito Política. |
| resourceGroupName | Nome do grupo de recursos para o recurso avaliado. |
| nome fornecedor de recursos | Nome do fornecedor de recursos para o recurso avaliado. |
| resourceType | Para novos recursos, é o tipo que está a ser avaliado. Para os recursos existentes, devolve "Microsoft.Resources/checkPolicyCompliance". |
| resourceId | Identificação de recursos do recurso avaliado. |
| status | Cadeia descrevendo o estado do resultado da avaliação política. A maioria das avaliações políticas regressam "Bem sucedidas", mas um efeito Deny devolve "Falhado". Erros na auditoriaIfNotExists ou implementaIfNotExists também retornam "Falhado". |
| subEstatuto | O campo está em branco para eventos políticos. |
| submissãoTimestamp | Hora do tempo quando o evento ficou disponível para consulta. |
| subscriptionId | ID de subscrição azure. |
| propriedades.isComplianceCheck | Devoluções "Falsas" quando um novo recurso é implantado ou as propriedades do Gestor de Recursos existentes são atualizadas. Todos os outros [gatilhos de avaliação](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) resultam em "True". |
| propriedades.recursosLocalização | A região azure do recurso que está a ser avaliado. |
| propriedades.ancestrais | Uma lista separada de grupos de gestão de pais ordenados de pais diretos para avós mais distantes. |
| propriedades.políticas | Inclui detalhes sobre a definição de política, atribuição, efeito e parâmetros de que esta avaliação política é resultado. |
| relacionadoSEventos | Este campo está em branco para eventos políticos. |


## <a name="schema-from-storage-account-and-event-hubs"></a>Schema da conta de armazenamento e centros de eventos
Ao transmitir o registo da Atividade Azure para uma conta de armazenamento ou centro de eventos, os dados seguem o esquema de registo de [recursos](diagnostic-logs-schema.md). A tabela abaixo fornece um mapeamento de propriedades desde o esquema acima até ao esquema de registos de recursos.

> [!IMPORTANT]
> O formato de dados de registo de Atividades escritos numa conta de armazenamento alterado para JSON Lines em 1 de novembro de 2018. Consulte a Preparação para a alteração do [formato para os registos de recursos do Monitor Azure arquivados numa conta](diagnostic-logs-append-blobs.md) de armazenamento para obter detalhes sobre esta alteração de formato.


| Propriedade de esquemade registos de recursos | Propriedade de esquema saema do Log de Atividades | Notas |
| --- | --- | --- |
| hora | eventoTimestamp |  |
| resourceId | resourceId | subscriçãoId, recursoType, resourceGroupName são todos inferidos do recursoId. |
| operationName | operaçãoNome.valor |  |
| categoria | Parte do nome da operação | Fuga do tipo de operação - "Escrever"/"Excluir"/"Ação" |
| resultType | estado.valor | |
| resultSignature | subestatuto.valor | |
| resultDescription | descrição |  |
| durationMs | N/D | Sempre 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identidade | sinistros e propriedades de autorização |  |
| Nível | Nível |  |
| localização | N/D | Localização do local onde o evento foi processado. *Esta não é a localização do recurso, mas sim onde o evento foi processado. Esta propriedade será removida numa futura atualização.* |
| Propriedades | propriedades.eventProperties |  |
| propriedades.eventCategoria | categoria | Se os imóveis.eventCategoria não estiver presente, a categoria é "Administrativa" |
| propriedades.eventName | nome de evento |  |
| propriedades.operationId | operationId |  |
| propriedades.eventProperties | propriedades |  |

Segue-se um exemplo de um evento que usa este esquema.

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```





## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre o Registo de Atividades](platform-logs-overview.md)
* [Criar uma definição de diagnóstico para enviar Registo de Atividade sonâmlo para log analytics espaço de trabalho, armazenamento Azure ou centros de eventos](diagnostic-settings.md)

