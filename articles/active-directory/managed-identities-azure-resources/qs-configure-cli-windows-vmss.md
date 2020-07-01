---
title: Configurar identidades geridas em conjunto de escala de máquina virtual - Azure CLI - Azure AD
description: Instruções passo a passo para configurar o sistema e identidades geridas atribuídas pelo utilizador num conjunto de escala de máquina virtual Azure, utilizando o Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: MarkusVi
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969307070d23f9892105b2f620ee839356f46330
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85609177"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Configure identidades geridas para recursos Azure em um conjunto de escala de máquina virtual usando Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código. 

Neste artigo, você aprende a executar as seguintes identidades geridas para operações de recursos Azure em um conjunto de escala de máquina virtual Azure, usando o Azure CLI:
- Ativar e desativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual Azure
- Adicione e remova uma identidade gerida atribuída pelo utilizador num conjunto de escala de máquina virtual Azure


## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gestão neste artigo, a sua conta necessita das seguintes atribuições de controlo de acesso baseados em funções Azure:

    > [!NOTE]
    > Não são necessárias atribuições adicionais de diretório ad AD.

    - [Contribuinte de máquinas virtuais](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de escala de máquina virtual e ativar e remover sistema e/ou identidade gerida atribuída pelo utilizador a partir de um conjunto de escala de máquina virtual.
    - [Papel de Contribuinte de Identidade Gerido](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade gerida atribuída pelo utilizador.
    - [Função de Operador de Identidade Gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade gerida atribuída pelo utilizador de e para um conjunto de escala de máquina virtual.
- Para executar os exemplos de script do CLI, tem três opções:
    - Utilize a Casca de [Nuvem Azure](../../cloud-shell/overview.md) a partir do portal Azure (ver secção seguinte).
    - Utilize o Azure Cloud Shell incorporado através do botão "Try It", localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se preferir utilizar uma consola CLI local. 
      
      > [!NOTE]
      > Os comandos foram atualizados para refletir a mais recente versão do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprende-se a ativar e desativar a identidade gerida atribuída pelo sistema para um conjunto de escala de máquina virtual Azure utilizando o Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema durante a criação de um conjunto de escala de máquina virtual Azure

Para criar um conjunto de escala de máquina virtual com a identidade gerida atribuída pelo sistema:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta associada à subscrição Azure sob a qual pretende implementar o conjunto de escala de máquina virtual:

   ```azurecli-interactive
   az login
   ```

2. Crie um [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) para a contenção e implantação do seu conjunto de escalas de máquinas virtuais e seus recursos relacionados, utilizando a [criação do grupo AZ](/cli/azure/group/#az-group-create). Pode saltar este passo se já tiver um grupo de recursos que gostaria de utilizar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. [Crie](/cli/azure/vmss/#az-vmss-create) um conjunto de escala de máquina virtual. O exemplo a seguir cria um conjunto de escala de máquina virtual chamado *myVMSS* com uma identidade gerida atribuída ao sistema, conforme solicitado pelo `--assign-identity` parâmetro. Os parâmetros `--admin-username` e `--admin-password` especificam o nome e a palavra-passe da conta de utilizador administrativo para início de sessão na máquina virtual. Atualize estes valores conforme adequado para o seu ambiente: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual Azure existente

Se precisar de ativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual Azure existente:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta que esteja associada à subscrição Azure que contenha o conjunto de escala de máquina virtual.

   ```azurecli-interactive
   az login
   ```

2. [Permitir](/cli/azure/vmss/identity/#az-vmss-identity-assign) uma identidade gerida atribuída pelo sistema a um VM existente:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desativar a identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual Azure

Se tiver um conjunto de escala de máquina virtual que já não necessita da identidade gerida atribuída ao sistema, mas que ainda precisa de identidades geridas atribuídas pelo utilizador, utilize o seguinte comando:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se tiver uma máquina virtual que já não necessita de identidade gerida atribuída ao sistema e não tiver identidades geridas atribuídas ao utilizador, utilize o seguinte comando:

> [!NOTE]
> O valor `none` é sensível a casos. Deve ser minúsculo. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>identidade gerida atribuída pelo utilizador

Nesta secção, aprende-se a ativar e remover uma identidade gerida atribuída ao utilizador utilizando o Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuída ao utilizador durante a criação de um conjunto de escala de máquina virtual

Esta secção acompanha-o através da criação de um conjunto de escala de máquina virtual e atribuição de uma identidade gerida atribuída pelo utilizador ao conjunto de escala de máquina virtual. Se já tiver um conjunto de balança de máquina virtual que pretende utilizar, salte esta secção e proceda à seguinte.

1. Pode saltar este passo se já tiver um grupo de recursos que gostaria de utilizar. Crie um [grupo de recursos](~/articles/azure-resource-manager/management/overview.md#terminology) para a contenção e implantação da sua identidade gerida atribuída pelo utilizador, utilizando a [criação do grupo AZ](/cli/azure/group/#az-group-create). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade gerida atribuída pelo utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade gerida atribuída pelo utilizador é criada e o parâmetro `-n` especifica o respetivo nome. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   A resposta contém detalhes para a identidade gerida atribuída pelo utilizador criada, semelhante à seguinte. O valor de recursos `id` atribuído à identidade gerida atribuída pelo utilizador é utilizado no passo seguinte.

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

3. [Crie](/cli/azure/vmss/#az-vmss-create) um conjunto de escala de máquina virtual. O exemplo a seguir cria um conjunto de escala de máquina virtual associado à nova identidade gerida atribuída pelo utilizador, conforme especificado pelo `--assign-identity` parâmetro. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY>` pelos seus próprios valores. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Atribua uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual existente

1. Crie uma identidade gerida atribuída pelo utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade gerida atribuída pelo utilizador é criada e o parâmetro `-n` especifica o respetivo nome. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A resposta contém detalhes para a identidade gerida atribuída pelo utilizador criada, semelhante à seguinte.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [Atribua](/cli/azure/vmss/identity) a identidade gerida atribuída ao utilizador para o seu conjunto de escala de máquina virtual. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. A `<USER ASSIGNED IDENTITY>` propriedade de recursos atribuído pelo utilizador é a propriedade de recursos `name` atribuídos pelo utilizador, tal como criado no passo anterior:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remova uma identidade gerida atribuída pelo utilizador de um conjunto de escala de máquina virtual Azure

Para [remover](/cli/azure/vmss/identity#az-vmss-identity-remove) uma identidade gerida atribuída pelo utilizador de uma utilização de escala de máquina virtual `az vmss identity remove` . Se esta for a única identidade gerida atribuída ao utilizador atribuída ao conjunto de escala de máquina virtual, `UserAssigned` será removida do valor do tipo de identidade.  Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. A `<USER ASSIGNED IDENTITY>` propriedade da identidade gerida atribuída ao `name` utilizador, que pode ser encontrada na secção de identidade do conjunto de escala de máquina virtual `az vmss identity show` utilizando:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Se o seu conjunto de escala de máquina virtual não tiver uma identidade gerida atribuída ao sistema e pretender remover todas as identidades geridas atribuídas pelo utilizador, utilize o seguinte comando:

> [!NOTE]
> O valor `none` é sensível a casos. Deve ser minúsculo.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se o seu conjunto de escala de máquina virtual tiver identidades geridas atribuídas ao sistema e atribuídas ao utilizador, pode remover todas as identidades atribuídas pelo utilizador, mudando para utilizar apenas a identidade gerida atribuída pelo sistema. Utilize o seguinte comando:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para visão geral dos recursos da Azure](overview.md)
- Para a criação completa da balança de máquinas virtual Azure Quickstart, consulte: 

  - [Criar um conjunto de balança de máquina virtual com CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















