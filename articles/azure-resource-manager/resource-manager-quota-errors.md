---
title: Erros de cota
description: Descreve como resolver erros de cota de recursos ao implantar recursos com Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 8760b027d815dfc70000db5aea534bf10f60b759
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149430"
---
# <a name="resolve-errors-for-resource-quotas"></a>Resolver erros de cotas de recursos

Este artigo descreve os erros de cota que você pode encontrar ao implantar recursos.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintoma

Se você implantar um modelo que cria recursos que excedem suas cotas do Azure, obterá um erro de implantação semelhante a:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Ou você pode ver:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Causa

As quotas são aplicadas por grupo de recursos, subscrições, contas e outros âmbitos. Por exemplo, a subscrição pode ser configurada para limitar o número de núcleos de uma região. Se tentar implementar uma máquina virtual com mais núcleos do que o montante permitido, receberá um erro a indicar que a quota foi excedida.
Para obter informações completas sobre cota, consulte [assinatura e limites de serviço, cotas e restrições do Azure](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="azure-cli"></a>CLI do Azure

Para CLI do Azure, use o comando `az vm list-usage` para localizar cotas de máquina virtual.

```azurecli
az vm list-usage --location "South Central US"
```

Que retorna:

```azurecli
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

Para o PowerShell, use o comando **Get-AzVMUsage** para localizar cotas de máquina virtual.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Que retorna:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Solução

Para solicitar um aumento de cota, vá para o portal e emita um problema de suporte. No problema de suporte, solicite um aumento na sua cota para a região na qual você deseja implantar.

> [!NOTE]
> Lembre-se de que, para grupos de recursos, a cota é para cada região individual, não para toda a assinatura. Se você precisar implantar 30 núcleos no oeste dos EUA, precisará solicitar 30 núcleos do Resource Manager no oeste dos EUA. Se você precisar implantar 30 núcleos em qualquer uma das regiões às quais tem acesso, solicite 30 núcleos do Resource Manager em todas as regiões.
>
>

1. Selecione **Subscrições**.

   ![Subscrições](./media/resource-manager-quota-errors/subscriptions.png)

2. Selecione a subscrição que precisa de uma quota maior.

   ![Selecionar subscrição](./media/resource-manager-quota-errors/select-subscription.png)

3. Selecionar **uso + cotas**

   ![Selecionar uso e cotas](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. No canto superior direito, selecione **solicitar aumento**.

   ![Aumento da solicitação](./media/resource-manager-quota-errors/request-increase.png)

5. Preencha os formulários para o tipo de quota que precisa de aumentar.

   ![Preencher formulário](./media/resource-manager-quota-errors/forms.png)