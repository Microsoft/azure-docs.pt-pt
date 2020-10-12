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
ms.date: 12/06/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 13c69dda1e300bcff95b6a017fdeb308a6bbf3a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969253"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Atribuir um acesso de identidade gerido a um recurso utilizando o Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Uma vez configurado um recurso Azure com uma identidade gerida, pode dar à identidade gerida acesso a outro recurso, como qualquer diretor de segurança. Este exemplo mostra-lhe como dar a uma máquina virtual Azure ou à balança de máquinas virtual acesso de identidade gerida a uma conta de armazenamento Azure usando O Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os scripts de exemplo, tem duas opções:
    - Utilize o [Azure Cloud Shell,](../../cloud-shell/overview.md)que pode abrir utilizando o botão **Try It** no canto superior direito dos blocos de código.
    - Executar scripts localmente instalando a versão mais recente do [Azure CLI,](/cli/azure/install-azure-cli)em seguida, inicie sessão no Azure usando [login az](/cli/azure/reference-index#az-login). Utilize uma conta associada à subscrição do Azure na qual pretende criar recursos.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Use o Azure RBAC para atribuir um acesso de identidade gerido a outro recurso

Depois de ter ativado a identidade gerida num recurso Azure, como uma [máquina virtual Azure](qs-configure-cli-windows-vm.md) ou [um conjunto de escala de máquina virtual Azure:](qs-configure-cli-windows-vmss.md) 

1. Neste exemplo, estamos a dar acesso a uma máquina virtual Azure a uma conta de armazenamento. Primeiro usamos [a lista de recursos az](/cli/azure/resource/#az-resource-list) para obter o principal de serviço para a máquina virtual chamada myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Para um conjunto de escala de máquina virtual Azure, o comando é o mesmo, exceto aqui, obtém-se o principal de serviço para o conjunto de escala de máquina virtual chamado "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Assim que tiver o ID principal do serviço, utilize [a az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) para dar à máquina virtual ou conjunto de escala de máquina virtual "Reader" acesso a uma conta de armazenamento chamada "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para visão geral dos recursos da Azure](overview.md)
- Para permitir a identidade gerida numa máquina virtual Azure, consulte [identidades geridas configure para recursos Azure num VM Azure utilizando O Azure CLI](qs-configure-cli-windows-vm.md).
- Para permitir a identidade gerida num conjunto de escala de máquina virtual Azure, consulte [identidades geridas Configure para recursos Azure numa escala de máquina virtual definida utilizando O Azure CLI](qs-configure-cli-windows-vmss.md).
