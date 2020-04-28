---
title: Atribuir um acesso de identidade gerido a um recurso utilizando o PowerShell - Azure AD
description: Instruções passo a passo para atribuir uma identidade gerida num recurso, acesso a outro recurso, utilizando o PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2283ac076ef761fd098d75e7120e6557a959574
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74547252"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Atribuir um acesso de identidade gerido a um recurso usando powerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Uma vez configurado um recurso Azure com uma identidade gerida, pode dar à identidade gerida acesso a outro recurso, como qualquer diretor de segurança. Este exemplo mostra-lhe como dar acesso de identidade gerido a uma máquina virtual Azure a uma conta de armazenamento Azure utilizando o PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Utilize o RBAC para atribuir um acesso de identidade gerido a outro recurso

Depois de ter ativado a identidade gerida num recurso Azure, [como um Azure VM:](qs-configure-powershell-windows-vm.md)

1. Inscreva-se no Azure utilizando o `Connect-AzAccount` cmdlet. Utilize uma conta associada à subscrição Azure sob a qual configura a identidade gerida:

   ```powershell
   Connect-AzAccount
   ```
2. Neste exemplo, estamos a dar acesso a uma conta de armazenamento de Um VM Azure. Primeiro usamos [o Get-AzVM](/powershell/module/az.compute/get-azvm) para obter o `myVM`diretor de serviço para o VM nomeado , que foi criado quando permitimos a identidade gerida. Em seguida, use [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) **Reader** para dar ao Leitor `myStorageAcct`VM acesso a uma conta de armazenamento chamada:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Passos seguintes

- [Identidade gerida para visão geral dos recursos do Azure](overview.md)
- Para permitir a identidade gerida num VM Azure, consulte [as identidades geridas pela Configure para os recursos Azure num Azure VM utilizando powerShell](qs-configure-powershell-windows-vm.md).
