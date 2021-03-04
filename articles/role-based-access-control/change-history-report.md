---
title: Ver registos de atividade para alterações do RBAC do Azure
description: Ver registos de atividade para alterações do controlo de acesso baseado em funções Azure (Azure RBAC) nos últimos 90 dias.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: d9b39bc9a2f00fe83cae0ff78c6346042967e8bf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042142"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Ver registos de atividade para alterações do RBAC do Azure

Por vezes, precisa de informações sobre alterações no controlo de acesso baseado em funções (Azure RBAC), tais como para efeitos de auditoria ou resolução de problemas. Sempre que alguém faz alterações nas atribuições de funções ou definições de funções dentro das suas subscrições, as alterações são registadas no [Registo de Atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md). Pode ver os registos de atividade para ver todas as alterações do Azure RBAC nos últimos 90 dias.

## <a name="operations-that-are-logged"></a>Operações registadas

Aqui estão as operações relacionadas com o Azure RBAC que estão registadas no Registo de Atividades:

- Criar atribuição de funções
- Eliminar atribuição de funções
- Criar ou atualizar a definição de função personalizada
- Eliminar definição de função personalizada

## <a name="azure-portal"></a>Portal do Azure

A forma mais fácil de dar início a este processo é ver os registos de atividades com recurso ao portal do Azure. A imagem que se segue mostra um exemplo de operações de atribuição de funções no registo de atividades. Também inclui uma opção para descarregar os registos como um ficheiro CSV.

![Registos de atividade usando o portal - screenshot](./media/change-history-report/activity-log-portal.png)

Para obter mais informações, clique numa entrada para abrir o painel de resumo. Clique no separador **JSON** para obter um registo detalhado.

![Registos de atividade usando o portal com painel de resumo aberto - screenshot](./media/change-history-report/activity-log-summary-portal.png)

O registo de atividade no portal tem vários filtros. Aqui estão os filtros relacionados com o Azure RBAC:

| Filtro | Valor |
| --------- | --------- |
| Categoria de eventos | <ul><li>Administrativo</li></ul> |
| Operação | <ul><li>Criar atribuição de funções</li><li>Eliminar atribuição de funções</li><li>Criar ou atualizar a definição de função personalizada</li><li>Eliminar definição de função personalizada</li></ul> |

Para obter mais informações sobre registos de atividades, consulte [os registos de atividade para monitorizar as ações sobre recursos.](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)


## <a name="interpret-a-log-entry"></a>Interpretar uma entrada de registo

A saída de registo a partir do separador JSON, Azure PowerShell ou Azure CLI pode incluir muita informação. Aqui estão algumas das propriedades-chave a procurar quando se tenta interpretar uma entrada de registo. Para obter formas de filtrar a saída de registo utilizando a Azure PowerShell ou a Azure CLI, consulte as seguintes secções.

> [!div class="mx-tableFixed"]
> | Propriedade | Valores de exemplo | Descrição |
> | --- | --- | --- |
> | autorização:ação | Microsoft.Authorization/roleAssignments/write | Criar atribuição de funções |
> |  | Microsoft.Autorização/funAsignments/delete | Eliminar atribuição de funções |
> |  | Microsoft.Authorization/roleDefinitions/write | Criar ou atualizar a definição de função |
> |  | Microsoft.Authorization/roleDefinitions/delete | Eliminar a definição de função |
> | autorização:âmbito | /subscrições/{subscriçãoD}<br/>/subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | Margem para a ação |
> | chamador | admin@example.com<br/>{objectId} | Quem iniciou a ação |
> | eventTimestamp | 2021-03-01T22:07:41.126243Z | Tempo que a ação ocorreu |
> | estado:valor | Iniciado<br/>Com êxito<br/>Com falhas | Estado da ação |

## <a name="azure-powershell"></a>Azure PowerShell

Para visualizar registos de atividade com a Azure PowerShell, utilize o comando [Get-AzLog.](/powershell/module/Az.Monitor/Get-AzLog)

Este comando lista todas as alterações de atribuição de funções numa subscrição dos últimos sete dias:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Este comando lista todas as alterações de definição de funções num grupo de recursos nos últimos sete dias:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>Saída de registo de filtro

A saída de registo pode incluir muita informação. Este comando lista todas as alterações de atribuição de funções e definição de função numa subscrição dos últimos sete dias e filtra a saída:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

O seguinte mostra um exemplo da saída de registo filtrado ao criar uma atribuição de função:

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

Se estiver a utilizar um principal de serviço para criar atribuições de funções, a propriedade Caller será um ID principal de serviço. Pode utilizar [o Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) para obter informações sobre o principal do serviço.

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>CLI do Azure

Para visualizar registos de atividade com o CLI Azure, utilize o comando [da lista de registos de atividade do monitor az.](/cli/azure/monitor/activity-log#az_monitor_activity_log_list)

Este comando lista os registos de atividade num grupo de recursos a partir de 1 de março, olhando para a frente sete dias:

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

Este comando lista os registos de atividade do fornecedor de recursos de autorização a partir de 1 de março, aguardando sete dias:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>Saída de registo de filtro

A saída de registo pode incluir muita informação. Este comando lista todas as alterações de atribuição de funções e definição de função numa subscrição que olha para a frente sete dias e filtra a saída:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

O seguinte mostra um exemplo da saída de registo filtrado ao criar uma atribuição de função:

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Os registos do Azure Monitor](../azure-monitor/logs/log-query-overview.md) são outra ferramenta que pode utilizar para recolher e analisar alterações do Azure RBAC para todos os seus recursos Azure. Os registos do Azure Monitor têm os seguintes benefícios:

- Escreva consultas complexas e lógica
- Integre com alertas, Power BI e outras ferramentas
- Guardar dados para períodos de retenção mais longos
- Cruze referência com outros registos, tais como segurança, máquina virtual e personalizado

Aqui estão os passos básicos para começar:

1. [Criar um espaço de trabalho Log Analytics](../azure-monitor/logs/quick-create-workspace.md).

1. [Configure a solução Activity Log Analytics](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution) para o seu espaço de trabalho.

1. [Ver os registos de atividade](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution). Uma forma rápida de navegar para a página de visão geral da solução De Registo de Atividade é clicar na opção **Logs.**

   ![Opção de registos do Monitor Azure no portal](./media/change-history-report/azure-log-analytics-option.png)

1. Utilize opcionalmente o [Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) para consultar e visualizar os registos. Para obter mais informações, consulte [Começar com consultas de registo no Azure Monitor](../azure-monitor/logs/get-started-queries.md).

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
* [Ver registos de atividades para monitorizar ações em recursos](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorizar a atividade de subscrição com o registo de atividades Azure](../azure-monitor/essentials/platform-logs-overview.md)