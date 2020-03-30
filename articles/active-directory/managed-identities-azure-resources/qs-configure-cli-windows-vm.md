---
title: Configure identidades geridas em Azure VM usando Azure CLI - Azure AD
description: Instruções passo a passo para configurar o sistema e as identidades geridas atribuídas pelo utilizador num Azure VM utilizando o Azure CLI.
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
ms.openlocfilehash: 2f2efaceefc53b3c0b5dfd899baf9fd30fdf9a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244150"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Configure identidades geridas para recursos Azure em um Azure VM usando O ClI Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código. 

Neste artigo, utilizando o Azure CLI, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num Azure VM:

- Ativar e desativar a identidade gerida atribuída pelo sistema num VM Azure
- Adicione e remova uma identidade gerida atribuída pelo utilizador num VM Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos do script CLI, tem três opções:
    - Utilize a Casca de [Nuvem Azure](../../cloud-shell/overview.md) a partir do portal Azure (ver secção seguinte).
    - Utilize a casca de nuvem azure incorporada através do botão "Try It", localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) se preferir utilizar uma consola CLI local. 
      
      > [!NOTE]
      > Os comandos foram atualizados para refletir a mais recente versão do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprende-se a ativar e desativar a identidade gerida atribuída pelo sistema num Azure VM utilizando o Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Ativar identidade gerida atribuída pelo sistema durante a criação de um VM Azure

Para criar um VM Azure com a identidade gerida atribuída pelo sistema, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta que esteja associada à subscrição do Azure na qual pretende implementar a VM:

   ```azurecli-interactive
   az login
   ```

2. Crie uma [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) para contenção e implementação da VM e os respetivos recursos relacionados, utilizando [az group create](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Crie uma VM com [az vm create](/cli/azure/vm/#az-vm-create). O exemplo seguinte cria um VM denominado *myVM* com uma identidade gerida `--assign-identity` atribuída pelo sistema, conforme solicitado pelo parâmetro. Os parâmetros `--admin-username` e `--admin-password` especificam o nome e a palavra-passe da conta de utilizador administrativo para início de sessão na máquina virtual. Atualize estes valores conforme adequado para o seu ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Ativar a identidade gerida atribuída pelo sistema num VM Azure existente

Para permitir a identidade gerida atribuída pelo sistema num VM, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta associada à subscrição Azure que contenha o VM.

   ```azurecli-interactive
   az login
   ```

2. Utilize a [az vm designada](/cli/azure/vm/identity/) com o `identity assign` comando, permitir a identidade atribuída ao sistema a um VM existente:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Desativar a identidade atribuída ao sistema a partir de um VM Azure

Para desativar a identidade gerida atribuída pelo sistema num VM, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

Se tiver uma Máquina Virtual que já não necessite da identidade atribuída ao sistema, mas que ainda necessite de identidades atribuídas ao utilizador, utilize o seguinte comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se tiver uma máquina virtual que já não necessite de identidade atribuída ao sistema e não possui identidades atribuídas ao utilizador, utilize o seguinte comando:

> [!NOTE]
> O `none` valor é sensível ao caso. Deve ser minúsculo. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída ao utilizador

Nesta secção, você aprenderá a adicionar e remover uma identidade gerida atribuída pelo utilizador de um Azure VM usando o Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador durante a criação de um VM Azure

Para atribuir uma identidade atribuída ao utilizador a um VM durante a sua criação, a sua conta necessita das atribuições de funções de Colaborador de [Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e Operador de [Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Não são necessárias atribuições adicionais de diretório da AD.

1. Pode saltar este passo se já tiver um grupo de recursos que gostaria de utilizar. Crie um grupo de [recursos](~/articles/azure-resource-manager/management/overview.md#terminology) para contenção e implementação da sua identidade gerida atribuída ao utilizador, utilizando a criação do [grupo Az](/cli/azure/group/#az-group-create). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade gerida atribuída pelo utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade gerida atribuída pelo utilizador é criada e o parâmetro `-n` especifica o respetivo nome.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   A resposta contém detalhes para a identidade gerida atribuída pelo utilizador criada, semelhante à seguinte. O valor de identificação do recurso atribuído à identidade gerida atribuída ao utilizador é utilizado na etapa seguinte.

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

3. Crie uma VM com [az vm create](/cli/azure/vm/#az-vm-create). O exemplo seguinte cria um VM associado à nova identidade atribuída `--assign-identity` ao utilizador, conforme especificado pelo parâmetro. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador a um VM Azure existente

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções de Colaborador de [Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e Operador de [Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Não são necessárias atribuições adicionais de diretório da AD.

1. Crie uma identidade atribuída ao utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O `-g` parâmetro especifica o grupo de recursos onde a identidade `-n` atribuída ao utilizador é criada, e o parâmetro especifica o seu nome. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

    > [!IMPORTANT]
    > A criação de identidades geridas atribuídas ao utilizador com caracteres especiais (isto é, sublinhado) no nome não é suportada atualmente. Por favor, use caracteres alfanuméricos. Volte mais tarde para obter atualizações.  Para mais informações consulte [faQs e questões conhecidas](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A resposta contém detalhes para a identidade gerida atribuída pelo utilizador criada, semelhante à seguinte. 

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

2. Atribuir a identidade atribuída ao utilizador ao seu VM utilizando a atribuição de [identidade Az vM](/cli/azure/vm). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. Trata-se `<USER ASSIGNED IDENTITY NAME>` da propriedade de recursos `name` da identidade gerida atribuída pelo utilizador, tal como criado na etapa anterior:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remova uma identidade gerida atribuída ao utilizador de um VM Azure

Para remover uma identidade atribuída ao utilizador a um VM, a sua conta necessita da atribuição da função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 

Se esta for a única identidade gerida atribuída ao utilizador `UserAssigned` atribuída à máquina virtual, será removida do valor do tipo de identidade.  Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` será propriedade da `name` identidade atribuída ao utilizador, que pode ser encontrada na `az vm identity show`secção de identidade da máquina virtual utilizando:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Se o seu VM não tiver uma identidade gerida atribuída ao sistema e pretender remover todas as identidades atribuídas ao utilizador, utilize o seguinte comando:

> [!NOTE]
> O `none` valor é sensível ao caso. Deve ser minúsculo.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se o seu VM tiver identidades atribuídas ao sistema e atribuídas ao utilizador, pode remover todas as identidades atribuídas ao utilizador, mudando para utilização apenas atribuída ao sistema. Utilize o seguinte comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Passos seguintes
- [Identidades geridas para a visão geral dos recursos do Azure](overview.md)
- Para a criação full Azure VM Quickstarts, consulte: 
  - [Criar uma máquina virtual Windows com CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Crie uma máquina virtual Linux com CLI](../../virtual-machines/linux/quick-create-cli.md) 

















