---
title: Atribuir um acesso de identidade gerido a um recurso utilizando o PowerShell - Azure AD
description: Instruções passo a passo para atribuir uma identidade gerida num recurso, acesso a outro recurso, utilizando o PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf1a7a608df7a2b752d9a6bed52a4024fd776c5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592505"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Atribuir um acesso de identidade gerido a um recurso utilizando o PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Uma vez configurado um recurso Azure com uma identidade gerida, pode dar à identidade gerida acesso a outro recurso, como qualquer diretor de segurança. Este exemplo mostra-lhe como dar acesso de identidade gerido por uma máquina virtual Azure a uma conta de armazenamento Azure utilizando o PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os scripts de exemplo, tem duas opções:
    - Utilize o [Azure Cloud Shell,](../../cloud-shell/overview.md)que pode abrir utilizando o botão **Try It** no canto superior direito dos blocos de código.
    - Executar scripts localmente instalando a versão mais recente do [Azure PowerShell,](/powershell/azure/install-az-ps)em seguida, inscreva-se no Azure usando `Connect-AzAccount` . 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Use o Azure RBAC para atribuir um acesso de identidade gerido a outro recurso

1. Ativar a identidade gerida num recurso Azure, [como um Azure VM](qs-configure-powershell-windows-vm.md).

1. Neste exemplo, estamos a dar um acesso Azure VM a uma conta de armazenamento. Primeiro [usamos o Get-AzVM](/powershell/module/az.compute/get-azvm) para obter o principal de serviço para o VM nomeado `myVM` , que foi criado quando permitimos a identidade gerida. Em seguida, utilize [a assinatura da New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) para dar ao **Leitor** VM acesso a uma conta de armazenamento chamada `myStorageAcct` :

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Passos seguintes

- [Identidade gerida para visão geral dos recursos da Azure](overview.md)
- Para permitir a identidade gerida num Azure VM, consulte [identidades geridas configure para recursos Azure num VM Azure utilizando o PowerShell](qs-configure-powershell-windows-vm.md).
