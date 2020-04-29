---
title: Alertas do Centro de Segurança Azure
description: Este artigo descreve os diferentes esquemas usados pelo Azure Security Center para alertas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 19ca17f66f6818ed4c3ef532e2030cc03f0e73ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062954"
---
# <a name="security-alerts-schemas"></a>Alertas de segurança schemas

Os utilizadores do nível padrão do Azure Security Center recebem alertas de segurança quando o Centro de Segurança deteta ameaças aos seus recursos.

Pode ver estes alertas de segurança nas páginas de **Proteção contra Ameaças** do Azure Security Center, ou através de ferramentas externas como:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) - SIEM nativo da Nuvem da Microsoft. O Conector Sentinel recebe alertas do Azure Security Center e envia-os para o espaço de [trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) para o Azure Sentinel.
- SIEMs de terceiros - Use as ferramentas [de exportação contínuas](continuous-export.md) do Security Center para enviar dados para os Hubs de [Eventos Azure](https://docs.microsoft.com/azure/event-hubs/). Em seguida, integre os seus dados do Event Hub com um SIEM de terceiros.
- [Rest API](https://docs.microsoft.com/rest/api/securitycenter/) - Se estiver a usar a API REST para aceder a alertas, consulte a documentação da [API de Alertas online](https://docs.microsoft.com/rest/api/securitycenter/alerts).

Se estiver a usar métodos programáticos para consumir os alertas, precisará do esquema correto para encontrar os campos que lhe são relevantes. Além disso, se estiver a exportar para um Hub de Eventos ou a tentar ativar a Workflow Automation com conectores genéricos HTTP, utilize os schemas para analisar corretamente os objetos JSON.

>[!IMPORTANT]
> O esquema é ligeiramente diferente para cada um destes cenários, por isso certifique-se de selecionar o separador relevante abaixo.


## <a name="the-schemas"></a>Os schemas 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Automação de fluxo de trabalho e exportação contínua para O Hub de Eventos](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Amostra JSON para alertas enviados para Apps Lógicas, Hub de Eventos e SIEMs de terceiros

Abaixo encontrará o esquema dos eventos de alerta passados para:

- Instâncias da App Azure Logic que foram configuradas na automatização de fluxos de trabalho do Security Center
- Azure Event Hub usando a funcionalidade de exportação contínua do Security Center

Para obter mais informações sobre a funcionalidade de automatização do fluxo de trabalho, consulte [as respostas da Automatização automática a alertas e recomendações.](workflow-automation.md)
Para obter mais informações sobre exportação contínua, consulte [alertas e recomendações para exportação.](continuous-export.md)

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Espaços de trabalho Azure Sentinel e Log Analytics](#tab/schema-sentinel)

O Conector Sentinel recebe alertas do Azure Security Center e envia-os para o espaço de trabalho de Log Analytics para o Azure Sentinel. 

Para criar um caso Sentinel ou incidente usando alertas do Security Center, você precisará do esquema para os alertas mostrados abaixo. 

Para mais informações sobre o Azure Sentinel, consulte [a documentação](https://docs.microsoft.com/azure/sentinel/).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Registo de Atividades do Azure](#tab/schema-activitylog)

As auditorias do Azure Security Center geraram alertas de segurança como eventos no Registo de Atividades do Azure.

Pode visualizar os alertas de segurança em eventos no Registo de Atividade, procurando o evento Activate Alert como mostrado:

[![Pesquisar o registo de atividade para o evento Activate Alert](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Amostra JSON para alertas enviados para Registo de Atividades do Azure

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
|**coralaid**|O Id de alerta do Centro de Segurança Azure|
|**descrição**|Descrição do alerta|
|**eventoDataId**|Ver correlationid|
|**nome de evento**|Os subcampos value e localizados Contêm o nome do visor de alerta|
|**categoria**|Os subcampos de valor e valor localizadosão constantes - "Segurança"|
|**eventoTimestamp**|Carimbo de tempo UTC para quando o alerta foi gerado|
|**ID**|O ID de alerta totalmente qualificado|
|**nível**|Constante, "Informacional"|
|**operaçãoId**|Ver correlationid|
|**operaçãoNome**|O campo de valor é constante - "Microsoft.Security/locations/alerts/activate/action", e o valor localizado será "Activate Alert" (pode potencialmente ser localizado par o local do utilizador)|
|**resourceGroupName**|Incluirá o nome do grupo de recursos|
|**nome fornecedor de recursos**|Os subcampos de valor e valor localizados são constantes - "Microsoft.Security"|
|**recursosType**|Os subcampos de valor e valor localizados são constantes - "Microsoft.Security/locations/alerts"|
|**recursosId**|O ID de recurso Azure totalmente qualificado|
|**estado**|Os subcampos de valor e valor localizados são constantes - "Ativo"|
|**subEstatuto**|Os subcampos de valor e valor localizados estão vazios|
|**submissãoTimestamp**|O carimbo de tempo utc da submissão do evento ao Registo de Atividades|
|**subscriptionId**|A identificação de subscrição do recurso comprometido|
|**propriedades**|Um saco JSON de propriedades adicionais relacionadas com o alerta. Estes podem mudar de um alerta para o outro, no entanto, os seguintes campos aparecerão em todos os alertas:<br>- gravidade: A gravidade do ataque<br>- Entidade comprometida: O nome do recurso comprometido<br>- remediaçãoPassos: Conjunto de medidas de reparação a tomar<br>- intenção: A intenção de matar o alerta. Possíveis intenções estão documentadas na [tabela Intenções](alerts-reference.md#intentions)|
|**relacionadoSEventos**|Constante - matriz vazia|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

O Microsoft Graph é a porta de entrada para dados e inteligência no Microsoft 365. Fornece um modelo de programabilidade unificado que pode utilizar para aceder à enorme quantidade de dados no Office 365, Windows 10 e Enterprise Mobility + Security. Use a riqueza de dados no Microsoft Graph para construir apps para organizações e consumidores que interagem com milhões de utilizadores.

O esquema e uma representação da JSON para alertas de segurança enviados para o MS Graph, estão disponíveis na [documentação do Microsoft Graph](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0).

---


## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu os esquemas que as ferramentas de proteção contra ameaças do Azure Security Center usam ao enviar informações de alerta de segurança.

Para obter mais informações sobre as formas de aceder a alertas de segurança de fora do Centro de Segurança, consulte as seguintes páginas:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) - SIEM nativo da nuvem da Microsoft
- [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) - Serviço de ingestão de dados totalmente gerido e em tempo real da Microsoft
- Característica contínua de [exportação](continuous-export.md) do Centro de Segurança
- [Log Analytics espaços de trabalho](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) - Azure Monitor armazena dados de log num espaço de trabalho log Analytics, um recipiente que inclui dados e informações de configuração
