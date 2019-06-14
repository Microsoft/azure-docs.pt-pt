---
title: Como atribuir acesso uma identidade gerida a um recurso do Azure com a CLI do Azure
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
ms.openlocfilehash: 2fb16b1762f1e5330cf058c37a6b7e0f008eb447
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60290983"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Atribuir um acesso de identidade gerida a um recurso com a CLI do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Assim que tiver configurado um recurso do Azure com uma identidade gerida, pode dar o acesso de identidade gerida para outro recurso, tal como qualquer entidade de segurança. Este exemplo mostra como conceder acesso de identidade gerida do conjunto de dimensionamento de máquina virtual ou máquina virtual do Azure para uma conta de armazenamento do Azure com a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)** .
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos de script da CLI, tem três opções:
    - Uso [Azure Cloud Shell](../../cloud-shell/overview.md) do portal do Azure (consulte a secção seguinte).
    - Utilize o embedded Azure Cloud Shell através do "Experimente-lo" botão do, localizado no canto superior direito de cada bloco de código.
    - [Instalar a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se preferir utilizar uma consola CLI local. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Utilizar o RBAC para atribuir um acesso de identidade gerida para outro recurso

Depois de ativar a identidade gerida num recurso do Azure, tal como um [máquina virtual do Azure](qs-configure-cli-windows-vm.md) ou [conjunto de dimensionamento de máquina virtual do Azure](qs-configure-cli-windows-vmss.md): 

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta que seja associada à subscrição do Azure sob a qual pretende implementar o conjunto de dimensionamento VM ou numa máquina virtual:

   ```azurecli-interactive
   az login
   ```

2. Neste exemplo, estamos oferecendo uma máquina virtual do Azure de acesso a uma conta de armazenamento. Primeiro, usamos [lista de recursos de az](/cli/azure/resource/#az-resource-list) para obter o principal de serviço para a máquina virtual com o nome myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Para obter um conjunto de dimensionamento de máquina virtual do Azure, o comando é o mesmo, exceto aqui, obtém o principal de serviço para o conjunto de dimensionamento de máquinas virtuais com o nome "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Assim que tiver o ID de principal de serviço, utilize [criação da atribuição de função de az](/cli/azure/role/assignment#az-role-assignment-create) dar a máquina virtual ou o dimensionamento de máquinas virtuais, definir acesso de "Leitor de" para uma conta de armazenamento denominada "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Passos Seguintes

- [Identidades geridas de descrição geral de recursos do Azure](overview.md)
- Para ativar a identidade gerida numa máquina virtual do Azure, consulte [configurar geridos identidades para recursos do Azure na VM do Azure com a CLI do Azure](qs-configure-cli-windows-vm.md).
- Para ativar a identidade gerida num conjunto de dimensionamento de máquina virtual do Azure, consulte [configurar geridos identidades para recursos do Azure num conjunto de dimensionamento da CLI do Azure](qs-configure-cli-windows-vmss.md).
