---
title: Ver registos de atividades de RBAC muda para recursos do Azure | Documentos da Microsoft
description: Ver registos de atividades para controlo de acesso baseado em funções (RBAC) alterações aos recursos do Azure nos últimos 90 dias.
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
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cab5b9317102a86dd75d2cb7e5a820cf64d2e831
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535553"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Ver registos de atividade para alterações RBAC para recursos do Azure

Às vezes, precisa de informações sobre as alterações de controlo (RBAC) de acesso baseado em funções aos recursos do Azure, tal como para auditoria ou fins de resolução de problemas. Sempre que alguém faz alterações a atribuições de funções ou definições de funções nas suas subscrições, as alterações de fazer logon [registo de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md). Pode ver os registos de atividade para ver todas as alterações RBAC nos últimos 90 dias.

## <a name="operations-that-are-logged"></a>Operações que têm sessão iniciadas

Seguem-se as operações relacionadas com o RBAC que estão registadas no registo de atividades:

- Criar atribuição de função
- Eliminar atribuição de função
- Criar ou atualizar uma definição de função personalizada
- Eliminar definição de função personalizada

## <a name="azure-portal"></a>Portal do Azure

É a maneira mais fácil para começar a utilizar ver os registos de atividade no portal do Azure. Captura de ecrã seguinte mostra um exemplo de um registo de atividade que foi filtrado para apresentar a atribuição de função e as operações de definição de função. Ele também inclui uma ligação para transferir os registos como um ficheiro CSV.

![Registos de atividades com o portal - captura de ecrã](./media/change-history-report/activity-log-portal.png)

O registo de atividades no portal do tem vários filtros. Seguem-se os filtros de RBAC:

|Filtrar  |Value  |
|---------|---------|
|Categoria de eventos     | <ul><li>Administrativa</li></ul>         |
|Operação     | <ul><li>Criar atribuição de função</li> <li>Eliminar atribuição de função</li> <li>Criar ou atualizar uma definição de função personalizada</li> <li>Eliminar definição de função personalizada</li></ul>      |


Para obter mais informações sobre os registos de atividade, consulte [visualizar eventos no registo de atividades](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Para ver os registos de atividades com o Azure PowerShell, utilize o [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) comando.

Este comando apresenta uma lista de todas as alterações de atribuição de função numa subscrição nos últimos sete dias:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Este comando apresenta uma lista de todas as alterações de definição de função num grupo de recursos nos últimos sete dias:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales-projectforecast -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Este comando apresenta uma lista de todos os atribuição de função e as alterações de definição de função numa subscrição nos últimos sete dias e exibe os resultados numa lista:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast"}}

```

## <a name="azure-cli"></a>CLI do Azure

Para ver os registos de atividades com a CLI do Azure, utilize o [lista de registo de atividade do monitor de az](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) comando.

Este comando lista os registos de atividades num grupo de recursos, desde a hora de início:

```azurecli
az monitor activity-log list --resource-group pharma-sales-projectforecast --start-time 2018-04-20T00:00:00Z
```

Este comando lista os registos de atividade para o fornecedor de recursos de autorização, desde a hora de início:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Registos de Monitor do Azure

[Registos de Monitor do Azure](../log-analytics/log-analytics-overview.md) é outra ferramenta que pode utilizar para recolher e analisar as alterações de RBAC para todos os seus recursos do Azure. Registos de Monitor do Azure tem as seguintes vantagens:

- Escrever consultas complexas e lógica
- Integrar com alertas, o Power BI e outras ferramentas
- Guardar os dados por períodos de retenção mais longos
- Referência cruzada com outros registos, como segurança, a máquina virtual e personalizada

Aqui estão as etapas básicas para começar a utilizar:

1. [Criar uma área de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Configurar a solução Log Analytics da atividade](../azure-monitor/platform/collect-activity-logs.md#configuration) sua área de trabalho.

1. [Ver os registos de atividade](../azure-monitor/platform/collect-activity-logs.md#using-the-solution). Uma forma rápida de navegar para a página de descrição geral de solução do Log Analytics da atividade é clicar o **do Log Analytics** opção.

   ![Opção de registos de Monitor do Azure no portal](./media/change-history-report/azure-log-analytics-option.png)

1. Opcionalmente, utilize o [pesquisa de registos](../log-analytics/log-analytics-log-search.md) página ou o [portal da análise avançada](../azure-monitor/log-query/get-started-portal.md) para consultar e ver os registos. Para obter mais informações sobre estas duas opções, consulte [página de pesquisa de registos ou o portal da análise avançada](../azure-monitor/log-query/portals.md).

Eis uma consulta que retorna as novas atribuições de função organizadas pelo fornecedor de recursos de destino:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Eis uma consulta que retorna as alterações de atribuição de função apresentadas num gráfico:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Registos de atividades com o portal do Advanced Analytics - captura de ecrã](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Passos Seguintes
* [Ver eventos no registo de atividades](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorizar a atividade de subscrição com o registo de atividades do Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
