---
title: Criar política para auditar recursos com o PowerShell
description: Use Azure PowerShell para criar uma atribuição de Azure Policy para identificar recursos sem conformidade.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: azure-policy
ms.openlocfilehash: 55b7d8509fcf681438b377aa43f5f8aa9bac158d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255910"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Início rápido: criar uma atribuição de política para identificar recursos sem conformidade usando Azure PowerShell

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos. Neste guia de início rápido, você cria uma atribuição de política para identificar as máquinas virtuais que não estão usando discos gerenciados. Ao concluir, você identificará as máquinas virtuais que *não estão em conformidade*.

O módulo Azure PowerShell é usado para gerenciar recursos do Azure na linha de comando ou em scripts.
Este guia explica como usar o módulo AZ para criar uma atribuição de política.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de começar, verifique se a versão mais recente do Azure PowerShell está instalada. Consulte [instalar Azure PowerShell módulo](/powershell/azure/install-az-ps) para obter informações detalhadas.
- Registre o provedor de recursos do Azure Policy insights usando Azure PowerShell. Registar o fornecedor de recursos assegura que a sua subscrição funciona com o mesmo. Para registrar um provedor de recursos, você deve ter permissão para a operação registrar provedor de recursos. Esta operação está incluída nas funções de Contribuinte e Proprietário. Execute o seguinte comando para registar o fornecedor de recursos:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Para obter mais informações sobre como registar e ver os fornecedores de recursos, veja [Resource Providers and Types](../../azure-resource-manager/resource-manager-supported-services.md) (Fornecedores e Tipos de Recursos)

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, você cria uma atribuição de política para a definição *auditar VMs sem Managed disks* . Essa definição de política identifica as máquinas virtuais que não usam discos gerenciados.

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
- **DisplayName** – O nome da atribuição de política a apresentar. Nesse caso, você está usando as *VMs de auditoria sem a atribuição de discos gerenciados*.
- **Definição** – A definição de política, com base na qual está a utilizar para criar a atribuição. Nesse caso, é a ID das VMs de auditoria de definição de política *que não usam discos gerenciados*.
- **Âmbito** – Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Pode ir de uma subscrição aos grupos de recursos. Não se esqueça de substituir &lt;âmbito&gt; pelo nome do seu grupo de recursos.

Agora você está pronto para identificar recursos sem conformidade para entender o estado de conformidade do seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos não compatíveis

Utilize as seguintes informações para identificar recursos que não estão em conformidade com a atribuição de política que criou. Execute os seguintes comandos:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Para obter mais informações sobre como obter o estado da política, consulte [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

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

Os resultados correspondem ao que você vê na guia **conformidade de recursos** de uma atribuição de política na exibição portal do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover a atribuição criada, use o seguinte comando:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, atribuiu uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Para saber mais sobre como atribuir políticas para validar que novos recursos estão em conformidade, prossiga para o tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)