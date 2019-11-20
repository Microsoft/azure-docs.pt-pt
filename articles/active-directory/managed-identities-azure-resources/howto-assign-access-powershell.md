---
title: Atribuir um acesso de identidade gerenciada a um recurso usando o PowerShell-Azure AD
description: Instruções passo a passo instruções para atribuir uma identidade gerida num recurso, o acesso a outro recurso, com o PowerShell.
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
ms.openlocfilehash: 82fa5019e740d16d0b97111fcf8dbc4f6c91d57b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184006"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Atribuir um acesso de identidade gerida a um recurso com o PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Assim que tiver configurado um recurso do Azure com uma identidade gerida, pode dar o acesso de identidade gerida para outro recurso, tal como qualquer entidade de segurança. Este exemplo mostra como conceder acesso de identidade gerida de uma máquina virtual do Azure para uma conta de armazenamento do Azure com o PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)** .
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Utilizar o RBAC para atribuir um acesso de identidade gerida para outro recurso

Depois de ativar a identidade gerida num recurso do Azure, [como uma VM do Azure](qs-configure-powershell-windows-vm.md):

1. Inicie sessão no Azure com o `Connect-AzAccount` cmdlet. Utilize uma conta que seja associada à subscrição do Azure na qual configurou a identidade gerida:

   ```powershell
   Connect-AzAccount
   ```
2. Neste exemplo, estamos oferecendo um acesso de VM do Azure para uma conta de armazenamento. Primeiro, usamos [Get-AzVM](/powershell/module/az.compute/get-azvm) para obter a entidade de serviço para a VM chamada `myVM`, que foi criada quando habilitamos a identidade gerenciada. Em seguida, use [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) para conceder ao **leitor** de VM acesso a uma conta de armazenamento chamada `myStorageAcct`:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Passos seguintes

- [Identidade gerida para descrição geral de recursos do Azure](overview.md)
- Para ativar a identidade gerida numa VM do Azure, consulte [configurar geridos identidades para recursos do Azure na VM do Azure com o PowerShell](qs-configure-powershell-windows-vm.md).
