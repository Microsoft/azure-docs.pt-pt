---
title: Como configurar o sistema e identidades gerenciadas atribuídas pelo usuário em uma VM do Azure usando CLI do Azure
description: Instruções passo a passo para configurar o sistema e identidades gerenciadas atribuídas pelo usuário em uma VM do Azure usando CLI do Azure.
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
ms.openlocfilehash: 272315346091bacb15aef02184e1cc72d64ed49d
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309807"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando CLI do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, usando o CLI do Azure, você aprende a executar as seguintes identidades gerenciadas para operações de recursos do Azure em uma VM do Azure:

- Habilitar e desabilitar a identidade gerenciada atribuída pelo sistema em uma VM do Azure
- Adicionar e remover uma identidade gerenciada atribuída pelo usuário em uma VM do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)** .
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos de script da CLI, tem três opções:
    - Uso [Azure Cloud Shell](../../cloud-shell/overview.md) do portal do Azure (consulte a secção seguinte).
    - Utilize o embedded Azure Cloud Shell através do "Experimente-lo" botão do, localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente do CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se preferir usar um console da CLI local. 
      
      > [!NOTE]
      > Os comandos foram atualizados para refletir a versão mais recente do [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá a habilitar e desabilitar a identidade gerenciada atribuída pelo sistema em uma VM do Azure usando o CLI do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Habilitar a identidade gerenciada atribuída pelo sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade gerenciada atribuída pelo sistema habilitada, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta que esteja associada à subscrição do Azure na qual pretende implementar a VM:

   ```azurecli-interactive
   az login
   ```

2. Crie uma [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para contenção e implementação da VM e os respetivos recursos relacionados, utilizando [az group create](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Crie uma VM com [az vm create](/cli/azure/vm/#az-vm-create). O exemplo a seguir cria uma VM chamada *myVM* com uma identidade gerenciada atribuída pelo sistema, conforme solicitado `--assign-identity` pelo parâmetro. Os parâmetros `--admin-username` e `--admin-password` especificam o nome e a palavra-passe da conta de utilizador administrativo para início de sessão na máquina virtual. Atualize estes valores conforme adequado para o seu ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Habilitar identidade gerenciada atribuída pelo sistema em uma VM do Azure existente

Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Use uma conta que esteja associada à assinatura do Azure que contém a VM.

   ```azurecli-interactive
   az login
   ```

2. Use [AZ VM Identity Assign](/cli/azure/vm/identity/) com o `identity assign` comando habilitar a identidade atribuída pelo sistema para uma VM existente:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Desabilitar a identidade atribuída pelo sistema de uma VM do Azure

Para desabilitar a identidade gerenciada atribuída pelo sistema em uma VM, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

Se você tiver uma máquina virtual que não precisa mais da identidade atribuída pelo sistema, mas ainda precisar de identidades atribuídas pelo usuário, use o seguinte comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se você tiver uma máquina virtual que não precisa mais de identidade atribuída pelo sistema e não tiver identidades atribuídas pelo usuário, use o seguinte comando:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Ele deve estar em minúsculas. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá a adicionar e remover uma identidade gerenciada atribuída pelo usuário de uma VM do Azure usando CLI do Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade gerenciada atribuída pelo usuário durante a criação de uma VM do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM durante sua criação, sua conta precisa das atribuições de função [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Você pode ignorar esta etapa se já tiver um grupo de recursos que deseja usar. Crie um [grupo de recursos](~/articles/azure-resource-manager/resource-group-overview.md#terminology) para contenção e implantação de sua identidade gerenciada atribuída pelo usuário, usando [AZ Group Create](/cli/azure/group/#az-group-create). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade gerida atribuída pelo utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade gerida atribuída pelo utilizador é criada e o parâmetro `-n` especifica o respetivo nome.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   A resposta contém detalhes para a identidade gerenciada atribuída pelo usuário criada, semelhante à seguinte. O valor da ID de recurso atribuído à identidade gerenciada atribuída pelo usuário é usado na etapa a seguir.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
       "location": "westcentralus",
       "name": "<USER ASSIGNED IDENTITY NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Crie uma VM com [az vm create](/cli/azure/vm/#az-vm-create). O exemplo a seguir cria uma VM associada à nova identidade atribuída pelo usuário, conforme especificado pelo `--assign-identity` parâmetro. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerenciada atribuída pelo usuário a uma VM do Azure existente

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Crie uma identidade atribuída ao utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O `-g` parâmetro especifica o grupo de recursos onde a identidade atribuída pelo usuário é criada e o `-n` parâmetro especifica seu nome. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

    > [!IMPORTANT]
    > Atualmente, não há suporte para a criação de identidades gerenciadas atribuídas pelo usuário com caracteres especiais (ou seja, sublinhado) no nome. Use caracteres alfanuméricos. Volte mais tarde para obter atualizações.  Para obter mais informações [, consulte perguntas frequentes e problemas conhecidos](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A resposta contém detalhes para a identidade gerenciada atribuída pelo usuário criada, semelhante à seguinte. 

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Atribua a identidade atribuída pelo usuário à sua VM usando [AZ VM Identity Assign](/cli/azure/vm). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY NAME>` é a propriedade de recurso `name` da identidade gerenciada atribuída pelo usuário, conforme criada na etapa anterior:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerenciada atribuída pelo usuário de uma VM do Azure

Para remover uma identidade atribuída pelo usuário a uma VM, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . 

Se essa for a única identidade gerenciada atribuída pelo usuário atribuída à máquina virtual, `UserAssigned` será removida do valor do tipo de identidade.  Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` será a propriedade da `name` identidade atribuída pelo usuário, que pode ser encontrada na seção de identidade da máquina virtual usando `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Se sua VM não tiver uma identidade gerenciada atribuída pelo sistema e você quiser remover todas as identidades atribuídas pelo usuário dela, use o seguinte comando:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Ele deve estar em minúsculas.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se sua VM tiver identidades atribuídas pelo sistema e com o usuário, você poderá remover todas as identidades atribuídas pelo usuário alternando para uso somente atribuído pelo sistema. Utilize o seguinte comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Passos seguintes
- [Identidades geridas de descrição geral de recursos do Azure](overview.md)
- Para obter os guias de início rápido de criação de VM do Azure completos, consulte: 
  - [Criar uma máquina virtual do Windows com a CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Criar uma máquina virtual do Linux com a CLI](../../virtual-machines/linux/quick-create-cli.md) 

















