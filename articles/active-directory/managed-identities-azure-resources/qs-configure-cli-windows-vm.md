---
title: Configure identidades geridas em Azure VM usando Azure CLI - Azure AD
description: Instruções passo a passo para configurar o sistema e identidades geridas atribuídas pelo utilizador num Azure VM utilizando o Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31e4f80aa8ffec96538b53ac64df6918c632a950
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892473"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Configure identidades geridas para recursos Azure em um Azure VM usando Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código. 

Neste artigo, utilizando o CLI Azure, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num Azure VM:

- Ativar e desativar a identidade gerida atribuída pelo sistema num VM Azure
- Adicione e remova uma identidade gerida atribuída pelo utilizador num VM Azure

Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com identidades geridas para recursos Azure, veja [o que são identidades geridas para os recursos do Azure?](overview.md) Para saber mais sobre os tipos de identidade geridos atribuídos pelo sistema e atribuídos pelo utilizador, consulte [os tipos de identidade geridos](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprende-se a ativar e desativar a identidade gerida atribuída pelo sistema num Azure VM utilizando o Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Permitir a identidade gerida atribuída pelo sistema durante a criação de um VM Azure

Para criar um VM Azure com a identidade gerida atribuída pelo sistema, a sua conta precisa da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Crie uma [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) para contenção e implementação da VM e os respetivos recursos relacionados, utilizando [az group create](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. Crie uma VM com [az vm create](/cli/azure/vm/#az-vm-create). O exemplo a seguir cria um VM nomeado *myVM* com uma identidade gerida atribuída ao sistema, conforme solicitado pelo `--assign-identity` parâmetro. Os parâmetros `--admin-username` e `--admin-password` especificam o nome e a palavra-passe da conta de utilizador administrativo para início de sessão na máquina virtual. Atualize estes valores conforme adequado para o seu ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Permitir a identidade gerida atribuída pelo sistema num VM Azure existente

Para ativar a identidade gerida atribuída pelo sistema num VM, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta associada à subscrição Azure que contém o VM.

   ```azurecli-interactive
   az login
   ```

2. Utilizar [a identidade az vm atribuir](/cli/azure/vm/identity/) com o comando permitir a identidade atribuída ao sistema a um `identity assign` VM existente:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Desativar a identidade atribuída ao sistema a partir de um VM Azure

Para desativar a identidade gerida atribuída pelo sistema num VM, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

Se tiver uma Máquina Virtual que já não necessita da identidade atribuída ao sistema, mas que ainda precisa de identidades atribuídas ao utilizador, utilize o seguinte comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se tiver uma máquina virtual que já não necessita de identidade atribuída ao sistema e não tiver identidades atribuídas ao utilizador, utilize o seguinte comando:

> [!NOTE]
> O valor `none` é sensível a casos. Deve ser minúsculo. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, você aprenderá a adicionar e remover uma identidade gerida atribuída ao utilizador de um Azure VM usando Azure CLI. Se criar a sua identidade gerida atribuída pelo utilizador num RG diferente do seu VM. Terá de usar o URL da sua identidade gerida para atribuí-lo ao seu VM.
i.e. --identidades "/subscrições/ <SUBID> /grupos de recursos/ <RESROURCEGROUP> /fornecedores/Microsoft.ManagedIdentity/userAssignedIdentities/<USER_ASSIGNED_ID_NAME>"

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador durante a criação de um VM Azure

Para atribuir uma identidade atribuída ao utilizador a um VM durante a sua criação, a sua conta necessita das atribuições de funções [de Colaborador de Máquinas Virtuais](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e Operador de Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-operator) Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Pode saltar este passo se já tiver um grupo de recursos que gostaria de utilizar. Crie um [grupo de recursos](~/articles/azure-resource-manager/management/overview.md#terminology) para a contenção e implantação da sua identidade gerida atribuída pelo utilizador, utilizando a [criação do grupo AZ](/cli/azure/group/#az-group-create). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade gerida atribuída pelo utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade gerida atribuída pelo utilizador é criada e o parâmetro `-n` especifica o respetivo nome.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   A resposta contém detalhes para a identidade gerida atribuída pelo utilizador criada, semelhante à seguinte. O valor de identificação de recursos atribuído à identidade gerida atribuída pelo utilizador é utilizado no passo seguinte.

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

3. Crie uma VM com [az vm create](/cli/azure/vm/#az-vm-create). O exemplo a seguir cria um VM associado à nova identidade atribuída ao utilizador, conforme especificado pelo `--assign-identity` parâmetro. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador a um Azure VM existente

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções [de Colaborador de Máquinas Virtuais](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e Operador de Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-operator) Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Crie uma identidade atribuída ao utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O `-g` parâmetro especifica o grupo de recursos onde a identidade atribuída ao utilizador é criada, e o parâmetro especifica o `-n` seu nome. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

    > [!IMPORTANT]
    > A criação de identidades geridas atribuídas pelo utilizador com caracteres especiais (ou seja, sublinhar) no nome não é suportada atualmente. Por favor, use caracteres alfanuméricos. Volte mais tarde para obter atualizações.  Para mais informações, consulte [as PERGUNTAS Frequentes e questões conhecidas](known-issues.md)

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

2. Atribua a identidade atribuída ao utilizador para o seu VM utilizando [a designação de identidade az vm](/cli/azure/vm). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. A `<USER ASSIGNED IDENTITY NAME>` propriedade de recursos geridos pelo utilizador atribuído ao `name` utilizador, tal como foi criado no passo anterior. Se criou a sua identidade gerida atribuída ao utilizador num RG diferente do seu VM. Terá de usar o URL da sua identidade gerida.

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerida atribuída pelo utilizador de um VM Azure

Para remover uma identidade atribuída ao utilizador para um VM, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 

Se esta for a única identidade gerida atribuída ao utilizador atribuída à máquina virtual, `UserAssigned` será removida do valor do tipo de identidade.  Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. A `<USER ASSIGNED IDENTITY>` propriedade da identidade atribuída ao `name` utilizador, que pode ser encontrada na secção de identidade da máquina virtual `az vm identity show` utilizando:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Se o seu VM não tiver uma identidade gerida atribuída ao sistema e pretender remover todas as identidades atribuídas ao utilizador, utilize o seguinte comando:

> [!NOTE]
> O valor `none` é sensível a casos. Deve ser minúsculo.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se o seu VM tiver identidades atribuídas ao sistema e atribuídas ao utilizador, pode remover todas as identidades atribuídas pelo utilizador, mudando para utilizar apenas as do sistema atribuídas ao sistema. Utilize o seguinte comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Passos seguintes
- [Identidades geridas para visão geral dos recursos da Azure](overview.md)
- Para a criação completa de Azure VM Quickstarts, consulte: 
  - [Criar uma máquina virtual Windows com CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Criar uma máquina virtual Linux com CLI](../../virtual-machines/linux/quick-create-cli.md)
