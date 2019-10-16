---
title: Exibir os logs de atividades do Azure para monitorar recursos | Microsoft Docs
description: Use os logs de atividade para examinar erros e ações do usuário. Mostra portal do Azure PowerShell, CLI do Azure e REST.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 25bce613ab45f20f7060447bcfc47f452f4d70f2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329445"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Exibir logs de atividade para monitorar ações em recursos

Através dos registos de atividades, pode determinar:

* quais operações foram executadas nos recursos em sua assinatura
* Quem iniciou a operação
* Quando a operação ocorreu
* o status da operação
* os valores de outras propriedades que podem ajudá-lo a Pesquisar a operação

O log de atividades contém todas as operações de gravação (PUT, POST e DELETE) para seus recursos. Não inclui operações de leitura (GET). Para obter uma lista de ações de recursos, consulte [Azure Resource Manager operações do provedor de recursos](../role-based-access-control/resource-provider-operations.md). Pode utilizar os registos de atividade para encontrar um erro ao resolver um problema ou para monitorizar a forma como um utilizador na sua organização alterou um recurso.

Os registos de atividades são mantidos durante 90 dias. Pode consultar qualquer intervalo de datas, desde que a data de início não seja superior a 90 dias no passado.

Você pode recuperar informações dos logs de atividade por meio do portal, do PowerShell, do CLI do Azure, da API REST do insights ou da [biblioteca do .net do insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Portal do Azure

1. Para exibir os logs de atividade por meio do portal, selecione **Monitor**.

    ![Selecionar monitor](./media/resource-group-audit/select-monitor.png)

1. Selecione **log de atividades**.

    ![Selecionar log de atividades](./media/resource-group-audit/select-activity-log.png)

1. Você verá um resumo das operações recentes. Um conjunto padrão de filtros é aplicado às operações. Observe que as informações no resumo incluem quem iniciou a ação e quando ela aconteceu.

    ![Exibir Resumo das operações recentes](./media/resource-group-audit/audit-summary.png)

1. Para executar rapidamente um conjunto predefinido de filtros, selecione **insights rápidos**.

    ![Selecionar insights rápidos](./media/resource-group-audit/select-quick-insights.png)

1. Selecione uma das opções. Por exemplo, selecione **implantações com falha** para ver erros de implantações.

    ![Selecionar implantações com falha](./media/resource-group-audit/select-failed-deployments.png)

1. Observe que os filtros foram alterados para se concentrar em erros de implantação nas últimas 24 horas. Somente as operações que correspondem aos filtros são exibidas.

    ![Ver filtros](./media/resource-group-audit/view-filters.png)

1. Para se concentrar em operações específicas, altere os filtros ou aplique novos. Por exemplo, a imagem a seguir mostra um novo valor para o **TimeSpan** e o **tipo de recurso** é definido como contas de armazenamento.

    ![Definir opções de filtro](./media/resource-group-audit/set-filter.png)

1. Se você precisar executar a consulta novamente mais tarde, selecione **fixar filtros atuais**.

    ![Filtros de PIN](./media/resource-group-audit/pin-filters.png)

1. Dê um nome ao filtro.

    ![Filtros de nome](./media/resource-group-audit/name-filters.png)

1. O filtro está disponível no painel.

    ![Mostrar filtro no painel](./media/resource-group-audit/show-dashboard.png)

1. No portal, você pode exibir as alterações em um recurso. Volte para o modo de exibição padrão no monitor e selecione uma operação que envolvia a alteração de um recurso.

    ![Selecionar operação](./media/resource-group-audit/select-operation.png)

1. Selecione **histórico de alterações (versão prévia)** e escolha uma das operações disponíveis.

    ![Selecionar histórico de alterações](./media/resource-group-audit/select-change-history.png)

1. As alterações no recurso são exibidas.

    ![Mostrar alterações](./media/resource-group-audit/show-changes.png)

Para saber mais sobre o histórico de alterações, consulte [obter alterações de recurso](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para recuperar entradas de log, execute o comando **Get-AzLog** . Você fornece parâmetros adicionais para filtrar a lista de entradas. Se você não especificar uma hora de início e de término, as entradas dos últimos sete dias serão retornadas.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

O exemplo a seguir mostra como usar o log de atividades para pesquisar operações executadas durante um período especificado. As datas de início e de término são especificadas em um formato de data.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Ou, você pode usar as funções de data para especificar o intervalo de datas, como os últimos 14 dias.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Você pode pesquisar as ações executadas por um usuário específico.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Você pode filtrar para operações com falha.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Você pode se concentrar em um erro examinando a mensagem de status para essa entrada.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Você pode selecionar valores específicos para limitar os dados retornados.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

Dependendo da hora de início especificada, os comandos anteriores podem retornar uma longa lista de operações para o grupo de recursos. Você pode filtrar os resultados para o que está procurando fornecendo critérios de pesquisa. Por exemplo, você pode filtrar pelo tipo de operação.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Você pode usar o grafo de recursos para ver o histórico de alterações de um recurso. Para obter mais informações, consulte [obter alterações de recurso](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>CLI do Azure

Para recuperar entradas de log, execute o comando [AZ monitor Activity-log List](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) com um deslocamento para indicar o período de tempo.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

O exemplo a seguir mostra como usar o log de atividades para pesquisar operações executadas durante um período especificado. As datas de início e de término são especificadas em um formato de data.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Você pode pesquisar as ações executadas por um usuário específico, mesmo para um grupo de recursos que não existe mais.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Você pode filtrar para operações com falha.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Você pode se concentrar em um erro examinando a mensagem de status para essa entrada.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Você pode selecionar valores específicos para limitar os dados retornados.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

Dependendo da hora de início especificada, os comandos anteriores podem retornar uma longa lista de operações para o grupo de recursos. Você pode filtrar os resultados para o que está procurando fornecendo critérios de pesquisa. Por exemplo, você pode filtrar pelo tipo de operação.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Você pode usar o grafo de recursos para ver o histórico de alterações de um recurso. Para obter mais informações, consulte [obter alterações de recurso](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>API REST

As operações REST para trabalhar com o log de atividades fazem parte da [API REST do insights](/rest/api/monitor/). Para recuperar eventos do log de atividades, consulte [listar os eventos de gerenciamento em uma assinatura](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Passos seguintes

* Os logs de atividades do Azure podem ser usados com Power BI para obter mais informações sobre as ações em sua assinatura. Consulte [Exibir e analisar logs de atividades do Azure em Power bi e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Para saber mais sobre como configurar políticas de segurança, consulte [controle de acesso baseado em função do Azure](../role-based-access-control/role-assignments-portal.md).
* Para exibir mais detalhes sobre as alterações em seus aplicativos da camada de infraestrutura até a implantação do aplicativo, consulte [usar a análise de alterações do aplicativo no Azure monitor](../azure-monitor/app/change-analysis.md).
* Para saber mais sobre os comandos para exibir as operações de implantação, consulte [Exibir operações de implantação](resource-manager-deployment-operations.md).
* Para saber como evitar exclusões em um recurso para todos os usuários, consulte [Bloquear recursos com Azure Resource Manager](resource-group-lock-resources.md).
* Para ver a lista de operações disponíveis para cada provedor Microsoft Azure Resource Manager, consulte [Azure Resource Manager operações do provedor de recursos](../role-based-access-control/resource-provider-operations.md)
