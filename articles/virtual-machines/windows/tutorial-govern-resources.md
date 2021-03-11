---
title: Tutorial - Gerir máquinas virtuais com PowerShell
description: Neste tutorial, você aprende a usar a Azure PowerShell para gerir máquinas virtuais Azure aplicando Azure RBAC, polícias, fechaduras e tags
author: tfitzmac
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: f0107a28a655097f983e0711c2ccab19f47f7cae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554314"
---
# <a name="tutorial-learn-about-windows-virtual-machine-management-with-azure-powershell"></a>Tutorial: Saiba mais sobre a gestão de máquinas virtuais do Windows com o Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="understand-scope"></a>Compreender o âmbito

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Neste tutorial, vai aplicar todas as definições de gestão a um grupo de recursos para poder remover facilmente essas definições quando terminar.

Vamos então criar esse grupo de recursos.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Atualmente, o grupo de recursos está vazio.

## <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure

Deve confirmar que os utilizadores na sua organização possuem o nível adequado de acesso a estes recursos. Não vai querer conceder acesso ilimitado aos utilizadores, mas também precisa de confirmar que estes podem fazer o seu trabalho. [O controlo de acesso baseado em funções (Azure RBAC) permite-lhe](../../role-based-access-control/overview.md) gerir quais os utilizadores que têm permissão para completar ações específicas num âmbito.

Para criar e remover atribuições de funções, os utilizadores devem ter acesso `Microsoft.Authorization/roleAssignments/*`. Este acesso é concedido através das funções Proprietário ou Administrador de Acesso dos Utilizadores.

Para gerir soluções de máquina virtual, existem três funções de recursos específicos que fornecem o acesso normalmente necessário:

* [Contribuidor de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Contribuidor de Rede](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Contribuidor de Conta de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Em vez de atribuir funções a utilizadores individuais, muitas vezes, é mais fácil utilizar um grupo do Azure Active Directory que tem utilizadores que precisam de realizar ações semelhantes. Em seguida, atribua esse grupo à função adequada. Neste artigo, utilize um grupo existente para gerir a máquina virtual ou utilize o portal para [criar um grupo do Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Depois de criar um novo grupo ou encontrar um existente, use o comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para atribuir o grupo Azure Ative Directory ao papel de Contribuinte de Máquina Virtual para o grupo de recursos.  

```azurepowershell-interactive
$adgroup = Get-AzADGroup -DisplayName <your-group-name>

New-AzRoleAssignment -ObjectId $adgroup.id `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Se receber um erro a indicar que o **Principal\<guid> não existe no diretório**, o novo grupo ainda não foi propagado pelo Azure Active Directory. Tente executar o comando novamente.

Normalmente, pode repetir o processo para o *Contribuidor de Rede* e o *Contribuidor de Conta de Armazenamento* para confirmar que os utilizadores estão atribuídos para gerir os recursos implementados. Neste artigo, pode ignorar esses passos.

## <a name="azure-policy"></a>Azure Policy

O [Azure Policy](../../governance/policy/overview.md) ajuda-o a certificar-se de que todos os recursos na subscrição cumprem os padrões empresariais. A subscrição já tem várias definições de política. Para ver as definições de política disponíveis, utilize o comando [Get-AzPolicyDefinition:](/powershell/module/az.resources/get-azpolicydefinition)

```azurepowershell-interactive
(Get-AzPolicyDefinition).Properties | Format-Table displayName, policyType
```

Poderá ver as definições de política existentes. O tipo de política é **BuiltIn** ou **Personalizado**. Examine as definições para encontrar aquelas que descrevem uma condição que pretenda atribuir. Neste artigo, pode atribuir políticas para:

* Limitar as localizações de todos os recursos.
* Limitar os SKUs das máquinas virtuais.
* Auditar máquinas virtuais que não utilizam discos geridos.

No exemplo seguinte, obtém três definições de políticas baseadas no nome a apresentar. Usa o comando [new-AzPolicyAssignment](/powershell/module/az.resources/new-azpolicyassignment) para atribuir essas definições ao grupo de recursos. Para algumas políticas, forneça valores de parâmetros para especificar os valores permitidos.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine size SKUs"}
$auditDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Implementar a máquina virtual

Atribuiu funções e políticas, pelo que está pronto para implementar a sua solução. O tamanho predefinido é Standard_DS1_v2, que é um dos seus SKUs permitidos. Ao executar este passo, serão pedidas credenciais. Os valores que introduzir são configurados, como o nome de utilizador e a palavra-passe para a máquina virtual.

```azurepowershell-interactive
New-AzVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Após a conclusão da implementação, pode aplicar mais definições de gestão à solução.

## <a name="lock-resources"></a>Bloquear recursos

Os [bloqueios de recursos](../../azure-resource-manager/management/lock-resources.md) impedem que os utilizadores na sua organização eliminem ou modifiquem acidentalmente recursos críticos. Ao contrário do controlo de acesso baseado em funções, os bloqueios de recursos aplicam uma restrição a todos os utilizadores e a todas as funções. Pode definir o nível do bloqueio para *CanNotDelete* ou *ReadOnly*.

Para bloquear a máquina virtual e o grupo de segurança da rede, utilize o comando [New-AzResourceLock:](/powershell/module/az.resources/new-azresourcelock)

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Para testar os bloqueios, tente executar o seguinte comando:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup
```

Verá um erro a indicar que a operação de eliminação não pode ser concluída devido a um bloqueio. O grupo de recursos só poderá ser eliminado se remover especificamente os bloqueios. Este passo é apresentado em [Limpar recursos](#clean-up-resources).

## <a name="tag-resources"></a>Etiquetar recursos

Aplica [etiquetas](../../azure-resource-manager/management/tag-resources.md) nos seus recursos Azure para organizá-las logicamente por categorias. Cada etiqueta é constituída por um nome e um valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Para aplicar tags numa máquina virtual, utilize o comando [Set-AzResource:](/powershell/module/az.resources/set-azresource)

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Localizar recursos por etiqueta

Para encontrar recursos com um nome e valor de etiqueta, utilize o comando [Get-AzResource:](/powershell/module/az.resources/get-azresource)

```azurepowershell-interactive
(Get-AzResource -Tag @{ Environment="Test"}).Name
```

Pode utilizar os valores devolvidos das tarefas de gestão, como parar todas as máquinas virtuais com um valor de etiqueta.

```azurepowershell-interactive
Get-AzResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzVM
```

### <a name="view-costs-by-tag-values"></a>Ver custos por valores de etiqueta

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

O grupo de segurança de rede bloqueado não pode ser eliminado até que o bloqueio seja removido. Para remover o bloqueio, utilize o comando [Remove-AzResourceLock:](/powershell/module/az.resources/remove-azresourcelock)

```azurepowershell-interactive
Remove-AzResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="manage-costs"></a>Gerir os custos

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma imagem de VM personalizada. Aprendeu a:

> [!div class="checklist"]
> * Atribuir utilizadores a uma função
> * Aplicar políticas que impõem normas
> * Proteger recursos críticos com bloqueios
> * Etiquetar recursos para faturação e gestão

Avance para o próximo tutorial para saber como identificar alterações e gerir atualizações de pacotes numa máquina virtual Linux.

> [!div class="nextstepaction"]
> [Gerir máquinas virtuais](tutorial-config-management.md)
