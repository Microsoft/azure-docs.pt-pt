---
title: Eliminações do histórico de implementações
description: Descreve como o Azure Resource Manager elimina automaticamente as implementações do histórico de implementação. As implementações são eliminadas quando o histórico está perto de ultrapassar o limite de 800.
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 13c65f3311e308708034bb5befb7e3c3ee158d38
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91652487"
---
# <a name="automatic-deletions-from-deployment-history"></a>Supressões automáticas do histórico de implantação

Sempre que implementa um modelo, as informações sobre a implementação são escritas para o histórico de implementação. Cada grupo de recursos está limitado a 800 implementações no seu histórico de implantação.

O Azure Resource Manager elimina automaticamente as implementações do seu histórico à medida que se aproxima do limite. A eliminação automática é uma mudança do comportamento do passado. Anteriormente, tinha de eliminar manualmente as implementações do histórico de implementação para evitar um erro. Esta alteração foi implementada no dia 6 de agosto de 2020.

**As supressões automáticas são suportadas para implantações de grupos de recursos. Atualmente, as implementações na história para [subscrição,](deploy-to-subscription.md) [grupo de gestão](deploy-to-management-group.md)e implantações [de inquilinos](deploy-to-tenant.md) não são automaticamente eliminadas.**

> [!NOTE]
> Excluir uma implantação da história não afeta nenhum dos recursos que foram mobilizados.

## <a name="when-deployments-are-deleted"></a>Quando as implementações são eliminadas

As implementações são eliminadas do seu histórico quando ultrapassa as 775 implementações. O Gestor de Recursos Azure elimina as implementações até que o histórico seja reduzido a 750. As implementações mais antigas são sempre eliminadas primeiro.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Supressões da história da implantação":::

> [!NOTE]
> O número inicial (775) e o número final (750) estão sujeitos a alterações.
>
> Se o seu grupo de recursos já estiver no limite de 800, a sua próxima implementação falha com um erro. O processo de eliminação automática começa imediatamente. Pode tentar o seu destacamento novamente depois de uma curta espera.

Além das implementações, também desencadeia supressões quando executa o [e-se operação](template-deploy-what-if.md) ou valida uma implementação.

Quando se dá a uma implantação o mesmo nome que um na história, repõe-se o seu lugar na história. A implantação muda-se para o lugar mais recente da história. Também reinicia o lugar de uma implantação quando [retroceda para essa implantação](rollback-on-error.md) após um erro.

## <a name="remove-locks-that-block-deletions"></a>Remover fechaduras que bloqueiam supressões

Se tiver um [bloqueio CanNotDelete](../management/lock-resources.md) num grupo de recursos, as implementações desse grupo de recursos não podem ser eliminadas. Tem de remover o cadeado para tirar partido das supressões automáticas no histórico de implantação.

Para utilizar o PowerShell para eliminar uma fechadura, execute os seguintes comandos:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Para utilizar o Azure CLI para apagar uma fechadura, execute os seguintes comandos:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="opt-out-of-automatic-deletions"></a>Opte por excluir as supressões automáticas

Pode optar por não excluir as supressões automáticas da história. **Utilize esta opção apenas quando pretender gerir o histórico de implementação por si mesmo.** O limite de 800 destacamentos na história ainda está imposto. Se exceder 800 implementações, receberá um erro e a sua implantação falhará.

Para desativar as eliminações automáticas, registe a bandeira da `Microsoft.Resources/DisableDeploymentGrooming` funcionalidade. Quando regista a bandeira de funcionalidade, opta por não excluir as supressões automáticas para toda a subscrição do Azure. Não pode optar por apenas um determinado grupo de recursos. Para reencontrar as supressões automáticas, desmarça a bandeira de características.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para powerShell, utilize [a Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Para ver o estado atual da sua subscrição, utilize:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Para reencar as supressões automáticas, utilize Azure REST API ou Azure CLI.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para O Azure CLI, utilize [o registo de recursos Az](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Para ver o estado atual da sua subscrição, utilize:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Para reencontrar as supressões automáticas, utilize [o registo de recurso az](/cli/azure/feature#az-feature-unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Para REST API, utilize [funcionalidades - Registar.](/rest/api/resources/features/register)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Para ver o estado atual da sua subscrição, utilize:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Para reencar as supressões automáticas reensáveis, utilize [funcionalidades - Não registar](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a visualização do histórico de implementação, consulte [o histórico de implementação da Azure Resource Manager](deployment-history.md).
