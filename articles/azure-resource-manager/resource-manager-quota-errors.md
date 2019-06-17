---
title: Erros de quota do Azure | Documentos da Microsoft
description: Descreve como resolver erros de quota de recursos.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 7938f2c47e4af8d8804191fbb9e55b379f9554ef
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60390230"
---
# <a name="resolve-errors-for-resource-quotas"></a>Resolver erros de quotas de recursos

Este artigo descreve o que poderá encontrar ao implementar os recursos de erros de quota.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintoma

Se implementar um modelo que cria recursos que excederem as quotas do Azure, obtém um erro de implementação que é semelhante a:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Em alternativa, poderá ver:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Causa

As quotas são aplicadas por grupo de recursos, subscrições, contas e outros âmbitos. Por exemplo, a sua subscrição pode ser configurada para limitar o número de núcleos para uma região. Se está tentando implementar uma máquina virtual com o maior número de núcleos que a quantia permitido, receberá um erro a indicar que a quota foi excedida.
Para informações sobre a quota completa, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="azure-cli"></a>CLI do Azure

Para a CLI do Azure, utilize o `az vm list-usage` comando para encontrar as quotas de máquina virtual.

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

Para o PowerShell, utilize o **Get-AzVMUsage** comando para encontrar as quotas de máquina virtual.

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

Para pedir um aumento de quota, aceda ao portal e um problema de suporte de ficheiros. Na edição de suporte, pedir um aumento da sua quota para a região na qual pretende implementar.

> [!NOTE]
> Lembre-se de que para grupos de recursos, a quota para cada região individual, não para a subscrição completa. Se precisar de implementar 30 núcleos na região E.U.A. oeste, terá de pedir 30 núcleos de Gestor de recursos na região E.U.A. oeste. Se precisar de implementar em qualquer uma das regiões às quais tem acesso de 30 núcleos, deve perguntar para 30 núcleos de Gestor de recursos em todas as regiões.
>
>

1. Selecione **Subscrições**.

   ![Subscriptions](./media/resource-manager-quota-errors/subscriptions.png)

2. Selecione a subscrição que precisa de uma quota maior.

   ![Selecionar subscrição](./media/resource-manager-quota-errors/select-subscription.png)

3. Selecione **utilização + quotas**

   ![Selecione a utilização e quotas](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. No canto superior direito, selecione **pedir aumento**.

   ![Pedir aumento](./media/resource-manager-quota-errors/request-increase.png)

5. Preencha os formulários para o tipo de quota que precisa de aumentar.

   ![Preencha o formulário](./media/resource-manager-quota-errors/forms.png)