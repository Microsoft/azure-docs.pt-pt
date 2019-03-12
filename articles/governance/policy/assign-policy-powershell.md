---
title: Criar uma política para recursos não compatíveis com o Azure PowerShell
description: Utilize o Azure PowerShell para criar uma atribuição de política do Azure para identificar recursos incompatíveis.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 103e0e09d3ac5f3d3f6bb8d8d44e25dd8d8d87e6
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726979"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Criar uma atribuição de política para identificar recursos incompatíveis com o Azure PowerShell

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos. Neste início rápido, vai criar uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos. Quando terminar, vai poder identificar máquinas virtuais que estejam *incompatíveis*.

O módulo Azure PowerShell é utilizado para gerir recursos do Azure a partir da linha de comandos ou em scripts.
Este guia explica como utilizar o módulo de Az para criar uma atribuição de política.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Antes de começar, certifique-se de que a versão mais recente do Azure PowerShell está instalada. Ver [módulo de instalar o Azure PowerShell](/powershell/azure/install-az-ps) para obter informações detalhadas.
- Utilize o Azure PowerShell para registar o fornecedor de recursos do Policy Insights. Registar o fornecedor de recursos assegura que a sua subscrição funciona com o mesmo. Para registar um fornecedor de recursos, tem de ter permissão para a operação de fornecedor de recursos de registo. Esta operação está incluída nas funções de Contribuinte e Proprietário. Execute o seguinte comando para registar o fornecedor de recursos:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Para obter mais informações sobre como registar e ver os fornecedores de recursos, veja [Resource Providers and Types](../../azure-resource-manager/resource-manager-supported-services.md) (Fornecedores e Tipos de Recursos)

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste início rápido, vai criar uma atribuição de política para o *VMs de auditoria sem discos geridos* definição. Esta definição de política identifica as máquinas virtuais não utilizar discos geridos.

Execute os seguintes comandos para criar uma nova atribuição de política:

```azurepowershell-interactive
# Get a reference to the resource group that will be the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition that will be assigned
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Os comandos anteriores utilizam as seguintes informações:

- **Nome** – O nome real da atribuição. Neste exemplo, foi utilizado *audit-vm-manageddisks*.
- **DisplayName** – O nome da atribuição de política a apresentar. Neste caso, está usando *VMs de auditoria sem discos geridos atribuição*.
- **Definição** – A definição de política, com base na qual está a utilizar para criar a atribuição. Neste caso, é o ID de definição de política *VMs de auditoria que não utilizam discos geridos*.
- **Âmbito** – Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Pode ir de uma subscrição aos grupos de recursos. Não se esqueça de substituir &lt;âmbito&gt; pelo nome do seu grupo de recursos.

Agora, está pronto para identificar recursos incompatíveis para compreender o estado de compatibilidade do seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos não compatíveis

Utilize as seguintes informações para identificar recursos que não estão em conformidade com a atribuição de política que criou. Execute os seguintes comandos:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Para obter mais informações sobre como obter o estado de política, consulte [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

Os resultados assemelham-se ao seguinte exemplo:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

Os resultados corresponderem a que vê na **conformidade do recurso** separador de uma atribuição de política na vista do portal do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover a atribuição de criado, utilize o seguinte comando:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Passos Seguintes

Neste guia de introdução, atribuiu uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, avance para o tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)