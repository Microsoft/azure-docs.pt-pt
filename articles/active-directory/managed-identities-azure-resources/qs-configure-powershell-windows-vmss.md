---
title: Conjunto de como configurar identidades geridas para recursos do Azure numa escala de máquina virtual com o PowerShell
description: Instruções passo a passo instruções para configurar um sistema e de identidades geridas atribuído ao utilizador numa escala de máquina virtual conjunto com o PowerShell.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dee7759dccf3093e9ba9f66bffcceaf603a11d4
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226883"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Configurar identidades geridas para recursos do Azure em conjuntos de dimensionamento de máquina virtual com o PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, com o PowerShell, aprenderá a efetuar as identidades geridas para operações de recursos do Azure num conjunto de dimensionamento de máquina virtual:
- Ativar e desativar a identidade gerida atribuído de sistema num conjunto de dimensionamento de máquinas virtuais
- Adicionar e remover uma identidade gerida atribuído ao utilizador num conjunto de dimensionamento de máquinas virtuais

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre um sistema-atribuído e gerido pelo utilizador de identidade atribuída](overview.md#how-does-it-work)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para efetuar as operações de gestão neste artigo, a conta tem das atribuições de controlo de acesso baseado em funções do Azure seguintes:

    > [!NOTE]
    > Nenhum adicionais do Azure AD directory as atribuições de funções necessárias.

    - [Contribuinte de máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um dimensionamento de máquinas virtuais conjunto e ativar e remover atribuído o sistema gerido e/ou atribuído ao utilizador a identidade de gerido a partir de um conjunto de dimensionamento de máquina virtual.
    - [Contribuidor de identidade de geridos](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) identidade gerida de função para criar um atribuído ao utilizador.
    - [Gerido operador de identidade](/azure/role-based-access-control/built-in-roles#managed-identity-operator) função para atribuir e remover um atribuído ao utilizador identidade gerida de e para um conjunto de dimensionamento de máquina virtual.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez. 

## <a name="system-assigned-managed-identity"></a>Atribuído ao sistema de identidade gerida

Nesta secção, saiba como ativar e remover uma identidade gerida atribuído de sistema com o Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuído ao sistema durante a criação de um conjunto de dimensionamento de máquina virtual do Azure

Para criar um conjunto de dimensionamento com a identidade gerida atribuídos do sistema ativada:

1. Consulte a *exemplo 1* no [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) conjunto de artigo de referência do cmdlet para criar um dimensionamento de máquina virtual com uma identidade gerida atribuído de sistema.  Adicione o parâmetro `-IdentityType SystemAssigned` para o `New-AzVmssConfig` cmdlet:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```
> [!NOTE]
> Opcionalmente, pode aprovisionar as identidades geridas para extensão do conjunto de dimensionamento de máquina virtual de recursos do Azure, mas em breve será preterido. Recomendamos que utilize o ponto final de identidade de metadados de instância do Azure para a autenticação. Para obter mais informações, consulte [parar de utilizar a extensão de VM e começar a utilizar o ponto de extremidade IMDS do Azure para a autenticação](howto-migrate-vm-extension.md).


## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuído de sistema num conjunto de dimensionamento de máquina virtual do Azure existente

Se precisar de ativar uma identidade gerida atribuído de sistema num conjunto de dimensionamento de máquina virtual do Azure existente:

1. Inicie sessão no Azure com `Connect-AzAccount`. Utilize uma conta que está associada à subscrição do Azure que contém o conjunto de dimensionamento de máquina virtual. Além disso, certifique-se de que a sua conta pertencer a uma função que dá-lhe permissões de escrita no conjunto de dimensionamento de máquina virtual, tais como "Contribuinte de Máquina Virtual":

   ```powershell
   Connect-AzAccount
   ```

2. Obter primeiro o dimensionamento de máquinas virtuais definir propriedades usando o [ `Get-AzVmss` ](/powershell/module/az.compute/get-azvmss) cmdlet. Em seguida, para ativar uma identidade gerida atribuído de sistema, utilize o `-IdentityType` alternar o [AzVmss de atualização](/powershell/module/az.compute/update-azvmss) cmdlet:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

> [!NOTE]
> Opcionalmente, pode aprovisionar as identidades geridas para extensão do conjunto de dimensionamento de máquina virtual de recursos do Azure, mas em breve será preterido. Recomendamos que utilize o ponto final de identidade de metadados de instância do Azure para a autenticação. Para obter mais informações, consulte [migrar a partir da extensão da VM para o ponto final de IMDS do Azure para a autenticação](howto-migrate-vm-extension.md).

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desativar a identidade gerida atribuído de sistema de um conjunto de dimensionamento de máquina virtual do Azure

Se tiver um conjunto de dimensionamento de máquina virtual que já não precisa de a identidade gerida atribuído de sistema, mas ainda precisa de identidades geridas atribuído ao utilizador, utilize o seguinte cmdlet:

1. Inicie sessão no Azure com `Connect-AzAccount`. Utilize uma conta que está associada à subscrição do Azure que contém a VM. Além disso, certifique-se de que a sua conta pertencer a uma função que dá-lhe permissões de escrita no conjunto de dimensionamento de máquina virtual, tais como "Contribuinte de Máquina Virtual":

2. Execute o seguinte cmdlet:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Se tiver um conjunto de dimensionamento de máquina virtual que já não precisa atribuído de sistema de identidade gerida e tem não identidades geridas atribuído ao utilizador, utilize os seguintes comandos:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, saiba como adicionar e remover uma identidade gerida atribuído ao utilizador a partir de um conjunto de dimensionamento com o Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuído ao utilizador durante a criação de um conjunto de dimensionamento de máquina virtual do Azure

Criar um novo conjunto de dimensionamento com uma identidade gerida atribuído ao utilizador atualmente não é suportada através do PowerShell. Veja a secção seguinte sobre como adicionar uma identidade gerida atribuído ao utilizador a um conjunto de dimensionamento de máquina virtual existente. Volte mais tarde para obter atualizações.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuído ao utilizador a um conjunto de dimensionamento de máquina virtual do Azure existente

Para atribuir uma identidade gerida atribuído ao utilizador a um conjunto de dimensionamento de máquina virtual do Azure existente:

1. Inicie sessão no Azure com `Connect-AzAccount`. Utilize uma conta que está associada à subscrição do Azure que contém o conjunto de dimensionamento de máquina virtual. Além disso, certifique-se de que a sua conta pertencer a uma função que dá-lhe permissões de escrita no conjunto de dimensionamento de máquina virtual, tais como "Contribuinte de Máquina Virtual":

   ```powershell
   Connect-AzAccount
   ```

2. Obter primeiro o dimensionamento de máquinas virtuais definir propriedades usando o `Get-AzVM` cmdlet. Em seguida, para atribuir uma identidade gerida atribuído ao utilizador para o conjunto de dimensionamento de máquina virtual, utilize o `-IdentityType` e `-IdentityID` alternar o [AzVmss de atualização](/powershell/module/az.compute/update-azvmss) cmdlet. Substitua `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` pelos seus próprios valores.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remover uma identidade gerida atribuído ao utilizador a partir de um conjunto de dimensionamento de máquina virtual do Azure

Se o conjunto de dimensionamento de máquina virtual tiver várias identidades geridas atribuído ao utilizador, pode remover todas, exceto a última está com os comandos seguintes. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY NAME>` é propriedade de nome do atribuído ao utilizador gerido da identidade, que deve permanecer no conjunto de dimensionamento de máquina virtual. Estas informações podem ser encontradas na secção da identidade do conjunto com o dimensionamento da máquina virtual `az vmss show`:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Se o conjunto de dimensionamento de máquina virtual não tiver um sistema atribuído geridos identidade e pretender remover todas as identidades geridas atribuído ao utilizador do mesmo, utilize o seguinte comando:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Se o conjunto de dimensionamento de máquinas virtuais tem ambos atribuído de sistema e identidades geridas atribuído ao utilizador, pode remover todos os geridos identidiades atribuídas ao mudar para utilizar apenas atribuído de sistema de identidade gerida.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Passos Seguintes

- [Identidades geridas de descrição geral de recursos do Azure](overview.md)
- Para a criação de VM do Azure completa inícios rápidos, consulte:
  
  - [Criar uma máquina virtual do Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Criar uma máquina virtual Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















