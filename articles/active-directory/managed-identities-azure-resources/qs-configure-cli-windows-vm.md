---
title: Como configurar o sistema e atribuído ao utilizador identidades geridas na VM do Azure com a CLI do Azure
description: Passo a passo instruções para configurar o sistema e atribuído ao utilizador identidades geridas na VM do Azure com a CLI do Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/10/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d3bb9708c7bab41f87ad9c2b6ae18ac62849a2d
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223925"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Configurar identidades geridas para recursos do Azure na VM do Azure com a CLI do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, com a CLI do Azure, aprenderá a efetuar as seguintes identidades geridas para operações de recursos do Azure numa VM do Azure:

- Ativar e desativar a identidade gerida atribuído de sistema numa VM do Azure
- Adicionar e remover uma identidade gerida atribuído ao utilizador numa VM do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos de script da CLI, tem três opções:
    - Uso [Azure Cloud Shell](../../cloud-shell/overview.md) do portal do Azure (consulte a secção seguinte).
    - Utilize o embedded Azure Cloud Shell através do "Experimente-lo" botão do, localizado no canto superior direito de cada bloco de código.
    - [Instalar a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se preferir utilizar uma consola CLI local. 
      
      > [!NOTE]
      > Os comandos foram atualizados para refletir a versão mais recente dos [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Atribuído ao sistema de identidade gerida

Nesta secção, saiba como ativar e desativar a identidade gerida atribuído de sistema na VM do Azure com a CLI do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Ativar a identidade gerida atribuído ao sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade gerida atribuídos do sistema ativada, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função.  Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta que esteja associada à subscrição do Azure na qual pretende implementar a VM:

   ```azurecli-interactive
   az login
   ```

2. Crie uma [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para contenção e implementação da VM e os respetivos recursos relacionados, utilizando [az group create](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Crie uma VM com [az vm create](/cli/azure/vm/#az-vm-create). O exemplo seguinte cria uma VM com o nome *myVM* com um atribuído de sistema de identidade gerida, conforme solicitado pelo `--assign-identity` parâmetro. Os parâmetros `--admin-username` e `--admin-password` especificam o nome e a palavra-passe da conta de utilizador administrativo para início de sessão na máquina virtual. Atualize estes valores conforme adequado para o seu ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Ativar a identidade gerida atribuído de sistema numa VM do Azure existente

Para ativar a identidade gerida atribuído de sistema numa VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função.  Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta que está associada à subscrição do Azure que contém a VM.

   ```azurecli-interactive
   az login
   ```

2. Uso [atribuir identidade da vm de az](/cli/azure/vm/identity/) com o `identity assign` comando ativar a identidade do sistema atribuído a uma VM existente:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Desativar a identidade atribuída de sistema de uma VM do Azure

Para desativar a identidade gerida atribuído de sistema numa VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função.  Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

Se tiver uma Máquina Virtual que já não é precisa a identidade atribuída de sistema, mas ainda precisa identidiades atribuídas, utilize o seguinte comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se tiver uma máquina virtual que já não necessita de identidade atribuída de sistema e tem não identidiades atribuídas, utilize o seguinte comando:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Tem de estar em minúsculas. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```
> [!NOTE]
> Se aprovisionou a identidade gerida para a extensão VM de recursos do Azure (para ser preterida), terá de removê-lo com [delete de extensão az vm](https://docs.microsoft.com/cli/azure/vm/). Para obter mais informações, consulte [migre da extensão de VM para o Azure IMDS para autenticação](howto-migrate-vm-extension.md).

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, irá aprender como adicionar e remover uma identidade gerida atribuído ao utilizador a partir de uma VM do Azure com a CLI do Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade gerida atribuído ao utilizador durante a criação de uma VM do Azure

Para atribuir uma identidade de utilizador atribuído a uma VM durante sua criação, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) atribuições de funções. Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Pode ignorar este passo se já tiver um grupo de recursos que pretende utilizar. Criar uma [grupo de recursos](~/articles/azure-resource-manager/resource-group-overview.md#terminology) para a contenção e a implementação da sua identidade gerida atribuído ao utilizador, utilizando [criar grupo az](/cli/azure/group/#az-group-create). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade gerida atribuída pelo utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade gerida atribuída pelo utilizador é criada e o parâmetro `-n` especifica o respetivo nome.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   A resposta contém detalhes para a identidade gerida de atribuído ao utilizador que criou, semelhante ao seguinte. O valor do ID de recurso atribuído para a identidade gerida atribuído ao utilizador é utilizado no passo seguinte.

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

3. Crie uma VM com [az vm create](/cli/azure/vm/#az-vm-create). O exemplo seguinte cria uma VM associada com a identidade de utilizador atribuído novos, como especificado pelo `--assign-identity` parâmetro. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerida atribuído ao utilizador a uma VM do Azure existente

Para atribuir uma identidade de utilizador atribuído a uma VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) atribuições de funções. Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Crie uma identidade atribuída ao utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O `-g` parâmetro especifica o grupo de recursos em que é criada a identidade de utilizador atribuído, e o `-n` parâmetro especifica o respetivo nome. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

    > [!IMPORTANT]
    > Atualmente, o criação atribuído ao utilizador identidades geridas com carateres especiais (por exemplo, um caráter de sublinhado), o nome não é suportada. Utilize carateres alfanuméricos. Volte mais tarde para obter atualizações.  Para obter mais informações consulte [FAQ e problemas conhecidos](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A resposta contém detalhes para a identidade gerida de atribuído ao utilizador que criou, semelhante ao seguinte. 

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

2. Atribuir a identidade de utilizador atribuído à VM com [atribuir identidade da vm de az](/cli/azure/vm). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY NAME>` é o recurso do utilizador atribuído identidade gerida `name` propriedade, como foi criado no passo anterior:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerida atribuído ao utilizador a partir de uma VM do Azure

Para remover uma identidade de utilizador atribuído a uma VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função. 

Se este é o único utilizador atribuído a identidade atribuída à máquina virtual, gerido `UserAssigned` será removida do valor de tipo de identidade.  Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` será a identidade atribuída por utilizador `name` propriedade, que pode ser encontrada na secção da identidade da máquina virtual usando `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Se a VM não tem uma identidade gerida atribuído de sistema e que pretende remover todas as identidiades atribuídas da mesma, utilize o seguinte comando:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Tem de estar em minúsculas.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se a VM tem ambos sistema atribuído e identidiades atribuídas, pode remover todas as identidiades atribuídas ao mudar para utilizar apenas atribuído de sistema. Utilize o seguinte comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Passos Seguintes
- [Identidades geridas de descrição geral de recursos do Azure](overview.md)
- Para a criação de VM do Azure completa inícios rápidos, consulte: 
  - [Criar uma máquina virtual do Windows com a CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Criar uma máquina virtual Linux com a CLI](../../virtual-machines/linux/quick-create-cli.md) 

















