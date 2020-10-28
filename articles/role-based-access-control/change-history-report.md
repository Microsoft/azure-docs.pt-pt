---
title: Ver registos de atividade para alterações do RBAC do Azure
description: Ver registos de atividade para o controlo de acesso baseado em funções Azure (Azure RBAC) alterações nos recursos Azure nos últimos 90 dias.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: 0ad60e72300e381b57deb00f3db010e69a006441
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742940"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Ver registos de atividade para alterações do RBAC do Azure

Por vezes, precisa de informações sobre alterações no controlo de acesso baseado em funções (Azure RBAC), tais como para efeitos de auditoria ou resolução de problemas. Sempre que alguém faz alterações nas atribuições de funções ou definições de funções dentro das suas subscrições, as alterações são registadas no [Registo de Atividades do Azure](../azure-monitor/platform/platform-logs-overview.md). Pode ver os registos de atividade para ver todas as alterações do Azure RBAC nos últimos 90 dias.

## <a name="operations-that-are-logged"></a>Operações registadas

Aqui estão as operações relacionadas com o Azure RBAC que estão registadas no Registo de Atividades:

- Criar atribuição de funções
- Eliminar atribuição de funções
- Criar ou atualizar a definição de função personalizada
- Eliminar definição de função personalizada

## <a name="azure-portal"></a>Portal do Azure

A forma mais fácil de dar início a este processo é ver os registos de atividades com recurso ao portal do Azure. A imagem que se segue mostra um exemplo de operações de atribuição de funções no registo de atividades. Também inclui uma opção para descarregar os registos como um ficheiro CSV.

![Registos de atividade usando o portal - screenshot](./media/change-history-report/activity-log-portal.png)

O registo de atividade no portal tem vários filtros. Aqui estão os filtros relacionados com o Azure RBAC:

| Filtro | Valor |
| --------- | --------- |
| Categoria de eventos | <ul><li>Administrativo</li></ul> |
| Operação | <ul><li>Criar atribuição de funções</li><li>Eliminar atribuição de funções</li><li>Criar ou atualizar a definição de função personalizada</li><li>Eliminar definição de função personalizada</li></ul> |

Para obter mais informações sobre registos de atividades, consulte [os registos de atividade para monitorizar as ações sobre recursos.](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Para visualizar registos de atividade com a Azure PowerShell, utilize o comando [Get-AzLog.](/powershell/module/Az.Monitor/Get-AzLog)

Este comando lista todas as alterações de atribuição de funções numa subscrição dos últimos sete dias:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Este comando lista todas as alterações de definição de funções num grupo de recursos nos últimos sete dias:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Este comando lista todas as alterações de atribuição de funções e definição de função numa subscrição nos últimos sete dias e apresenta os resultados numa lista:

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
                          eventCategory  : Administrative

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

Se estiver a utilizar um principal de serviço para criar atribuições de funções, a propriedade Caller será um ID de objeto. Pode utilizar [o Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) para obter informações sobre o principal do serviço.

```Example
Caller                  : 44444444-4444-4444-4444-444444444444
EventTimestamp          : 6/4/2020 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: 55555555-5555-5555-5555-555555555555
                          category       : Administrative
```

## <a name="azure-cli"></a>CLI do Azure

Para visualizar registos de atividade com o CLI Azure, utilize o comando [da lista de registos de atividade do monitor az.](/cli/azure/monitor/activity-log#az-monitor-activity-log-list)

Este comando lista os registos de atividade num grupo de recursos a partir de 27 de fevereiro, olhando para a frente sete dias:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Este comando lista os registos de atividade do fornecedor de recursos de autorização a partir de 27 de fevereiro, aguardando sete dias:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Os registos do Azure Monitor](../log-analytics/log-analytics-overview.md) são outra ferramenta que pode utilizar para recolher e analisar alterações do Azure RBAC para todos os seus recursos Azure. Os registos do Azure Monitor têm os seguintes benefícios:

- Escreva consultas complexas e lógica
- Integre com alertas, Power BI e outras ferramentas
- Guardar dados para períodos de retenção mais longos
- Cruze referência com outros registos, tais como segurança, máquina virtual e personalizado

Aqui estão os passos básicos para começar:

1. [Criar um espaço de trabalho Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Configure a solução Activity Log Analytics](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) para o seu espaço de trabalho.

1. [Ver os registos de atividade](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Uma forma rápida de navegar para a página de visão geral da solução De Registo de Atividade é clicar na opção **Logs.**

   ![Opção de registos do Monitor Azure no portal](./media/change-history-report/azure-log-analytics-option.png)

1. Utilize opcionalmente o [Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md) para consultar e visualizar os registos. Para obter mais informações, consulte Começar com consultas de registo do [Azure Monitor](../azure-monitor/log-query/get-started-queries.md).

Aqui está uma consulta que devolve novas atribuições de funções organizadas pelo fornecedor de recursos-alvo:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Aqui está uma consulta que devolve as alterações de atribuição de funções exibidas num gráfico:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Registos de atividades usando o portal Advanced Analytics - screenshot](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Passos seguintes
* [Ver eventos no registo de atividades](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorizar a Atividade de Subscrição com o Registo de Atividades do Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
