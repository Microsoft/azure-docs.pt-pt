---
title: Erros de quota
description: Descreve como resolver erros de quota de recursos ao implementar recursos com o Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 75e8abf31d035a1e3a106bc0c6561624762db5d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530425"
---
# <a name="resolve-errors-for-resource-quotas"></a>Resolver erros de quotas de recursos

Este artigo descreve erros de quota que pode encontrar ao utilizar recursos.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintoma

Se implementar um modelo que crie recursos que excedam as suas quotas Azure, obtém-se um erro de implantação que se parece com:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Ou, pode ver:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Causa

As quotas são aplicadas por grupo de recursos, subscrições, contas e outros âmbitos. Por exemplo, a subscrição pode ser configurada para limitar o número de núcleos de uma região. Se tentar implementar uma máquina virtual com mais núcleos do que o montante permitido, receberá um erro a indicar que a quota foi excedida.
Para obter informações completas sobre as quotas, consulte [os limites de subscrição e serviço da Azure, quotas e restrições.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="azure-cli"></a>CLI do Azure

Para o Azure CLI, utilize o `az vm list-usage` comando para encontrar quotas de máquinas virtuais.

```azurecli
az vm list-usage --location "South Central US"
```

Que devolve:

```output
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

Para powerShell, utilize o comando **Get-AzVMUsage** para encontrar quotas de máquinas virtuais.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Que devolve:

```output
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Solução

Para solicitar um aumento de quota, vá ao portal e apresente um problema de suporte. Na questão do apoio, solicite um aumento da sua quota para a região em que pretende implantar.

> [!NOTE]
> Lembre-se que para os grupos de recursos, a quota é para cada região, não para toda a subscrição. Se precisa de implantar 30 núcleos nos EUA Ocidentais, tem de pedir 30 núcleos de gestores de recursos nos EUA Ocidentais. Se precisar de implantar 30 núcleos em qualquer uma das regiões a que tem acesso, deverá pedir 30 núcleos de Gestor de Recursos em todas as regiões.
>
>

1. Selecione **Subscrições**.

   ![A captura de ecrã mostra o menu Azure Portal com subscrições selecionadas.](./media/error-resource-quota/subscriptions.png)

2. Selecione a subscrição que precisa de uma quota maior.

   ![Selecionar subscrição](./media/error-resource-quota/select-subscription.png)

3. Selecione **Usage + quotas**

   ![Selecione a utilização e as quotas](./media/error-resource-quota/select-usage-quotas.png)

4. No canto superior direito, selecione **Pedir aumento**.

   ![Aumento do pedido](./media/error-resource-quota/request-increase.png)

5. Preencha os formulários para o tipo de quota que precisa de aumentar.

   ![Preencha o formulário](./media/error-resource-quota/forms.png)