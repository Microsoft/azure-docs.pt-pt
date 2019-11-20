---
title: Configurar identidades gerenciadas em uma VM do Azure usando o PowerShell-Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o PowerShell.
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
ms.openlocfilehash: 6e17b4a3f71e67b99bfbd4c52edc00f98d549ef2
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183700"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, usando o PowerShell, você aprende a executar as seguintes identidades gerenciadas para operações de recursos do Azure em uma VM do Azure.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)** .
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá a habilitar e desabilitar a identidade gerenciada atribuída pelo sistema usando o Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Habilitar a identidade gerenciada atribuída pelo sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade gerenciada atribuída pelo sistema habilitada, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Consulte um dos seguintes inícios rápidos de VM do Azure, concluindo apenas as seções necessárias ("entrar no Azure", "Criar grupo de recursos", "Criar grupo de rede", "criar a VM").
    
    Quando você chegar à seção "criar a VM", faça uma pequena modificação na sintaxe do cmdlet [New-AzVMConfig](/powershell/module/az.compute/new-azvm) . Certifique-se de adicionar um parâmetro `-AssignIdentity:$SystemAssigned` para provisionar a VM com a identidade atribuída pelo sistema habilitada, por exemplo:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Criar uma máquina virtual do Windows usando o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Criar uma máquina virtual do Linux usando o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Habilitar identidade gerenciada atribuída pelo sistema em uma VM do Azure existente

Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM que foi originalmente provisionada sem ela, sua conta precisa da atribuição de função de [colaborador de máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Entre no Azure usando `Connect-AzAccount`. Use uma conta que esteja associada à assinatura do Azure que contém a VM.

   ```powershell
   Connect-AzAccount
   ```

2. Primeiro, recupere as propriedades da VM usando o cmdlet `Get-AzVM`. Em seguida, para habilitar uma identidade gerenciada atribuída pelo sistema, use a opção `-AssignIdentity` no cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) :

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Adicionar identidade atribuída ao sistema de VM a um grupo

Depois de habilitar a identidade atribuída pelo sistema em uma VM, você pode adicioná-la a um grupo.  O procedimento a seguir adiciona a identidade atribuída do sistema de uma VM a um grupo.

1. Entre no Azure usando `Connect-AzAccount`. Use uma conta que esteja associada à assinatura do Azure que contém a VM.

   ```powershell
   Connect-AzAccount
   ```

2. Recupere e observe o `ObjectID` (conforme especificado no campo `Id` dos valores retornados) da entidade de serviço da VM:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Recupere e observe a `ObjectID` (conforme especificado no campo `Id` dos valores retornados) do Grupo:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Adicione a entidade de serviço da VM ao grupo:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Desabilitar a identidade gerenciada atribuída pelo sistema de uma VM do Azure

Para desabilitar a identidade gerenciada atribuída pelo sistema em uma VM, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

Se você tiver uma máquina virtual que não precisa mais da identidade gerenciada atribuída pelo sistema, mas ainda precisar de identidades gerenciadas atribuídas pelo usuário, use o seguinte cmdlet:

1. Entre no Azure usando `Connect-AzAccount`. Use uma conta que esteja associada à assinatura do Azure que contém a VM.

   ```powershell
   Connect-AzAccount
   ```

2. Recupere as propriedades da VM usando o cmdlet `Get-AzVM` e defina o parâmetro `-IdentityType` como `UserAssigned`:

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Se você tiver uma máquina virtual que não precisa mais de uma identidade gerenciada atribuída pelo sistema e não tiver identidades gerenciadas atribuídas pelo usuário, use os seguintes comandos:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá a adicionar e remover uma identidade gerenciada atribuída pelo usuário de uma VM usando o Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Atribuir uma identidade gerenciada atribuída pelo usuário a uma VM durante a criação

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Consulte um dos seguintes inícios rápidos de VM do Azure, concluindo apenas as seções necessárias ("entrar no Azure", "Criar grupo de recursos", "Criar grupo de rede", "criar a VM"). 
  
    Quando você chegar à seção "criar a VM", faça uma pequena modificação na sintaxe do cmdlet [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) . Adicione os parâmetros `-IdentityType UserAssigned` e `-IdentityID` para provisionar a VM com uma identidade atribuída pelo usuário.  Substitua `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores.  Por exemplo:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Criar uma máquina virtual do Windows usando o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Criar uma máquina virtual do Linux usando o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerenciada atribuída pelo usuário a uma VM do Azure existente

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Entre no Azure usando `Connect-AzAccount`. Use uma conta que esteja associada à assinatura do Azure que contém a VM.

   ```powershell
   Connect-AzAccount
   ```

2. Crie uma identidade gerenciada atribuída pelo usuário usando o cmdlet [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) .  Observe a `Id` na saída, pois você precisará dela na próxima etapa.

   > [!IMPORTANT]
   > A criação de identidades gerenciadas atribuídas pelo usuário dá suporte apenas a caracteres alfanuméricos, sublinhados e hifens (0-9 ou a-z ou A-Z, \_ ou-). Além disso, o nome deve ser limitado de 3 a 128 de comprimento de caractere para que a atribuição a VM/VMSS funcione corretamente. Para obter mais informações [, consulte perguntas frequentes e problemas conhecidos](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Recupere as propriedades da VM usando o cmdlet `Get-AzVM`. Em seguida, para atribuir uma identidade gerenciada atribuída pelo usuário à VM do Azure, use a opção `-IdentityType` e `-IdentityID` no cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) .  O valor do parâmetro`-IdentityId` é o `Id` que você anotou na etapa anterior.  Substitua `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores.

   > [!WARNING]
   > Para manter qualquer identidade gerenciada anteriormente atribuída pelo usuário atribuída à VM, consulte a propriedade `Identity` do objeto da VM (por exemplo, `$vm.Identity`).  Se quaisquer identidades gerenciadas atribuídas ao usuário forem retornadas, inclua-as no comando a seguir junto com a nova identidade gerenciada atribuída pelo usuário que você deseja atribuir à VM.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerenciada atribuída pelo usuário de uma VM do Azure

Para remover uma identidade atribuída pelo usuário a uma VM, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .

Se sua VM tiver várias identidades gerenciadas atribuídas pelo usuário, você poderá remover tudo, exceto o último, usando os comandos a seguir. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY NAME>` é a propriedade Name da identidade gerenciada atribuída pelo usuário, que deve permanecer na VM. Essas informações podem ser encontradas consultando a propriedade `Identity` do objeto da VM.  Por exemplo, `$vm.Identity`:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Se sua VM não tiver uma identidade gerenciada atribuída pelo sistema e você quiser remover todas as identidades gerenciadas atribuídas pelo usuário dela, use o seguinte comando:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Se sua VM tiver identidades gerenciadas atribuídas pelo sistema e com o usuário, você poderá remover todas as identidades gerenciadas atribuídas pelo usuário alternando para usar somente as identidades gerenciadas atribuídas pelo sistema.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas de descrição geral de recursos do Azure](overview.md)
- Para obter os guias de início rápido de criação de VM do Azure completos, consulte:
  
  - [Criar uma máquina virtual do Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Criar uma máquina virtual Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
