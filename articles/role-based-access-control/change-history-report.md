---
title: Ver registos de atividade para alterações do RBAC do Azure
description: Veja os registos de atividade para o controlo de acesso baseado em funções azure (Azure RBAC) alterações aos recursos do Azure nos últimos 90 dias.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2024bd14241184338195ed635039bae774da816
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161796"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Ver registos de atividade para alterações do RBAC do Azure

Por vezes, é necessário obter informações sobre alterações no controlo de acesso baseado em funções do Azure (Azure RBAC), como para fins de auditoria ou resolução de problemas. Sempre que alguém faz alterações em atribuições de papéis ou definições de papéis dentro das suas subscrições, as alterações são registadas no Registo de [Atividades do Azure](../azure-monitor/platform/platform-logs-overview.md). Pode ver os registos de atividade para ver todas as alterações do Azure RBAC nos últimos 90 dias.

## <a name="operations-that-are-logged"></a>Operações que estão registadas

Aqui estão as operações relacionadas com o Azure RBAC que estão registadas no Registo de Atividades:

- Criar atribuição de papéis
- Eliminar a atribuição de papéis
- Criar ou atualizar definição de função personalizada
- Eliminar definição de papel personalizada

## <a name="azure-portal"></a>Portal do Azure

A maneira mais fácil de começar é ver os registos de atividade com o portal Azure. A imagem seguinte mostra um exemplo de operações de atribuição de papéis no registo de atividade. Também inclui uma opção para descarregar os registos como um ficheiro CSV.

![Registos de atividade usando o portal - screenshot](./media/change-history-report/activity-log-portal.png)

O registo de atividade no portal tem vários filtros. Aqui estão os filtros relacionados com o Azure RBAC:

| Filtro | Valor |
| --------- | --------- |
| Categoria de eventos | <ul><li>Administrativa</li></ul> |
| Operação | <ul><li>Criar atribuição de papéis</li><li>Eliminar a atribuição de papéis</li><li>Criar ou atualizar definição de função personalizada</li><li>Eliminar definição de papel personalizada</li></ul> |

Para obter mais informações sobre os registos de atividade, consulte [os registos](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)de atividade do View para monitorizar as ações sobre recursos .

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Para visualizar os registos de atividade com o Azure PowerShell, utilize o comando [Get-AzLog.](/powershell/module/Az.Monitor/Get-AzLog)

Este comando lista todas as mudanças de atribuição de papéis numa subscrição dos últimos sete dias:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Este comando enumera todas as mudanças de definição de papéis num grupo de recursos nos últimos sete dias:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Este comando lista todas as mudanças de atribuição de papéis e definição de funções numa subscrição dos últimos sete dias e apresenta os resultados numa lista:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>CLI do Azure

Para visualizar os registos de atividade com o Azure CLI, utilize o comando da [lista de registos de atividade az](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) monitor.

Este comando lista os registos de atividade num grupo de recursos a partir de 27 de fevereiro, olhando para a frente sete dias:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Este comando lista os registos de atividade do prestador de recursos de Autorização a partir de 27 de fevereiro, com vista a sete dias:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Os registos do Azure Monitor](../log-analytics/log-analytics-overview.md) são outra ferramenta que pode utilizar para recolher e analisar as alterações do Azure RBAC para todos os seus recursos Azure. Os registos do Monitor Azure têm os seguintes benefícios:

- Escrever consultas complexas e lógica
- Integrar com alertas, Power BI e outras ferramentas
- Guardar dados para períodos de retenção mais longos
- Cruz-referência com outros registos tais como segurança, máquina virtual e personalizado

Aqui estão os passos básicos para começar:

1. [Criar um espaço](../azure-monitor/learn/quick-create-workspace.md)de trabalho log Analytics .

1. [Configure a solução Activity Log Analytics](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) para o seu espaço de trabalho.

1. [Ver os registos de atividade](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Uma forma rápida de navegar para a solução De acesso à solução De registo de atividade é clicar na opção **Logs.**

   ![Opção de logs do Monitor Azure no portal](./media/change-history-report/azure-log-analytics-option.png)

1. Utilize opcionalmente o [Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md) para consultar e ver os registos. Para mais informações, consulte Iniciar com consultas de [registo do Monitor Azure](../azure-monitor/log-query/get-started-queries.md).

Aqui está uma consulta que devolve novas atribuições de funções organizadas pelo fornecedor de recursos alvo:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Aqui está uma consulta que devolve as mudanças de atribuição de papéis exibidas num gráfico:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Registos de atividade usando o portal Advanced Analytics - screenshot](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Passos seguintes
* [Ver eventos no registo de atividades](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorizar a Atividade de Subscrição com o Registo de Atividades do Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
