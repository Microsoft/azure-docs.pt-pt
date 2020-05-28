---
title: Eliminações do histórico de implantação
description: Descreve como o Gestor de Recursos Azure elimina automaticamente as implementações do histórico de implementação. As implantações são eliminadas quando o histórico está perto de exceder o limite de 800.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 3e48b2da00986da00f7597cf887aa74f84587710
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122452"
---
# <a name="automatic-deletions-from-deployment-history"></a>Eliminações automáticas do histórico de implantação

Sempre que implementa um modelo, a informação sobre a implementação é escrita para o histórico de implementação. Cada grupo de recursos está limitado a 800 implantações no seu histórico de implantação.

A partir de junho de 2020, o Gestor de Recursos Azure elimina automaticamente as implementações do seu histórico à medida que se aproxima do limite. A eliminação automática é uma mudança do comportamento passado. Anteriormente, teve de eliminar manualmente as implementações do histórico de implementação para evitar um erro.

> [!NOTE]
> Apagar uma implantação da história não afeta nenhum dos recursos que foram implantados.

## <a name="when-deployments-are-deleted"></a>Quando as implementações são eliminadas

As implementações são eliminadas do seu histórico de implantação apenas quando se aproxima do limite de 800. O Azure Resource Manager elimina um pequeno conjunto das mais antigas implementações para limpar espaço para futuras implementações. A maior parte da sua história permanece inalterada. As implementações mais antigas são sempre eliminadas primeiro.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Eliminações do histórico de implantação":::

Além das implementações, também desencadeia supressões quando executa a [operação "e se"](template-deploy-what-if.md) ou valida uma implementação.

Quando se dá a uma implantação o mesmo nome que um na história, repõe-se o seu lugar na história. O destacamento muda-se para o lugar mais recente da história. Também repõe o lugar de uma implantação quando [volta para a implantação](rollback-on-error.md) após um erro.

## <a name="opt-out-of-automatic-deletions"></a>Opte por eliminações automáticas

Pode optar por exclusões automáticas da história. **Utilize esta opção apenas quando quiser gerir o histórico de implantação por si mesmo.** O limite de 800 destacamentos na história ainda é cumprido. Se ultrapassar 800 implementações, receberá um erro e a sua implementação falhará.

Para desativar as supressões automáticas, registe a bandeira da `Microsoft.Resources/DisableDeploymentGrooming` característica. Ao registar a bandeira de recurso, opte por eliminações automáticas para toda a subscrição do Azure. Não pode optar por um grupo de recursos em particular.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para o PowerShell, utilize o [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Para ver o estado atual da sua subscrição, utilize:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para o Azure CLI, utilize o registo de [funcionalidades Az](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Para ver o estado atual da sua subscrição, utilize:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Para rest API, utilize [Funcionalidades - Registar](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Para ver o estado atual da sua subscrição, utilize:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Próximos passos

* Para aprender sobre a visualização do histórico de implementação, consulte o [histórico de implementação do View com o Gestor](deployment-history.md)de Recursos Azure .
