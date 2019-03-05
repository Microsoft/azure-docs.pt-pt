---
title: Migrar os alertas do Azure em eventos de gestão para alertas de registo de atividades
description: Alertas nos eventos de gestão serão removidas no dia 1 de Outubro. Prepare-se através de alertas existentes de migração.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/14/2017
ms.author: johnkem
ms.subservice: alerts
ms.openlocfilehash: 4d82cc59eb1098451a263957aa028b66996bb072
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307195"
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Migrar os alertas do Azure em eventos de gestão para alertas de registo de atividades

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!WARNING]
> Alertas nos eventos de gestão serão desligadas em ou após 1 de Outubro. Utilize as instruções abaixo para compreender se tiver estes alertas e migrá-los, se for o caso.

## <a name="what-is-changing"></a>O que está mudando

O Azure Monitor (anteriormente conhecido como Azure Insights) oferecido um recurso para criar um alerta que acionou fora dos eventos de gestão e geradas notificações para um endereços de e-mail ou URL do webhook. Pode ter criado um destes alertas, qualquer uma das seguintes formas:
* No portal do Azure para determinados tipos de recursos, em monitorização -> alertas -> Adicionar alerta, em que "Alerta" é definida como "Eventos"
* Ao executar o cmdlet Add-AzLogAlertRule PowerShell
* Utilizando diretamente [a API de REST de alerta](https://docs.microsoft.com/rest/api/monitor/alertrules) com OData = "ManagementEventRuleCondition" e dataSource.odata.type = "RuleManagementEventDataSource"
 
O seguinte script do PowerShell devolve uma lista de todos os alertas nos eventos de gestão que tem na sua subscrição, bem como as condições definidas sobre cada alerta.

```powershell
Connect-AzAccount
$alerts = $null
foreach ($rg in Get-AzResourceGroup ) {
  $alerts += Get-AzAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

Se não tem alertas nos eventos de gestão, o cmdlet do PowerShell acima produzirá uma série de mensagens de aviso como esta:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

Estas mensagens de aviso podem ser ignoradas. Se tiver alertas nos eventos de gestão, a saída deste cmdlet do PowerShell terá esta aparência:

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

Cada alerta é separada por uma linha tracejada e detalhes incluem o ID de recurso do alerta e a regra específica que está a ser monitorizados.

Esta funcionalidade transitou para [alertas de registo de atividade do Azure Monitor](../../azure-monitor/platform/activity-log-alerts.md). Estes novos alertas permitem-lhe definir uma condição em eventos de registo de atividades e receber uma notificação quando a condição corresponde a um novo evento. Também oferece vários aperfeiçoamentos de alertas nos eventos de gestão:
* É possível reutilizar o seu grupo de destinatários de notificação ("ações") em muitos alertas usando [grupos de ação](../../azure-monitor/platform/action-groups.md), reduzindo a complexidade da alteração que deve receber um alerta.
* Pode receber uma notificação diretamente no telefone usando o SMS com grupos de ação.
* Pode [criar alertas de registo de atividades com modelos do Resource Manager](../../azure-monitor/platform/alerts-activity-log.md).
* Pode criar condições com maior flexibilidade e a complexidade para atender às suas necessidades específicas.
* Notificações são entregues mais rapidamente.
 
## <a name="how-to-migrate"></a>Como migrar
 
Para criar um alerta de registo de nova atividade, pode:
* Siga [nosso guia sobre como criar um alerta no portal do Azure](../../azure-monitor/platform/activity-log-alerts.md)
* Saiba como [criar um alerta com um modelo do Resource Manager](../../azure-monitor/platform/alerts-activity-log.md)
 
Alertas nos eventos de gestão que tenha criado anteriormente não serão migradas automaticamente para os alertas de registo de atividade. Tem de utilizar o script anterior do PowerShell para listar os alertas nos eventos de gestão que tiver configurado o atualmente e recriá-los manualmente como alertas de registo de atividade. Isso deve ser feito antes de 1 de Outubro, após o qual alertas nos eventos de gestão já não estarão visíveis na sua subscrição do Azure. Outros tipos de alertas do Azure, incluindo alertas de métricas do Azure Monitor, os alertas das informações de aplicativo e alertas do Log Analytics não são afetados por esta alteração. Se tiver dúvidas, publique nos comentários abaixo.


## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [registo de atividades](../../azure-monitor/platform/activity-logs-overview.md)
* Configurar [alertas de registo de atividade através do portal do Azure](../../azure-monitor/platform/activity-log-alerts.md)
* Configurar [alertas de registo de atividade através do Gestor de recursos](../../azure-monitor/platform/alerts-activity-log.md)
* Reveja o [esquema de webhook de alerta de registo de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md)
* Saiba mais sobre [notificações de serviço](../../azure-monitor/platform/service-notifications.md)
* Saiba mais sobre [grupos de ação](../../azure-monitor/platform/action-groups.md)

