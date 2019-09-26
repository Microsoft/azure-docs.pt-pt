---
title: Como configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando o PowerShell
description: Instruções passo a passo para configurar um sistema e identidades gerenciadas atribuídas pelo usuário em um conjunto de dimensionamento de máquinas virtuais usando o PowerShell.
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
ms.openlocfilehash: 5fa3100cae9b1a2c9ca320776cc357f3720b3473
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309987"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Configurar identidades gerenciadas para recursos do Azure em conjuntos de dimensionamento de máquinas virtuais usando o PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, usando o PowerShell, você aprende a executar as identidades gerenciadas para operações de recursos do Azure em um conjunto de dimensionamento de máquinas virtuais:
- Habilitar e desabilitar a identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais
- Adicionar e remover uma identidade gerenciada atribuída pelo usuário em um conjunto de dimensionamento de máquinas virtuais

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de examinar a [diferença entre uma identidade atribuída pelo sistema e a gerenciada pelo usuário](overview.md#how-does-it-work)** .
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar as operações de gerenciamento neste artigo, sua conta precisa das seguintes atribuições de controle de acesso com base em função do Azure:

    > [!NOTE]
    > Nenhuma atribuição adicional de função de diretório do Azure AD é necessária.

    - [Colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e habilitar e remover identidades gerenciadas e/ou atribuídas pelo usuário e/ou com gerenciamento atribuído pelo sistema de um conjunto de dimensionamento de máquinas virtuais.
    - Função [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade gerenciada atribuída pelo usuário.
    - Função de [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade gerenciada atribuída pelo usuário de e para um conjunto de dimensionamento de máquinas virtuais.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez. 

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá a habilitar e a remover uma identidade gerenciada atribuída pelo sistema usando o Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Habilitar a identidade gerenciada atribuída pelo sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais do Azure

Para criar um conjunto de dimensionamento de máquinas virtuais com a identidade gerenciada atribuída pelo sistema habilitada:

1. Consulte o *exemplo 1* no artigo de referência do cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) para criar um conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.  Adicione o parâmetro `-IdentityType SystemAssigned` `New-AzVmssConfig` ao cmdlet:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```



## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Habilitar identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais do Azure existente

Se você precisar habilitar uma identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais do Azure existente:

1. Entre no Azure usando `Connect-AzAccount`. Use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais. Verifique também se sua conta pertence a uma função que lhe dá permissões de gravação no conjunto de dimensionamento de máquinas virtuais, como "colaborador da máquina virtual":

   ```powershell
   Connect-AzAccount
   ```

2. Primeiro recupere as propriedades do conjunto de dimensionamento de [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) máquinas virtuais usando o cmdlet. Em seguida, para habilitar uma identidade gerenciada atribuída pelo sistema `-IdentityType` , use a opção no cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) :

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```



### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desabilitar a identidade gerenciada atribuída pelo sistema de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais da identidade gerenciada atribuída pelo sistema, mas ainda precisar de identidades gerenciadas atribuídas pelo usuário, use o seguinte cmdlet:

1. Entre no Azure usando `Connect-AzAccount`. Use uma conta que esteja associada à assinatura do Azure que contém a VM. Verifique também se sua conta pertence a uma função que lhe dá permissões de gravação no conjunto de dimensionamento de máquinas virtuais, como "colaborador da máquina virtual":

2. Execute o seguinte cmdlet:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais de uma identidade gerenciada atribuída pelo sistema e não tiver identidades gerenciadas atribuídas pelo usuário, use os seguintes comandos:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá a adicionar e remover uma identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais usando Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída pelo usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais do Azure

Atualmente, não há suporte para a criação de um novo conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo usuário por meio do PowerShell. Consulte a próxima seção sobre como adicionar uma identidade gerenciada atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais existente. Volte mais tarde para obter atualizações.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais do Azure existente

Para atribuir uma identidade gerenciada atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais do Azure existente:

1. Entre no Azure usando `Connect-AzAccount`. Use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais. Verifique também se sua conta pertence a uma função que lhe dá permissões de gravação no conjunto de dimensionamento de máquinas virtuais, como "colaborador da máquina virtual":

   ```powershell
   Connect-AzAccount
   ```

2. Primeiro recupere as propriedades do conjunto de dimensionamento de `Get-AzVM` máquinas virtuais usando o cmdlet. Em seguida, para atribuir uma identidade gerenciada atribuída pelo usuário ao conjunto de dimensionamento de `-IdentityType` máquinas `-IdentityID` virtuais, use a opção e no cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) . Substitua `<VM NAME>`, `<SUBSCRIPTION ID>`, ,`<RESROURCE GROUP>` ,pelos`USER ASSIGNED ID2` seus própriosvalores`<USER ASSIGNED ID1>`.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remover uma identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais do Azure

Se o conjunto de dimensionamento de máquinas virtuais tiver várias identidades gerenciadas atribuídas pelo usuário, você poderá remover tudo, exceto o último, usando os comandos a seguir. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY NAME>` é a propriedade Name da identidade gerenciada atribuída pelo usuário, que deve permanecer no conjunto de dimensionamento de máquinas virtuais. Essas informações podem ser encontradas na seção identidade do conjunto de dimensionamento de máquinas virtuais `az vmss show`usando:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Se o conjunto de dimensionamento de máquinas virtuais não tiver uma identidade gerenciada atribuída pelo sistema e você quiser remover todas as identidades gerenciadas atribuídas pelo usuário dela, use o seguinte comando:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Se o conjunto de dimensionamento de máquinas virtuais tiver identidades gerenciadas atribuídas pelo sistema e com o usuário, você poderá remover todas as identidades gerenciadas atribuídas pelo usuário alternando para usar somente a identidade gerenciada atribuída pelo sistema.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas de descrição geral de recursos do Azure](overview.md)
- Para obter os guias de início rápido de criação de VM do Azure completos, consulte:
  
  - [Criar uma máquina virtual do Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Criar uma máquina virtual Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















