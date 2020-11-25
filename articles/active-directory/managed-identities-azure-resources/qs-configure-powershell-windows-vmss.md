---
title: Configure identidades geridas em conjuntos de escala de máquina virtual usando PowerShell - Azure AD
description: Instruções passo a passo para configurar um sistema e identidades geridas atribuídas pelo utilizador num conjunto de escala de máquina virtual utilizando o PowerShell.
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
ms.date: 09/26/2019
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d193637122cb388ea2c5012638526719d245f524
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997388"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Configurar identidades geridas para recursos Azure em conjuntos de escala de máquinas virtuais usando PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código. 

Neste artigo, utilizando o PowerShell, aprende-se a executar as identidades geridas para operações de recursos Azure num conjunto de escala de máquina virtual:
- Ativar e desativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual
- Adicione e remova uma identidade gerida atribuída pelo utilizador num conjunto de escala de máquina virtual

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade atribuída pelo sistema e gerida pelo utilizador.](overview.md#managed-identity-types)**

- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

- Para realizar as operações de gestão neste artigo, a sua conta necessita das seguintes atribuições de controlo de acesso baseados em funções Azure:

    > [!NOTE]
    > Não são necessárias atribuições adicionais de diretório ad AD.

    - [O Contribuinte De Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para criar um conjunto de escala de máquina virtual e ativar e remover a identidade gerida gerida e/ou atribuída pelo utilizador a partir de um conjunto de escala de máquina virtual.
    - [Papel de Contribuinte de Identidade Gerido](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) para criar uma identidade gerida atribuída pelo utilizador.
    - [Função de Operador de Identidade Gerida](../../role-based-access-control/built-in-roles.md#managed-identity-operator) para atribuir e remover uma identidade gerida atribuída pelo utilizador de e para um conjunto de escala de máquina virtual.

- Para executar os scripts de exemplo, tem duas opções:
    - Utilize o [Azure Cloud Shell,](../../cloud-shell/overview.md)que pode abrir utilizando o botão **Try It** no canto superior direito dos blocos de código.
    - Executar scripts localmente instalando a versão mais recente do [Azure PowerShell,](/powershell/azure/install-az-ps)em seguida, inscreva-se no Azure usando `Connect-AzAccount` . 

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprende-se a ativar e remover uma identidade gerida atribuída pelo sistema utilizando o Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema durante a criação de um conjunto de escala de máquina virtual Azure

Para criar um conjunto de escala de máquina virtual com a identidade gerida atribuída pelo sistema:

1. Consulte o *Exemplo 1* no artigo de referência do cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) para criar uma balança de máquina virtual definida com uma identidade gerida atribuída pelo sistema.  Adicione o parâmetro `-IdentityType SystemAssigned` ao `New-AzVmssConfig` cmdlet:

    ```azurepowershell-interactive
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual Azure existente

Se precisar de ativar uma identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual Azure existente:

1. Certifique-se de que a conta Azure que está a utilizar pertence a uma função que lhe dá permissões de escrita no conjunto de escala de máquina virtual, como "Contribuinte de Máquina Virtual".
   
1. Recupere as propriedades de conjunto de escala de máquina virtual utilizando o [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) cmdlet. Em seguida, para ativar uma identidade gerida atribuída pelo sistema, utilize o `-IdentityType` interruptor no cmdlet [Update-AzVmss:](/powershell/module/az.compute/update-azvmss)

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desative a identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual Azure

Se tiver um conjunto de escala de máquina virtual que já não necessita da identidade gerida atribuída ao sistema, mas que ainda necessita de identidades geridas atribuídas pelo utilizador, utilize o seguinte cmdlet:

1. Certifique-se de que a sua conta pertence a uma função que lhe dá permissões de escrita no conjunto de escala de máquina virtual, como "Contribuinte de Máquina Virtual".

1. Execute o seguinte cmdlet:

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

1. Se tiver um conjunto de escala de máquina virtual que já não necessita de identidade gerida atribuída ao sistema e não tiver identidades geridas atribuídas ao utilizador, utilize o seguinte comando:

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
    ```
    
## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, aprende-se a adicionar e remover uma identidade gerida atribuída pelo utilizador a partir de um conjunto de escala de máquina virtual utilizando o Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Atribua uma identidade gerida atribuída ao utilizador durante a criação de um conjunto de escala de máquina virtual Azure

A criação de uma nova balança de máquinas virtual com uma identidade gerida atribuída pelo utilizador não é suportada atualmente através do PowerShell. Consulte a secção seguinte sobre como adicionar uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual existente. Volte mais tarde para obter atualizações.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Atribua uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual Azure existente

Para atribuir uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual Azure existente:

1. Certifique-se de que a sua conta pertence a uma função que lhe dá permissões de escrita no conjunto de escala de máquina virtual, como "Contribuinte de Máquina Virtual".

1. Recupere as propriedades de conjunto de escala de máquina virtual utilizando o `Get-AzVM` cmdlet. Em seguida, para atribuir uma identidade gerida atribuída ao conjunto de escala de máquina virtual, utilize o `-IdentityType` e `-IdentityID` ligue o cmdlet [Update-AzVmss.](/powershell/module/az.compute/update-azvmss) `<VM NAME>`Substitua, , , , com os seus `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED ID1>` `USER ASSIGNED ID2` próprios valores.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remova uma identidade gerida atribuída pelo utilizador de um conjunto de escala de máquina virtual Azure

Se o seu conjunto de escala de máquina virtual tiver várias identidades geridas atribuídas pelo utilizador, pode remover todas, menos a última, utilizando os seguintes comandos. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. A `<USER ASSIGNED IDENTITY NAME>` propriedade de nome gerido pelo utilizador atribuído ao utilizador, que deve permanecer no conjunto de escala de máquina virtual. Estas informações podem ser encontradas na secção de identidade do conjunto de escala de máquina virtual `az vmss show` utilizando:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Se o seu conjunto de escala de máquina virtual não tiver uma identidade gerida atribuída ao sistema e pretender remover todas as identidades geridas atribuídas pelo utilizador, utilize o seguinte comando:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Se o seu conjunto de escala de máquina virtual tiver identidades geridas atribuídas ao sistema e atribuídas ao utilizador, pode remover todas as identidades geridas atribuídas pelo utilizador, mudando para utilizar apenas a identidade gerida atribuída pelo sistema.

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para visão geral dos recursos da Azure](overview.md)
- Para a criação completa de Azure VM Quickstarts, consulte:
  
  - [Criar máquinas virtuais do Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Criar máquinas virtuais do Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
