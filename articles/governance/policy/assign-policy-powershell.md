---
title: 'Quickstart: Nova atribuição de políticas com a PowerShell'
description: Neste arranque rápido, você usa a Azure PowerShell para criar uma atribuição de Política Azure para identificar recursos não conformes.
ms.date: 08/17/2020
ms.topic: quickstart
ms.openlocfilehash: e941b74101308af703f243197fb4043f8f32d233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88548417"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Quickstart: Criar uma atribuição de política para identificar recursos não conformes usando a Azure PowerShell

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos. Neste arranque rápido, cria-se uma atribuição de políticas para identificar máquinas virtuais que não estão a usar discos geridos. Quando estiver concluído, identificará máquinas virtuais que _não são compatíveis._

O módulo Azure PowerShell é utilizado para gerir os recursos do Azure a partir da linha de comando ou em scripts.
Este guia explica como usar o módulo Az para criar uma atribuição de política.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Antes de começar, certifique-se de que a versão mais recente do Azure PowerShell está instalada. Consulte [o módulo Azure PowerShell](/powershell/azure/install-az-ps) para obter informações detalhadas.

- Registe o fornecedor de recursos Azure Policy Insights utilizando a Azure PowerShell. Registar o fornecedor de recursos assegura que a sua subscrição funciona com o mesmo. Para registar um fornecedor de recursos, tem de ter autorização para a operação do fornecedor de recursos de registo. Esta operação está incluída nas funções de Contribuinte e Proprietário. Execute o seguinte comando para registar o fornecedor de recursos:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Para obter mais informações sobre o registo e visualização de fornecedores de recursos, consulte [Fornecedores e Tipos de Recursos.](../../azure-resource-manager/management/resource-providers-and-types.md)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste arranque rápido, cria-se uma atribuição de política para os _VMs de auditoria sem_ definição de discos geridos. Esta definição de política identifica máquinas virtuais que não utilizam discos geridos.

Execute os seguintes comandos para criar uma nova atribuição de política:

```azurepowershell-interactive
# Get a reference to the resource group that is the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition to assign
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Os comandos anteriores utilizam as seguintes informações:

- **Nome** – O nome real da atribuição. Neste exemplo, foi utilizado _audit-vm-manageddisks_.
- **DisplayName** – O nome da atribuição de política a apresentar. Neste caso, está a utilizar _VMs de auditoria sem serviço de discos geridos_.
- **Definição** – A definição de política, com base na qual está a utilizar para criar a atribuição. Neste caso, é o ID de definição de política _Audit VMs que não usam discos geridos_.
- **Âmbito** – Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Pode ir desde uma subscrição a grupos de recursos. Não se esqueça de substituir &lt;âmbito&gt; pelo nome do seu grupo de recursos.

Está agora pronto para identificar recursos não conformes para entender o estado de conformidade do seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos que não estão em conformidade

Utilize as seguintes informações para identificar recursos que não estão em conformidade com a atribuição de política que criou. Execute os seguintes comandos:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Para obter mais informações sobre o estado político, consulte [o Estado-AzPolicyState.](/powershell/module/az.policyinsights/Get-AzPolicyState)

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

Os resultados coincidem com o que vê no **separador** de conformidade de recursos de uma atribuição de políticas na vista do portal Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a atribuição criada, utilize o seguinte comando:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, atribuiu uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, continue ao tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)