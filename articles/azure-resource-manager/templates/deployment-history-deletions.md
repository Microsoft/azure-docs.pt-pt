---
title: Eliminações do histórico de implementações
description: Descreve como o Azure Resource Manager elimina automaticamente as implementações do histórico de implementação. As implementações são eliminadas quando o histórico está perto de ultrapassar o limite de 800.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c16b71646e20b71c0d0ca8c9f8e028773983022f
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84673994"
---
# <a name="automatic-deletions-from-deployment-history"></a>Supressões automáticas do histórico de implantação

Sempre que implementa um modelo, as informações sobre a implementação são escritas para o histórico de implementação. Cada grupo de recursos está limitado a 800 implementações no seu histórico de implantação.

A partir de junho de 2020, o Azure Resource Manager elimina automaticamente as implementações do seu histórico à medida que se aproxima do limite. A eliminação automática é uma mudança do comportamento do passado. Anteriormente, tinha de eliminar manualmente as implementações do histórico de implementação para evitar um erro.

> [!NOTE]
> Excluir uma implantação da história não afeta nenhum dos recursos que foram mobilizados.
>
> Se tiver um [bloqueio CanNotDelete](../management/lock-resources.md) num grupo de recursos, as implementações desse grupo de recursos não podem ser eliminadas. Tem de remover o cadeado para tirar partido das supressões automáticas no histórico de implantação.

## <a name="when-deployments-are-deleted"></a>Quando as implementações são eliminadas

As implementações são eliminadas do seu histórico de implantação quando atinge 790 implementações. O Azure Resource Manager elimina um pequeno conjunto das implementações mais antigas para abrir espaço para futuras implementações. A maior parte da sua história permanece inalterada. As implementações mais antigas são sempre eliminadas primeiro.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Supressões da história da implantação":::

Além das implementações, também desencadeia supressões quando executa o [e-se operação](template-deploy-what-if.md) ou valida uma implementação.

Quando se dá a uma implantação o mesmo nome que um na história, repõe-se o seu lugar na história. A implantação muda-se para o lugar mais recente da história. Também reinicia o lugar de uma implantação quando [retroceda para essa implantação](rollback-on-error.md) após um erro.

> [!NOTE]
> Se o seu grupo de recursos já estiver no limite de 800, a sua próxima implementação falha com um erro. O processo de eliminação automática começa imediatamente. Pode tentar o seu destacamento novamente depois de uma curta espera.

## <a name="opt-out-of-automatic-deletions"></a>Opte por excluir as supressões automáticas

Pode optar por não excluir as supressões automáticas da história. **Utilize esta opção apenas quando pretender gerir o histórico de implementação por si mesmo.** O limite de 800 destacamentos na história ainda está imposto. Se exceder 800 implementações, receberá um erro e a sua implantação falhará.

Para desativar as eliminações automáticas, registe a bandeira da `Microsoft.Resources/DisableDeploymentGrooming` funcionalidade. Quando regista a bandeira de funcionalidade, opta por não excluir as supressões automáticas para toda a subscrição do Azure. Não pode optar por apenas um determinado grupo de recursos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para powerShell, utilize [a Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Para ver o estado atual da sua subscrição, utilize:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para O Azure CLI, utilize [o registo de recursos Az](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Para ver o estado atual da sua subscrição, utilize:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
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

---

## <a name="next-steps"></a>Próximos passos

* Para saber mais sobre a visualização do histórico de implementação, consulte [o histórico de implementação da Azure Resource Manager](deployment-history.md).
