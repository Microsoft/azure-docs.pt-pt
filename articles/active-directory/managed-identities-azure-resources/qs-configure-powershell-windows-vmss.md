---
title: Configure identidades geridas em conjuntos de escala de máquinas virtuais usando PowerShell - Azure AD
description: Instruções passo a passo para configurar um sistema e identidades geridas atribuídas pelo utilizador num conjunto de escala de máquina virtual utilizando o PowerShell.
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
ms.openlocfilehash: 755aee312fd0492fd57a82cb7a437b04ebf72987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547275"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Configure identidades geridas para recursos Azure em conjuntos de escala de máquinas virtuais usando PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código. 

Neste artigo, utilizando o PowerShell, aprende-se a executar as identidades geridas para operações de recursos Azure num conjunto de escala de máquina virtual:
- Ativar e desativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual
- Adicione e remova uma identidade gerida atribuída pelo utilizador num conjunto de escala de máquina virtual

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e o utilizador gerido .
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gestão neste artigo, a sua conta necessita das seguintes atribuições de controlo de acesso baseadas no papel Azure:

    > [!NOTE]
    > Não são necessárias atribuições adicionais de diretório da AD.

    - [Colaborador de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de escala de máquina virtual e ativar e remover a identidade gerida gerida e/ou atribuída ao utilizador a partir de um conjunto de escala de máquina virtual.
    - [Função de Colaborador de Identidade Gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade gerida atribuída pelo utilizador.
    - [Função do Operador de Identidade Gerido](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade gerida atribuída pelo utilizador de e para um conjunto de escala de máquina virtual.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez. 

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprende-se a ativar e remover uma identidade gerida atribuída pelo sistema utilizando o Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema durante a criação de um conjunto de máquinas virtuais Azure

Para criar um conjunto de escala de máquina virtual com a identidade gerida atribuída pelo sistema ativada:

1. Consulte o *Exemplo 1* no artigo de referência [new-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet para criar um conjunto de escala de máquina virtual com uma identidade gerida atribuída pelo sistema.  Adicione o `-IdentityType SystemAssigned` parâmetro `New-AzVmssConfig` ao cmdlet:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```



## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema num conjunto de máquinas virtuais Azure existente

Se precisar de ativar uma identidade gerida atribuída pelo sistema num conjunto de máquinas virtuais Azure existente:

1. Inscreva-se no `Connect-AzAccount`Azure usando . Utilize uma conta associada à subscrição Azure que contenha o conjunto de escala de máquina virtual. Certifique-se também de que a sua conta pertence a um papel que lhe dá permissões de escrita no conjunto de escala de máquina virtual, como "Virtual Machine Contributor":

   ```powershell
   Connect-AzAccount
   ```

2. Primeiro recupere as propriedades de [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) conjunto de máquinas virtuais utilizando o cmdlet. Em seguida, para permitir uma identidade gerida `-IdentityType` atribuída pelo sistema, utilize o interruptor no [azVmss cmdlet update-AzVmss:](/powershell/module/az.compute/update-azvmss)

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```



### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desativar a identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual Azure

Se tiver um conjunto de escala de máquina virtual que já não necessite da identidade gerida atribuída pelo sistema, mas que ainda necessite de identidades geridas atribuídas pelo utilizador, utilize o seguinte cmdlet:

1. Inscreva-se no `Connect-AzAccount`Azure usando . Utilize uma conta associada à subscrição Azure que contenha o VM. Certifique-se também de que a sua conta pertence a um papel que lhe dá permissões de escrita no conjunto de escala de máquina virtual, como "Virtual Machine Contributor":

2. Execute o seguinte cmdlet:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Se tiver um conjunto de escala de máquina virtual que já não necessite de identidade gerida atribuída pelo sistema e não possui identidades geridas atribuídas pelo utilizador, utilize os seguintes comandos:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída ao utilizador

Nesta secção, aprende-se a adicionar e remover uma identidade gerida atribuída pelo utilizador a partir de um conjunto de escala de máquina virtual utilizando o Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuída ao utilizador durante a criação de um conjunto de escala de máquina virtual Azure

A criação de um novo conjunto de máquinas virtuais com uma identidade gerida atribuída pelo utilizador não é atualmente suportada através do PowerShell. Consulte a secção seguinte sobre como adicionar uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual existente. Volte mais tarde para obter atualizações.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual Azure existente

Para atribuir uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual Azure existente:

1. Inscreva-se no `Connect-AzAccount`Azure usando . Utilize uma conta associada à subscrição Azure que contenha o conjunto de escala de máquina virtual. Certifique-se também de que a sua conta pertence a um papel que lhe dá permissões de escrita no conjunto de escala de máquina virtual, como "Virtual Machine Contributor":

   ```powershell
   Connect-AzAccount
   ```

2. Primeiro recupere as propriedades de `Get-AzVM` conjunto de máquinas virtuais utilizando o cmdlet. Em seguida, para atribuir uma identidade gerida atribuída ao conjunto `-IdentityType` de `-IdentityID` escala de máquina virtual, utilize o e ligue o cmdlet [Update-AzVmss.](/powershell/module/az.compute/update-azvmss) `<SUBSCRIPTION ID>`Substitua, `<RESROURCE GROUP>` `<VM NAME>` `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` com os seus próprios valores.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remova uma identidade gerida atribuída ao utilizador de um conjunto de escala de máquina virtual Azure

Se o seu conjunto de escala de máquina virtual tiver múltiplas identidades geridas atribuídas ao utilizador, pode remover todas, menos a última, utilizando os seguintes comandos. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. Trata-se `<USER ASSIGNED IDENTITY NAME>` da propriedade de nome de identidade gerida atribuída pelo utilizador, que deve permanecer no conjunto de escala de máquina virtual. Estas informações podem ser encontradas na secção `az vmss show`de identidade da escala de máquina virtual com recurso:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Se o seu conjunto de escala de máquina virtual não tiver uma identidade gerida atribuída pelo sistema e pretender remover todas as identidades geridas atribuídas pelo utilizador, utilize o seguinte comando:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Se o seu conjunto de escala de máquina virtual tiver identidades geridas atribuídas ao sistema e atribuídas ao utilizador, pode remover todas as identidades geridas atribuídas pelo utilizador, mudando para utilizar apenas a identidade gerida atribuída pelo sistema.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para a visão geral dos recursos do Azure](overview.md)
- Para a criação full Azure VM Quickstarts, consulte:
  
  - [Criar máquinas virtuais do Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Criar máquinas virtuais do Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















