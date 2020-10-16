---
title: Ver registos de atividades do Azure para monitorizar recursos
description: Utilize os registos de atividade para rever as ações e erros do utilizador. Mostra o portal Azure PowerShell, Azure CLI e REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 14015e9b2792515e6818af551b8bd9f54c686bee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371597"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Ver registos de atividades para monitorizar ações em recursos

Através dos registos de atividades, pode determinar:

* que operações foram tomadas sobre os recursos na sua subscrição
* que iniciou a operação
* quando a operação ocorreu
* o estado da operação
* os valores de outras propriedades que podem ajudá-lo a pesquisar a operação

O registo de atividades contém todas as operações de escrita (PUT, POST, DELETE) dos recursos. Não inclui operações de leitura (GET). Para obter uma lista de ações de recursos, consulte [as operações do fornecedor de recursos Azure](../../role-based-access-control/resource-provider-operations.md). Pode utilizar os registos de atividade para encontrar um erro ao resolver um problema ou para monitorizar a forma como um utilizador na sua organização alterou um recurso.

Os registos de atividades são mantidos durante 90 dias. Pode consultar qualquer intervalo de datas, desde que a data de início não seja superior a 90 dias no passado.

Pode obter informações dos registos de atividade através do portal, PowerShell, Azure CLI, Insights REST API ou [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Portal do Azure

Para ver os registos de atividade através do portal, siga estes passos:

1. No menu do portal Azure, selecione **Monitor**ou procure e selecione **Monitor** em qualquer página.

    ![Selecione monitor](./media/view-activity-logs/select-monitor-from-menu.png)

1. Selecione **Registo de Atividades**.

    ![Selecione registo de atividades](./media/view-activity-logs/select-activity-log.png)

1. Vê-se um resumo das operações recentes. Aplica-se um conjunto predefinido de filtros às operações. Note-se que a informação sobre o resumo inclui quem iniciou a ação e quando aconteceu.

    ![Ver resumo das operações recentes](./media/view-activity-logs/audit-summary.png)

1. Para executar rapidamente um conjunto de filtros pré-definidos, selecione **Quick Insights**.

    ![Selecione insights rápidos](./media/view-activity-logs/select-quick-insights.png)

1. Selecione uma das opções. Por exemplo, selecione **Implementações falhadas** para ver erros de implementações.

    ![Selecione implementações falhadas](./media/view-activity-logs/select-failed-deployments.png)

1. Note que os filtros foram alterados para se concentrarem nos erros de implementação nas últimas 24 horas. São apresentadas apenas operações que correspondam aos filtros.

    ![Ver filtros](./media/view-activity-logs/view-filters.png)

1. Para se concentrar em operações específicas, mude os filtros ou aplique novos. Por exemplo, a imagem a seguir mostra um novo valor para o **tipo Timespan** e **o tipo de recurso** é definido para contas de armazenamento.

    ![Definir opções de filtro](./media/view-activity-logs/set-filter.png)

1. Se precisar de executar a consulta novamente mais tarde, selecione **filtros de corrente Pin**.

    ![Filtros pinos](./media/view-activity-logs/pin-filters.png)

1. Dê um nome ao filtro.

    ![Filtros de nome](./media/view-activity-logs/name-filters.png)

1. O filtro está disponível no painel de instrumentos. No menu do portal do Azure, selecione **Dashboard**.

    ![Mostrar filtro no painel de instrumentos](./media/view-activity-logs/activity-log-on-dashboard.png)

1. A partir do portal, pode visualizar alterações a um recurso. Volte à vista padrão no Monitor e selecione uma operação que envolva a alteração de um recurso.

    ![Selecione operação](./media/view-activity-logs/select-operation.png)

1. Selecione **Alterar o histórico (Pré-visualização)** e escolha uma das operações disponíveis.

    ![Selecione o histórico de alterações](./media/view-activity-logs/select-change-history.png)

1. As alterações no recurso são apresentadas.

    ![Mostrar alterações](./media/view-activity-logs/show-changes.png)

Para saber mais sobre a história da mudança, consulte [Obter alterações de recursos.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para obter as entradas de registo, execute o comando **Get-AzLog.** Fornece parâmetros adicionais para filtrar a lista de entradas. Se não especificar um tempo de início e fim, as entradas dos últimos sete dias são devolvidas.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

O exemplo a seguir mostra como utilizar o registo de atividades para operações de pesquisa realizadas durante um determinado período de tempo. As datas de início e fim são especificadas num formato de data.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Ou, pode utilizar funções de data para especificar o intervalo de datas, como os últimos 14 dias.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Pode procurar as ações tomadas por um determinado utilizador.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Pode filtrar para operações falhadas.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Pode concentrar-se num erro olhando para a mensagem de estado dessa entrada.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Pode selecionar valores específicos para limitar os dados que são devolvidos.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

Dependendo da hora de início especificada, os comandos anteriores podem devolver uma longa lista de operações para o grupo de recursos. Pode filtrar os resultados para o que procura fornecendo critérios de pesquisa. Por exemplo, pode filtrar pelo tipo de funcionamento.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Pode utilizar o Gráfico de Recursos para ver o histórico de alteração de um recurso. Para obter mais informações, consulte [Obter alterações de recursos.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="azure-cli"></a>CLI do Azure

Para obter as entradas de registo, execute o comando da [lista de registos de atividade do monitor az](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) com uma compensação para indicar o tempo.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

O exemplo a seguir mostra como utilizar o registo de atividades para operações de pesquisa realizadas durante um determinado período de tempo. As datas de início e fim são especificadas num formato de data.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Pode procurar as ações tomadas por um determinado utilizador, mesmo para um grupo de recursos que já não existe.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Pode filtrar para operações falhadas.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Pode concentrar-se num erro olhando para a mensagem de estado dessa entrada.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Pode selecionar valores específicos para limitar os dados que são devolvidos.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

Dependendo da hora de início especificada, os comandos anteriores podem devolver uma longa lista de operações para o grupo de recursos. Pode filtrar os resultados para o que procura fornecendo critérios de pesquisa. Por exemplo, pode filtrar pelo tipo de funcionamento.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Pode utilizar o Gráfico de Recursos para ver o histórico de alteração de um recurso. Para obter mais informações, consulte [Obter alterações de recursos.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="rest-api"></a>API REST

As operações REST para trabalhar com o registo de atividades fazem parte da [API REST das Informações](/rest/api/monitor/). Para obter eventos do registo de atividades, veja [Listar os eventos de gestão numa subscrição](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Passos seguintes

* Os registos de atividades Azure podem ser usados com Power BI para obter maiores informações sobre as ações na sua subscrição. Ver [e analisar Registos de AtividadeS Azure em Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Para saber sobre a definição de políticas de segurança, consulte [o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* Para ver mais detalhes sobre as alterações às suas aplicações a partir da camada de infraestrutura até à implementação da aplicação, consulte [a Análise de Alteração de Aplicações de Utilização no Azure Monitor](../../azure-monitor/app/change-analysis.md).
* Para saber mais sobre os comandos de visualização das operações de implantação, consulte [as operações de implantação da visualização](../templates/deployment-history.md).
* Para aprender a prevenir supressões num recurso para todos os utilizadores, consulte [os recursos de Bloqueio com o Azure Resource Manager](lock-resources.md).
* Para ver a lista de operações disponíveis para cada fornecedor de Gestor de Recursos da Microsoft Azure, consulte [as operações do fornecedor de recursos Azure](../../role-based-access-control/resource-provider-operations.md)
