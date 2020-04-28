---
title: Ver registos de atividade do Azure para monitorizar recursos
description: Utilize os registos de atividade para rever as ações e erros dos utilizadores. Mostra o portal Azure PowerShell, Azure CLI e REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 9511090099894fad6708843f106570ed029f0c1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75478139"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Ver registos de atividade para monitorizar ações sobre recursos

Através dos registos de atividades, pode determinar:

* que operações foram tomadas sobre os recursos na sua subscrição
* que iniciou a operação
* quando a operação ocorreu
* o estado da operação
* os valores de outras propriedades que podem ajudá-lo a pesquisar a operação

O registo de atividade contém todas as operações de escrita (PUT, POST, DELETE) para os seus recursos. Não inclui operações de leitura (GET). Para obter uma lista de ações de recursos, consulte as operações do Fornecedor de Recursos do Gestor de [Recursos do Azure.](../../role-based-access-control/resource-provider-operations.md) Pode utilizar os registos de atividade para encontrar um erro ao resolver um problema ou para monitorizar a forma como um utilizador na sua organização alterou um recurso.

Os registos de atividades são mantidos durante 90 dias. Pode consultar qualquer intervalo de datas, desde que a data de início não seja superior a 90 dias no passado.

Pode obter informações dos registos de atividade através do portal, PowerShell, Azure CLI, Insights REST API ou [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Portal do Azure

Para ver os registos de atividade através do portal, siga estes passos:

1. No menu do portal Azure, selecione **Monitor,** ou procure e selecione **Monitor** a partir de qualquer página.

    ![Selecionar monitor](./media/view-activity-logs/select-monitor-from-menu.png)

1. Selecione **Registo de Atividade**.

    ![Selecione o registo de atividade](./media/view-activity-logs/select-activity-log.png)

1. Vê um resumo das operações recentes. Aplica-se um conjunto de filtros predefinido sintetizado nas operações. Note que a informação sobre o resumo inclui quem iniciou a ação e quando aconteceu.

    ![Ver resumo das operações recentes](./media/view-activity-logs/audit-summary.png)

1. Para executar rapidamente um conjunto de filtros pré-definidos, selecione **Quick Insights**.

    ![Selecione insights rápidos](./media/view-activity-logs/select-quick-insights.png)

1. Selecione uma das opções. Por exemplo, selecione **implementações falhadas** para ver erros de implementações.

    ![Selecione implementações falhadas](./media/view-activity-logs/select-failed-deployments.png)

1. Note que os filtros foram alterados para se concentrarem em erros de implementação nas últimas 24 horas. Apenas são apresentadas operações que correspondam aos filtros.

    ![Ver filtros](./media/view-activity-logs/view-filters.png)

1. Para se concentrar em operações específicas, altere os filtros ou aplique novos. Por exemplo, a imagem que se segue mostra um novo valor para o tipo **Timespan** e **Recurso** é definido para contas de armazenamento.

    ![Definir opções de filtro](./media/view-activity-logs/set-filter.png)

1. Se precisar de executar a consulta mais tarde, selecione **filtros de corrente Pin**.

    ![Filtros pin](./media/view-activity-logs/pin-filters.png)

1. Dê um nome ao filtro.

    ![Filtros de nome](./media/view-activity-logs/name-filters.png)

1. O filtro está disponível no painel de instrumentos. No menu do portal do Azure, selecione **Dashboard**.

    ![Mostrar filtro no painel de instrumentos](./media/view-activity-logs/activity-log-on-dashboard.png)

1. A partir do portal, pode ver alterações a um recurso. Volte à vista padrão no Monitor e selecione uma operação que envolva a alteração de um recurso.

    ![Selecione operação](./media/view-activity-logs/select-operation.png)

1. Selecione **o histórico de alterar (Pré-visualização)** e escolha uma das operações disponíveis.

    ![Selecione histórico de mudança](./media/view-activity-logs/select-change-history.png)

1. As alterações no recurso são apresentadas.

    ![Mostrar alterações](./media/view-activity-logs/show-changes.png)

Para saber mais sobre a história da mudança, consulte [Obter alterações de recursos.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para recuperar entradas de registo, faça o comando **Get-AzLog.** Fornece parâmetros adicionais para filtrar a lista de entradas. Se não especificar o início e o fim, as inscrições para os últimos sete dias são devolvidas.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

O exemplo seguinte mostra como utilizar o registo de atividade para operações de investigação realizadas durante um determinado período de tempo. As datas de início e fim são especificadas num formato de data.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Ou, pode utilizar funções de data para especificar o intervalo de data, como os últimos 14 dias.

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

Pode focar-se num erro olhando para a mensagem de estado dessa entrada.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Pode selecionar valores específicos para limitar os dados que são devolvidos.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

Dependendo da hora de início que especificar, os comandos anteriores podem devolver uma longa lista de operações para o grupo de recursos. Pode filtrar os resultados para o que procura, fornecendo critérios de pesquisa. Por exemplo, pode filtrar pelo tipo de funcionamento.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Pode usar o Resource Graph para ver o histórico de mudanças para um recurso. Para mais informações, consulte [Obter alterações de recursos](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>CLI do Azure

Para recuperar entradas de registo, faça o comando da [lista de registos de atividade az](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) monitor com uma compensação para indicar o tempo.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

O exemplo seguinte mostra como utilizar o registo de atividade para operações de investigação realizadas durante um determinado período de tempo. As datas de início e fim são especificadas num formato de data.

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

Pode focar-se num erro olhando para a mensagem de estado dessa entrada.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Pode selecionar valores específicos para limitar os dados que são devolvidos.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

Dependendo da hora de início que especificar, os comandos anteriores podem devolver uma longa lista de operações para o grupo de recursos. Pode filtrar os resultados para o que procura, fornecendo critérios de pesquisa. Por exemplo, pode filtrar pelo tipo de funcionamento.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Pode usar o Resource Graph para ver o histórico de mudanças para um recurso. Para mais informações, consulte [Obter alterações de recursos](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>API REST

As operações REST para trabalhar com o registo de atividade fazem parte da [API Insights REST](/rest/api/monitor/). Para recuperar eventos de registo de atividades, consulte [Listar os eventos de gestão numa subscrição](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Passos seguintes

* Os registos da Atividade Azure podem ser usados com o Power BI para obter mais informações sobre as ações na sua subscrição. Ver e analisar Registos de [Atividade do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Para aprender sobre a definição de políticas de segurança, consulte [o Controlo de Acesso baseado em Papel Azure](../../role-based-access-control/role-assignments-portal.md).
* Para ver mais detalhes sobre as alterações nas suas aplicações desde a camada de infraestrutura até à implementação da aplicação, consulte a Análise de [Alterações de Aplicações no Monitor Azure](../../azure-monitor/app/change-analysis.md).
* Para conhecer os comandos para visualizar operações de implantação, consulte as operações de [implantação do View](../templates/deployment-history.md).
* Para aprender a evitar supressões num recurso para todos os utilizadores, consulte os recursos de bloqueio com o Gestor de [Recursos Azure](lock-resources.md).
* Para ver a lista de operações disponíveis para cada fornecedor do Microsoft Azure Resource Manager, consulte as operações do Fornecedor de Recursos do Gestor de [Recursos do Azure](../../role-based-access-control/resource-provider-operations.md)
