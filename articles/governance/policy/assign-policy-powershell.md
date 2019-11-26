---
title: 'Quickstart: New policy assignment with PowerShell'
description: In this quickstart, you use Azure PowerShell to create an Azure Policy assignment to identify non-compliant resources.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 3ce823a7abfe16e4433128dcdfe073dfcfaeba50
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482397"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Quickstart: Create a policy assignment to identify non-compliant resources using Azure PowerShell

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos. In this quickstart, you create a policy assignment to identify virtual machines that aren't using managed disks. When complete, you'll identify virtual machines that are _non-compliant_.

The Azure PowerShell module is used to manage Azure resources from the command line or in scripts.
This guide explains how to use Az module to create a policy assignment.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Before you start, make sure that the latest version of Azure PowerShell is installed. See [Install Azure PowerShell module](/powershell/azure/install-az-ps) for detailed information.

- Register the Azure Policy Insights resource provider using Azure PowerShell. Registar o fornecedor de recursos assegura que a sua subscrição funciona com o mesmo. To register a resource provider, you must have permission to the register resource provider operation. Esta operação está incluída nas funções de Contribuinte e Proprietário. Execute o seguinte comando para registar o fornecedor de recursos:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Para obter mais informações sobre como registar e visualizar os fornecedores de recursos, veja [Fornecedores e Tipos de Recursos](../../azure-resource-manager/resource-manager-supported-services.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

In this quickstart, you create a policy assignment for the _Audit VMs without managed disks_ definition. This policy definition identifies virtual machines not using managed disks.

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

- **Nome** – O nome real da atribuição. Neste exemplo, foi utilizado _audit-vm-manageddisks_.
- **DisplayName** – O nome da atribuição de política a apresentar. In this case, you're using _Audit VMs without managed disks Assignment_.
- **Definição** – A definição de política, com base na qual está a utilizar para criar a atribuição. In this case, it's the ID of policy definition _Audit VMs that do not use managed disks_.
- **Âmbito** – Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Pode ir de uma subscrição aos grupos de recursos. Não se esqueça de substituir &lt;âmbito&gt; pelo nome do seu grupo de recursos.

You're now ready to identify non-compliant resources to understand the compliance state of your environment.

## <a name="identify-non-compliant-resources"></a>Identificar recursos não compatíveis

Utilize as seguintes informações para identificar recursos que não estão em conformidade com a atribuição de política que criou. Execute os seguintes comandos:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

For more information about getting policy state, see [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

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

The results match what you see in the **Resource compliance** tab of a policy assignment in the Azure portal view.

## <a name="clean-up-resources"></a>Limpar recursos

To remove the assignment created, use the following command:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, atribuiu uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

To learn more about assigning policies to validate that new resources are compliant, continue to the tutorial for:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)