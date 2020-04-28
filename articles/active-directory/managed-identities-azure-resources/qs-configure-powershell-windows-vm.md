---
title: Configure identidades geridas num Azure VM usando PowerShell - Azure AD
description: Instruções passo a passo para configurar identidades geridas para os recursos Azure num Azure VM utilizando powerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f24c89477d71df3f497590b49841403576343bd4
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74547221"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Configure identidades geridas para recursos Azure em um VM Azure usando PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código. 

Neste artigo, utilizando o PowerShell, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num Azure VM.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprenderá a ativar e desativar a identidade gerida atribuída pelo sistema utilizando o Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Ativar identidade gerida atribuída pelo sistema durante a criação de um VM Azure

Para criar um VM Azure com a identidade gerida atribuída pelo sistema, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

1. Consulte um dos seguintes Quickstarts Azure VM, completando apenas as secções necessárias ("Inscreva-se no Azure", "Criar grupo de recursos", "Criar grupo de networking", "Criar o VM").
    
    Quando chegar à secção "Criar o VM", faça uma ligeira modificação na sintaxe cmdlet [New-AzVMConfig.](/powershell/module/az.compute/new-azvm) Certifique-se de `-AssignIdentity:$SystemAssigned` adicionar um parâmetro para fornecer o VM com a identidade atribuída ao sistema, por exemplo:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Criar uma máquina virtual do Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Crie uma máquina virtual Linux usando powerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Ativar a identidade gerida atribuída pelo sistema num VM Azure existente

Para permitir a identidade gerida atribuída pelo sistema num VM originalmente provisionado sem ele, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

1. Inscreva-se no `Connect-AzAccount`Azure usando . Utilize uma conta associada à subscrição Azure que contenha o VM.

   ```powershell
   Connect-AzAccount
   ```

2. Primeiro recupere as propriedades `Get-AzVM` VM utilizando o cmdlet. Em seguida, para ativar uma identidade `-AssignIdentity` gerida atribuída pelo sistema, utilize o interruptor no cmdlet [Update-AzVM:](/powershell/module/az.compute/update-azvm)

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Adicione o sistema VM atribuído identidade a um grupo

Depois de ter ativado o sistema atribuído identidade num VM, pode adicioná-lo a um grupo.  O procedimento seguinte adiciona um sistema de VM atribuído identidade a um grupo.

1. Inscreva-se no `Connect-AzAccount`Azure usando . Utilize uma conta associada à subscrição Azure que contenha o VM.

   ```powershell
   Connect-AzAccount
   ```

2. Recuperar e observar `ObjectID` o (conforme `Id` especificado no campo dos valores devolvidos) do diretor de serviço da VM:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Recuperar e observar `ObjectID` o (conforme `Id` especificado no campo dos valores devolvidos) do grupo:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Adicione o principal de serviço da VM ao grupo:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Desativar a identidade gerida atribuída pelo sistema a partir de um VM Azure

Para desativar a identidade gerida atribuída pelo sistema num VM, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

Se tiver uma Máquina Virtual que já não necessite da identidade gerida atribuída pelo sistema, mas que ainda necessite de identidades geridas atribuídas pelo utilizador, utilize o seguinte cmdlet:

1. Inscreva-se no `Connect-AzAccount`Azure usando . Utilize uma conta associada à subscrição Azure que contenha o VM.

   ```powershell
   Connect-AzAccount
   ```

2. Recupere as propriedades `Get-AzVM` VM utilizando o `-IdentityType` cmdlet e coloque o parâmetro para: `UserAssigned`

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Se tiver uma máquina virtual que já não necessite de identidade gerida atribuída pelo sistema e não tiver identidades geridas atribuídas pelo utilizador, utilize os seguintes comandos:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída ao utilizador

Nesta secção, aprende-se a adicionar e remover uma identidade gerida atribuída pelo utilizador a partir de um VM utilizando o Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Atribuir uma identidade gerida atribuída ao utilizador a um VM durante a criação

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções de Colaborador de [Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e Operador de [Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Não são necessárias atribuições adicionais de diretório da AD.

1. Consulte um dos seguintes Quickstarts Azure VM, completando apenas as secções necessárias ("Inscreva-se no Azure", "Criar grupo de recursos", "Criar grupo de networking", "Criar o VM"). 
  
    Quando chegar à secção "Criar o VM", faça [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) uma ligeira modificação na sintaxe cmdlet. Adicione `-IdentityType UserAssigned` os `-IdentityID` e parâmetros ao fornecimento do VM com uma identidade atribuída ao utilizador.  Substitua,, `<VM NAME>``<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` e com os seus próprios valores.  Por exemplo:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Criar uma máquina virtual do Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Crie uma máquina virtual Linux usando powerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador a um VM Azure existente

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções de Colaborador de [Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e Operador de [Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Não são necessárias atribuições adicionais de diretório da AD.

1. Inscreva-se no `Connect-AzAccount`Azure usando . Utilize uma conta associada à subscrição Azure que contenha o VM.

   ```powershell
   Connect-AzAccount
   ```

2. Crie uma identidade gerida atribuída ao utilizador utilizando o cmdlet [New-AzUserAssignedIdentity.](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity)  Note `Id` a saída porque vai precisar disso no próximo passo.

   > [!IMPORTANT]
   > A criação de identidades geridas atribuídas ao utilizador apenas suporta caracteres alfanuméricos, \_ sublinhados e hífen (0-9 ou a-z ou A-Z, ou -) caracteres. Além disso, o nome deve ser limitado de 3 a 128 caracteres para que a atribuição à VM/VMSS funcione corretamente. Para mais informações consulte [faQs e questões conhecidas](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Recupere as propriedades `Get-AzVM` VM utilizando o cmdlet. Em seguida, para atribuir uma identidade gerida atribuída ao Azure `-IdentityType` `-IdentityID` VM, utilize o cmdlet [Update-AzVM.](/powershell/module/az.compute/update-azvm)  O valor`-IdentityId` para o `Id` parâmetro é o que você observou no passo anterior.  Substitua,, `<VM NAME>` `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` e com os seus próprios valores.

   > [!WARNING]
   > Para reter quaisquer identidades geridas previamente atribuídas ao utilizador `Identity` atribuídas ao VM, consulta `$vm.Identity`a propriedade do objeto VM (por exemplo, ).  Se algum utilizador designado for devolvido, inclua-os no seguinte comando juntamente com a identidade gerida do novo utilizador que deseja atribuir ao VM.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remova uma identidade gerida atribuída ao utilizador de um VM Azure

Para remover uma identidade atribuída ao utilizador a um VM, a sua conta necessita da atribuição da função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

Se o seu VM tiver várias identidades geridas atribuídas ao utilizador, pode remover todas, menos a última, utilizando os seguintes comandos. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. Trata-se `<USER ASSIGNED IDENTITY NAME>` da propriedade de identidade gerida atribuída pelo utilizador, que deve permanecer no VM. Esta informação pode ser encontrada `Identity` consultando a propriedade do objeto VM.  Por exemplo, `$vm.Identity`:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Se o seu VM não tiver uma identidade gerida atribuída pelo sistema e pretender remover todas as identidades geridas atribuídas pelo utilizador, utilize o seguinte comando:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Se o seu VM tiver identidades geridas atribuídas ao sistema e atribuídas ao utilizador, pode remover todas as identidades geridas atribuídas pelo utilizador, mudando para utilizar apenas identidades geridas atribuídas pelo sistema.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para a visão geral dos recursos do Azure](overview.md)
- Para a criação full Azure VM Quickstarts, consulte:
  
  - [Criar máquinas virtuais do Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Criar máquinas virtuais do Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
