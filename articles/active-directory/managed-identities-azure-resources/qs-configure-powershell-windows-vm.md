---
title: Configure identidades geridas num Azure VM usando PowerShell - Azure AD
description: Instruções passo a passo para configurar identidades geridas para recursos Azure num Azure VM utilizando o PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/19/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b4209ce159c9d0bbee01dd422b98832f6bb5713
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90969005"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Configure identidades geridas para recursos Azure em um Azure VM usando PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código.

Neste artigo, utilizando o PowerShell, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num VM Azure.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os scripts de exemplo, tem duas opções:
    - Utilize o [Azure Cloud Shell,](../../cloud-shell/overview.md)que pode abrir utilizando o botão **Try It** no canto superior direito dos blocos de código.
    - Executar scripts localmente instalando a versão mais recente do [Azure PowerShell,](/powershell/azure/install-az-ps)em seguida, inscreva-se no Azure usando `Connect-AzAccount` . 

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprenderá a ativar e desativar a identidade gerida atribuída pelo sistema utilizando o Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Permitir a identidade gerida atribuída pelo sistema durante a criação de um VM Azure

Para criar um VM Azure com a identidade gerida atribuída pelo sistema, a sua conta precisa da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Consulte um dos seguintes Quickstarts Azure VM, completando apenas as secções necessárias ("Iniciar ação com Azure", "Criar grupo de recursos", "Criar grupo de networking", "Criar o VM").

    Quando chegar à secção "Criar o VM", faça uma ligeira modificação na sintaxe cmdlet [New-AzVMConfig.](/powershell/module/az.compute/new-azvm) Certifique-se de adicionar um `-IdentityType SystemAssigned` parâmetro para a disponibilização do VM com a identidade atribuída ao sistema, por exemplo:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName myVM -IdentityType SystemAssigned ...
    ```

   - [Criar uma máquina virtual do Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Criar uma máquina virtual Linux utilizando o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Permitir a identidade gerida atribuída pelo sistema num VM Azure existente

Para permitir a identidade gerida atribuída pelo sistema num VM que foi originalmente a provisionado sem ele, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Recupere as propriedades VM utilizando o `Get-AzVM` cmdlet. Em seguida, para ativar uma identidade gerida atribuída pelo sistema, utilize o `-IdentityType` interruptor no cmdlet [Update-AzVM:](/powershell/module/az.compute/update-azvm)

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType SystemAssigned
   ```

### <a name="add-vm-system-assigned-identity-to-a-group"></a>Adicionar identidade atribuída ao sistema VM a um grupo

Depois de ter ativado a identidade atribuída ao sistema num VM, pode adicioná-la a um grupo.  O procedimento seguinte adiciona a identidade atribuída a um grupo do sistema VM.

1. Recupere e note o `ObjectID` (conforme especificado no `Id` campo dos valores devolvidos) do principal de serviço da VM:

   ```azurepowershell-interactive
   Get-AzADServicePrincipal -displayname "myVM"
   ```

1. Recupere e note o `ObjectID` (conforme especificado no `Id` campo dos valores devolvidos) do grupo:

   ```azurepowershell-interactive
   Get-AzADGroup -searchstring "myGroup"
   ```

1. Adicione o principal de serviço da VM ao grupo:

   ```azurepowershell-interactive
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Desativar a identidade gerida atribuída pelo sistema a partir de um VM Azure

Para desativar a identidade gerida atribuída pelo sistema num VM, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

Se tiver uma Máquina Virtual que já não necessita da identidade gerida atribuída ao sistema, mas que ainda precisa de identidades geridas atribuídas pelo utilizador, utilize o seguinte cmdlet:

1. Recupere as propriedades VM utilizando o `Get-AzVM` cmdlet e desajuste o `-IdentityType` parâmetro `UserAssigned` para:

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Se tiver uma máquina virtual que já não necessita de identidade gerida atribuída ao sistema e não tiver identidades geridas atribuídas ao utilizador, utilize os seguintes comandos:

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, aprende-se a adicionar e a remover uma identidade gerida atribuída ao utilizador a partir de um VM utilizando o Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Atribuir uma identidade gerida atribuída ao utilizador a um VM durante a criação

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções [de Colaborador de Máquinas Virtuais](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e Operador de Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-operator) Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Consulte um dos seguintes Quickstarts Azure VM, completando apenas as secções necessárias ("Iniciar ação com Azure", "Criar grupo de recursos", "Criar grupo de networking", "Criar o VM").

    Quando chegar à secção "Criar o VM", faça uma ligeira modificação na sintaxe do [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) cmdlet. Adicione os `-IdentityType UserAssigned` parâmetros e `-IdentityID` os parâmetros à disposição do VM com uma identidade atribuída ao utilizador.  `<VM NAME>`Substitua, , , e com os seus `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` próprios valores.  Por exemplo:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```

    - [Criar uma máquina virtual do Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Criar uma máquina virtual Linux utilizando o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)


### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador a um Azure VM existente

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções [de Colaborador de Máquinas Virtuais](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e Operador de Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-operator) Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Crie uma identidade gerida atribuída ao utilizador utilizando o cmdlet [da Nova Entidade AzUserAssignedId.](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity)  Note a `Id` saída porque vai precisar disso no próximo passo.

   > [!IMPORTANT]
   > A criação de identidades geridas atribuídas pelo utilizador apenas suporta caracteres alfanuméricos, sublinhados e hífen (0-9 ou a-z ou A-Z, \_ ou -) caracteres. Além disso, o nome deve ser limitado de 3 a 128 caracteres para que a atribuição a VM/VMSS funcione corretamente. Para mais informações consulte [perguntas frequentes e questões conhecidas](known-issues.md)

   ```azurepowershell-interactive
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
1. Recupere as propriedades VM utilizando o `Get-AzVM` cmdlet. Em seguida, para atribuir uma identidade gerida atribuída ao VM Azure, utilize o `-IdentityType` e `-IdentityID` ligue o cmdlet [Update-AzVM.](/powershell/module/az.compute/update-azvm)  O valor para o `-IdentityId` parâmetro é o que `Id` notou no passo anterior.  `<VM NAME>`Substitua, , , e com os seus `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` próprios valores.

   > [!WARNING]
   > Para reter quaisquer identidades geridas previamente atribuídas ao VM, consultar a `Identity` propriedade do objeto VM (por exemplo, `$vm.Identity` ).  Se algum utilizador designado identidades geridas for devolvida, inclua-as no seguinte comando, juntamente com o novo utilizador atribuído identidade gerida que gostaria de atribuir ao VM.

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerida atribuída pelo utilizador de um VM Azure

Para remover uma identidade atribuída ao utilizador para um VM, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

Se o seu VM tiver várias identidades geridas atribuídas pelo utilizador, pode remover todas, menos a última, utilizando os seguintes comandos. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. A `<USER ASSIGNED IDENTITY NAME>` propriedade de nome gerido pelo utilizador atribuído ao utilizador, que deve permanecer no VM. Esta informação pode ser encontrada consultando a `Identity` propriedade do objeto VM.  Por `$vm.Identity` exemplo:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Se o seu VM não tiver uma identidade gerida atribuída ao sistema e pretender remover todas as identidades geridas atribuídas pelo utilizador, utilize o seguinte comando:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Se o seu VM tiver identidades geridas atribuídas ao sistema e atribuídas ao utilizador, pode remover todas as identidades geridas atribuídas pelo utilizador, mudando para utilizar apenas identidades geridas atribuídas pelo sistema.

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para visão geral dos recursos da Azure](overview.md)
- Para a criação completa de Azure VM Quickstarts, consulte:

  - [Criar máquinas virtuais do Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
  - [Criar máquinas virtuais do Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
