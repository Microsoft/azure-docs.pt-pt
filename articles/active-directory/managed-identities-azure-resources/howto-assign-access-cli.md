---
title: Atribuir um acesso de identidade gerenciada a um recurso usando o CLI do Azure-Azure AD
description: Instruções passo a passo instruções para atribuir uma identidade gerida num recurso, o acesso a outro recurso, com a CLI do Azure.
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
ms.date: 12/06/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b241ac223fd1eb9df2b0a914726d8f37df5f4d88
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547363"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Atribuir um acesso de identidade gerida a um recurso com a CLI do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Assim que tiver configurado um recurso do Azure com uma identidade gerida, pode dar o acesso de identidade gerida para outro recurso, tal como qualquer entidade de segurança. Este exemplo mostra como conceder acesso de identidade gerida do conjunto de dimensionamento de máquina virtual ou máquina virtual do Azure para uma conta de armazenamento do Azure com a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção visão geral](overview.md). **Certifique-se de examinar a [diferença entre uma identidade gerenciada atribuída pelo sistema e](overview.md#how-does-the-managed-identities-for-azure-resources-work)** atribuída pelo usuário.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos de script da CLI, tem três opções:
    - Use [Azure cloud Shell](../../cloud-shell/overview.md) da portal do Azure (consulte a próxima seção).
    - Utilize o embedded Azure Cloud Shell através do "Experimente-lo" botão do, localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente do CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se preferir usar um console da CLI local. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Utilizar o RBAC para atribuir um acesso de identidade gerida para outro recurso

Depois de habilitar a identidade gerenciada em um recurso do Azure, como uma [máquina virtual do Azure](qs-configure-cli-windows-vm.md) ou um [conjunto de dimensionamento de máquinas virtuais do Azure](qs-configure-cli-windows-vmss.md): 

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta que seja associada à subscrição do Azure sob a qual pretende implementar o conjunto de dimensionamento VM ou numa máquina virtual:

   ```azurecli-interactive
   az login
   ```

2. Neste exemplo, estamos oferecendo uma máquina virtual do Azure de acesso a uma conta de armazenamento. Primeiro, usamos a [lista de recursos AZ](/cli/azure/resource/#az-resource-list) para obter a entidade de serviço para a máquina virtual chamada myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Para obter um conjunto de dimensionamento de máquina virtual do Azure, o comando é o mesmo, exceto aqui, obtém o principal de serviço para o conjunto de dimensionamento de máquinas virtuais com o nome "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Depois que você tiver a ID da entidade de serviço, use [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) para fornecer à máquina virtual ou ao conjunto de dimensionamento de máquinas virtuais acesso de "leitor" a uma conta de armazenamento chamada "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Passos seguintes

- [Visão geral de identidades gerenciadas para recursos do Azure](overview.md)
- Para habilitar a identidade gerenciada em uma máquina virtual do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando CLI do Azure](qs-configure-cli-windows-vm.md).
- Para habilitar a identidade gerenciada em um conjunto de dimensionamento de máquinas virtuais do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando CLI do Azure](qs-configure-cli-windows-vmss.md).
