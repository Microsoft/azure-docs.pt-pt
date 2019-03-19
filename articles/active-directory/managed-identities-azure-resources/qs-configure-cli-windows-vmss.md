---
title: Como configurar o sistema e de identidades geridas atribuído ao utilizador um VMSS do Azure com a CLI do Azure
description: Passo a passo instruções para configurar o sistema e de identidades geridas atribuído ao utilizador num VMSS do Azure, cli do Azure.
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
ms.date: 02/15/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34cd03ad6640ac809ce8ac2e8f4fc1070246df27
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57886869"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Configurar identidades geridas para recursos do Azure num conjunto de dimensionamento da CLI do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, aprenderá a efetuar as seguintes identidades geridas para operações de recursos do Azure numa Azure Virtual Machine dimensionamento definido (VMSS), com a CLI do Azure:
- Ativar e desativar a identidade gerida atribuído de sistema num VMSS do Azure
- Adicionar e remover uma identidade gerida atribuído ao utilizador num VMSS do Azure


## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para efetuar as operações de gestão neste artigo, a conta tem das atribuições de controlo de acesso baseado em funções do Azure seguintes:

    > [!NOTE]
    > Nenhum adicionais do Azure AD directory as atribuições de funções necessárias.

    - [Contribuinte de máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e ativar e remover o sistema e/ou atribuído ao utilizador a identidade gerida a partir de um conjunto de dimensionamento de máquina virtual.
    - [Contribuidor de identidade de geridos](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) identidade gerida de função para criar um atribuído ao utilizador.
    - [Gerido operador de identidade](/azure/role-based-access-control/built-in-roles#managed-identity-operator) função para atribuir e remover um atribuído ao utilizador identidade gerida de e para um conjunto de dimensionamento de máquina virtual.
- Para executar os exemplos de script da CLI, tem três opções:
    - Uso [Azure Cloud Shell](../../cloud-shell/overview.md) do portal do Azure (consulte a secção seguinte).
    - Utilize o embedded Azure Cloud Shell através do "Experimente-lo" botão do, localizado no canto superior direito de cada bloco de código.
    - [Instalar a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se preferir utilizar uma consola CLI local. 
      
      > [!NOTE]
      > Os comandos foram atualizados para refletir a versão mais recente dos [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Atribuído ao sistema de identidade gerida

Nesta secção, saiba como ativar e desativar a identidade gerida atribuído de sistema para um VMSS do Azure com a CLI do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuído ao sistema durante a criação de um conjunto de dimensionamento de máquina virtual do Azure

Para criar um conjunto de dimensionamento com a identidade gerida atribuídos do sistema ativada:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta que seja associada à subscrição do Azure sob a qual pretende implementar o conjunto de dimensionamento de máquina virtual:

   ```azurecli-interactive
   az login
   ```

2. Criar uma [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para a contenção e a implantação de seu conjunto de dimensionamento de máquina virtual e respetivos recursos relacionados, usando [criar grupo az](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver um grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Criar um conjunto de dimensionamento usando [az vmss criar](/cli/azure/vmss/#az-vmss-create) . O exemplo seguinte cria um conjunto nomeado de dimensionamento de máquina virtual *myVMSS* com um atribuído de sistema de identidade gerida, conforme solicitado pelo `--assign-identity` parâmetro. Os parâmetros `--admin-username` e `--admin-password` especificam o nome e a palavra-passe da conta de utilizador administrativo para início de sessão na máquina virtual. Atualize estes valores conforme adequado para o seu ambiente: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuído de sistema num conjunto de dimensionamento de máquina virtual do Azure existente

Se precisar de ativar a identidade gerida atribuído de sistema num conjunto de dimensionamento de máquina virtual do Azure existente:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta que está associada à subscrição do Azure que contém o conjunto de dimensionamento de máquina virtual.

   ```azurecli-interactive
   az login
   ```

2. Uso [identidade do vmss az atribuir](/cli/azure/vmss/identity/#az-vmss-identity-assign) comando para ativar um sistema-atribuídas a identidade para uma VM existente gerido:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desativar a identidade gerida atribuído de sistema de um conjunto de dimensionamento de máquina virtual do Azure

Se tiver um conjunto de dimensionamento de máquina virtual que já não precisa de a identidade gerida atribuído de sistema, mas ainda precisa de identidades geridas atribuído ao utilizador, utilize o seguinte comando:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se tiver uma máquina virtual que já não tem atribuído o sistema de identidade gerida e tem não identidades geridas atribuído ao utilizador, utilize o seguinte comando:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Tem de estar em minúsculas. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

Para remover as identidades geridas para a extensão VM de recursos do Azure (planeada para preterição em Janeiro de 2019), utilize [remover a identidade do vmss az](/cli/azure/vmss/identity/) comando para remover o atribuído de sistema de identidade gerida de uma VMSS:

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

## <a name="user-assigned-managed-identity"></a>Atribuído ao utilizador a identidade gerida

Nesta secção, saiba como ativar e remover uma identidade gerida atribuído ao utilizador com a CLI do Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuído ao utilizador durante a criação de um conjunto de dimensionamento de máquinas virtuais

Esta secção descreve a criação de um VMSS e a atribuição de uma identidade gerida atribuído ao utilizador para o VMSS. Se já tiver um VMSS que pretende utilizar, ignore esta secção e avance para a próxima.

1. Pode ignorar este passo se já tiver um grupo de recursos que pretende utilizar. Criar uma [grupo de recursos](~/articles/azure-resource-manager/resource-group-overview.md#terminology) para a contenção e a implementação da sua identidade gerida atribuído ao utilizador, utilizando [criar grupo az](/cli/azure/group/#az-group-create). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade gerida atribuída pelo utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade gerida atribuída pelo utilizador é criada e o parâmetro `-n` especifica o respetivo nome. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   A resposta contém detalhes para a identidade gerida de atribuído ao utilizador que criou, semelhante ao seguinte. O recurso `id` valor atribuído para a identidade gerida atribuído ao utilizador é utilizado no passo seguinte.

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

3. Criar uma com VMSS [az vmss criar](/cli/azure/vmss/#az-vmss-create). O exemplo seguinte cria um VMSS associados com a novo utilizador atribuído identidade gerida, tal como especificado pelo `--assign-identity` parâmetro. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY>` pelos seus próprios valores. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuído ao utilizador a um conjunto de dimensionamento de máquina virtual existente

1. Crie uma identidade gerida atribuída pelo utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade gerida atribuída pelo utilizador é criada e o parâmetro `-n` especifica o respetivo nome. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A resposta contém detalhes para a identidade gerida de atribuído ao utilizador que criou, semelhante ao seguinte.

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

2. Atribuir a identidade gerida atribuído ao utilizador para a sua utilização de VMSS [identidade do vmss az atribuir](/cli/azure/vmss/identity). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VMSS NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` é o recurso da identidade atribuída por utilizador `name` propriedade, como foi criado no passo anterior:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remover uma identidade gerida atribuído ao utilizador a partir de um conjunto de dimensionamento de máquina virtual do Azure

Para remover uma identidade gerida atribuído ao utilizador a uma utilização de conjunto de dimensionamento de máquina virtual [remover a identidade do vmss az](/cli/azure/vmss/identity#az-vmss-identity-remove). Se este é o único atribuído ao utilizador atribuída para o conjunto de dimensionamento de máquina virtual, a identidade gerido `UserAssigned` será removida do valor de tipo de identidade.  Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VMSS NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` será a atribuído ao utilizador identidade gerida `name` propriedade, que pode ser encontrada na secção da identidade do conjunto com o dimensionamento da máquina virtual `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
```

Se o conjunto de dimensionamento de máquina virtual não tiver um sistema atribuído geridos identidade e pretender remover todas as identidades geridas atribuído ao utilizador do mesmo, utilize o seguinte comando:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Tem de estar em minúsculas.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se o conjunto de dimensionamento de máquinas virtuais tem ambos atribuído de sistema e identidades geridas atribuído ao utilizador, pode remover todas as identidiades atribuídas ao mudar para utilizar apenas atribuído de sistema de identidade gerida. Utilize o seguinte comando:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Passos Seguintes

- [Identidades geridas de descrição geral de recursos do Azure](overview.md)
- Para o Azure completo criação manual de início rápido do conjunto de dimensionamento de máquina virtual, consulte: 

  - [Criar um conjunto de dimensionamento de máquinas virtuais com a CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















