---
title: Atribuir um acesso de identidade gerido a um recurso utilizando a Azure CLI - Azure AD
description: Instruções passo a passo para a atribuição de uma identidade gerida num recurso, acesso a outro recurso, utilizando o Azure CLI.
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
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1e1fa22cc36df00b098274002b6bd444be4140ff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783292"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Atribuir um acesso de identidade gerido a um recurso utilizando o Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Uma vez configurado um recurso Azure com uma identidade gerida, pode dar à identidade gerida acesso a outro recurso, como qualquer diretor de segurança. Este exemplo mostra-lhe como dar a uma máquina virtual Azure ou à balança de máquinas virtual acesso de identidade gerida a uma conta de armazenamento Azure usando O Azure CLI.

Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com identidades geridas para recursos Azure, veja [o que são identidades geridas para os recursos do Azure?](overview.md) Para saber mais sobre os tipos de identidade geridos atribuídos pelo sistema e atribuídos pelo utilizador, consulte [os tipos de identidade geridos](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Use o Azure RBAC para atribuir um acesso de identidade gerido a outro recurso

Depois de ter ativado a identidade gerida num recurso Azure, como uma [máquina virtual Azure](qs-configure-cli-windows-vm.md) ou [um conjunto de escala de máquina virtual Azure:](qs-configure-cli-windows-vmss.md) 

1. Neste exemplo, estamos a dar acesso a uma máquina virtual Azure a uma conta de armazenamento. Primeiro usamos [a lista de recursos az](/cli/azure/resource/#az_resource_list) para obter o principal de serviço para a máquina virtual chamada myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Para um conjunto de escala de máquina virtual Azure, o comando é o mesmo, exceto aqui, obtém-se o principal de serviço para o conjunto de escala de máquina virtual chamado "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Assim que tiver o ID principal do serviço, utilize [a az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para dar à máquina virtual ou conjunto de escala de máquina virtual "Reader" acesso a uma conta de armazenamento chamada "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para visão geral dos recursos da Azure](overview.md)
- Para permitir a identidade gerida numa máquina virtual Azure, consulte [identidades geridas configure para recursos Azure num VM Azure utilizando O Azure CLI](qs-configure-cli-windows-vm.md).
- Para permitir a identidade gerida num conjunto de escala de máquina virtual Azure, consulte [identidades geridas Configure para recursos Azure numa escala de máquina virtual definida utilizando O Azure CLI](qs-configure-cli-windows-vmss.md).
