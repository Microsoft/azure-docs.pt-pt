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
ms.date: 12/06/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ae2da130f61d31db4904ed2dd5ac18444929950
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177504"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Atribuir um acesso de identidade gerido a um recurso utilizando o PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Uma vez configurado um recurso Azure com uma identidade gerida, pode dar à identidade gerida acesso a outro recurso, como qualquer diretor de segurança. Este exemplo mostra-lhe como dar acesso de identidade gerido por uma máquina virtual Azure a uma conta de armazenamento Azure utilizando o PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Use o Azure RBAC para atribuir um acesso de identidade gerido a outro recurso

Depois de ter ativado a identidade gerida num recurso Azure, [como um Azure VM:](qs-configure-powershell-windows-vm.md)

1. Inscreva-se no Azure com o `Connect-AzAccount` cmdlet. Utilize uma conta associada à subscrição Azure sob a qual configuraste a identidade gerida:

   ```powershell
   Connect-AzAccount
   ```
2. Neste exemplo, estamos a dar um acesso Azure VM a uma conta de armazenamento. Primeiro [usamos o Get-AzVM](/powershell/module/az.compute/get-azvm) para obter o principal de serviço para o VM nomeado `myVM` , que foi criado quando permitimos a identidade gerida. Em seguida, utilize [a assinatura da New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) para dar ao **Leitor** VM acesso a uma conta de armazenamento chamada `myStorageAcct` :

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Passos seguintes

- [Identidade gerida para visão geral dos recursos da Azure](overview.md)
- Para permitir a identidade gerida num Azure VM, consulte [identidades geridas configure para recursos Azure num VM Azure utilizando o PowerShell](qs-configure-powershell-windows-vm.md).
