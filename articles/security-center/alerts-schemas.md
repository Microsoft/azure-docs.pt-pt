---
title: Schemas para o Centro de Segurança Azure alertas
description: Este artigo descreve os diferentes esquemas usados pelo Azure Security Center para alertas de segurança.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 55f8d37d435aa8adeb4d97246ce7b2c7811140be
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558003"
---
# <a name="security-alerts-schemas"></a>Esquemas de alerta de segurança

Se a sua subscrição tiver o Azure Defender ativado, receberá alertas de segurança quando o Centro de Segurança detetar ameaças aos seus recursos.

Pode ver estes alertas de segurança nas páginas de **Proteção** de Ameaças do Centro de Segurança Azure ou através de ferramentas externas como:

- [Azure Sentinel](../sentinel/index.yml) - SIEM nativo da Nuvem da Microsoft. O Sentinel Connector recebe alertas do Azure Security Center e envia-os para o [espaço de trabalho log Analytics](../azure-monitor/logs/quick-create-workspace.md) para o Azure Sentinel.
- SIEMs de terceiros - Envie dados para [Azure Event Hubs](../event-hubs/index.yml). Em seguida, integre os seus dados do Event Hub com um SIEM de terceiros. Saiba mais em [alertas stream para uma solução SIEM, SOAR ou IT Service Management.](export-to-siem.md)
- [A API REST](/rest/api/securitycenter/) - Se estiver a utilizar a API REST para aceder a alertas, consulte a [documentação da API alertas online](/rest/api/securitycenter/alerts).

Se estiver a utilizar métodos programáticos para consumir os alertas, precisará do esquema correto para encontrar os campos que são relevantes para si. Além disso, se estiver a exportar para um Centro de Eventos ou a tentar ativar a Automatização do Fluxo de Trabalho com conectores HTTP genéricos, utilize os esquemas para analisar corretamente os objetos JSON.

>[!IMPORTANT]
> O esquema é ligeiramente diferente para cada um destes cenários, por isso certifique-se de selecionar o separador relevante abaixo.


## <a name="the-schemas"></a>Os esquemas 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Automação do fluxo de trabalho e exportação contínua para o Centro de Eventos](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Amostra JSON para alertas enviados para Apps Lógicas, Centro de Eventos e SIEMs de terceiros

Abaixo você encontrará o esquema dos eventos de alerta passados para:

- Instâncias da Azure Logic App que foram configuradas na automatização do fluxo de trabalho do Security Center
- Azure Event Hub usando a funcionalidade de exportação contínua do Security Center

Para obter mais informações sobre a função de automatização do fluxo de trabalho, consulte [as respostas do Automatizar para os gatilhos do Centro de Segurança.](workflow-automation.md)

Para obter mais informações sobre a exportação contínua, consulte [os dados do Centro de Segurança de exportação contínua](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Espaços de trabalho Azure Sentinel e Log Analytics](#tab/schema-sentinel)

O Connector Sentinel recebe alertas do Azure Security Center e envia-os para o Log Analytics Workspace para O Azure Sentinel. 

Para criar um caso Sentinel ou incidente usando alertas do Centro de Segurança, você precisará do esquema para os alertas mostrados abaixo. 

Para mais informações sobre a Azure Sentinel, consulte [a documentação.](../sentinel/index.yml)

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Registo de Atividades do Azure](#tab/schema-activitylog)

As auditorias do Azure Security Center geraram alertas de segurança como eventos no Registo de Atividades do Azure.

Pode ver os eventos de alerta de segurança no Registo de Atividades, procurando o evento De Alerta Ativado como mostrado:

[![Pesquisando o registo de atividade para o evento De Alerta Ativado](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Amostra JSON para alertas enviados para Registo de Atividades Azure

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

|Campo|Descrição|
|----|----|
|**canais**|Constante, "Operação"|
|**correlationId**|O alerta do Centro de Segurança Azure ID|
|**descrição**|Descrição do alerta|
|**eventDataId**|Ver correlationId|
|**nome de evento**|Os subcampos de valor e de Valor Localizados contêm o nome de exibição de alerta|
|**categoria**|Os subcampos de valor e de valor localizados são constantes - "Segurança"|
|**eventTimestamp**|Calendário utc para quando o alerta foi gerado|
|**id**|O ID de alerta totalmente qualificado|
|**nível**|Constante, "Informacional"|
|**operationId**|Ver correlationId|
|**operationName**|O campo de valor é constante - "Microsoft.Security/locations/alerts/activate/action", e o valor localizado será "Alert Ativado" (pode potencialmente ser localizado em par do local do utilizador)|
|**nome do Grupo de Recursos**|Incluirá o nome do grupo de recursos|
|**nome de recursoProviderName**|Os subcampos de valor e de valor localizados são constantes - "Microsoft.Security"|
|**RecursosType**|Os subcampos de valor e de valor localizados são constantes - "Microsoft.Security/locations/alerts"|
|**recursosId**|O ID de recurso Azure totalmente qualificado|
|**estado**|Os subcampos de valor e de valor localizados são constantes - "Ative"|
|**subStatus**|Os subcampos de valor e de Valor Localizados estão vazios|
|**submissãoTimestamp**|O calendário utc de submissão de eventos ao Registo de Atividades|
|**subscriptionId**|O ID de subscrição do recurso comprometido|
|**propriedades**|Um saco JSON de propriedades adicionais pertencentes ao alerta. Estes podem mudar de um alerta para o outro, no entanto, os seguintes campos aparecerão em todos os alertas:<br>- gravidade: A gravidade do ataque<br>- Entidade comprometida: O nome do recurso comprometido<br>- reparaçãoSteps: Matriz de medidas de reparação a tomar<br>- intenção: a intenção da cadeia de morte do alerta. Possíveis intenções estão documentadas na [tabela Intenções](alerts-reference.md#intentions)|
|**relacionadosTevents**|Constante - matriz vazia|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

O Microsoft Graph é a porta de entrada para dados e inteligência na Microsoft 365. Fornece um modelo de programabilidade unificado que pode utilizar para aceder à enorme quantidade de dados no Microsoft 365, Windows 10 e Enterprise Mobility + Security. Use a riqueza de dados no Microsoft Graph para construir apps para organizações e consumidores que interagem com milhões de utilizadores.

O esquema e uma representação JSON para alertas de segurança enviados para o MS Graph, estão disponíveis [na documentação do Microsoft Graph](/graph/api/resources/alert).

---


## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu os esquemas que as ferramentas de proteção de ameaças do Azure Security Center usam ao enviar informações de alerta de segurança.

Para obter mais informações sobre as formas de aceder a alertas de segurança de fora do Centro de Segurança, consulte as seguintes páginas:

- [Azure Sentinel](../sentinel/index.yml) - SIEM nativo da Nuvem da Microsoft
- [Azure Event Hubs](../event-hubs/index.yml) - Serviço de ingestão de dados totalmente gerido e em tempo real da Microsoft
- [Exportar continuamente dados do Centro de Segurança](continuous-export.md)
- [Log Analytics workspaces](../azure-monitor/logs/quick-create-workspace.md) - Azure Monitor armazena dados de registo num espaço de trabalho Log Analytics, um recipiente que inclui informações de dados e configuração