---
title: Configurar identidades gerenciadas no conjunto de dimensionamento de máquinas virtuais-CLI do Azure-Azure AD
description: Instruções passo a passo para a configuração de identidades gerenciadas atribuídas pelo usuário e pelo sistema em um conjunto de dimensionamento de máquinas virtuais do Azure, usando CLI do Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: MarkusVi
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
ms.openlocfilehash: 74d8faed0637b5b5b82e1ad450a3b1535bb063e4
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547300"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando CLI do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, você aprende a executar as seguintes identidades gerenciadas para operações de recursos do Azure em um conjunto de dimensionamento de máquinas virtuais do Azure, usando o CLI do Azure:
- Habilitar e desabilitar a identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais do Azure
- Adicionar e remover uma identidade gerenciada atribuída pelo usuário em um conjunto de dimensionamento de máquinas virtuais do Azure


## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção visão geral](overview.md). **Certifique-se de examinar a [diferença entre uma identidade gerenciada atribuída pelo sistema e](overview.md#how-does-the-managed-identities-for-azure-resources-work)** atribuída pelo usuário.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar as operações de gerenciamento neste artigo, sua conta precisa das seguintes atribuições de controle de acesso com base em função do Azure:

    > [!NOTE]
    > Nenhuma atribuição adicional de função de diretório do Azure AD é necessária.

    - [Colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e habilitar e remover o sistema e/ou a identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais.
    - Função [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade gerenciada atribuída pelo usuário.
    - Função de [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade gerenciada atribuída pelo usuário de e para um conjunto de dimensionamento de máquinas virtuais.
- Para executar os exemplos de script da CLI, tem três opções:
    - Use [Azure cloud Shell](../../cloud-shell/overview.md) da portal do Azure (consulte a próxima seção).
    - Utilize o embedded Azure Cloud Shell através do "Experimente-lo" botão do, localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente do CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se preferir usar um console local da CLI. 
      
      > [!NOTE]
      > Os comandos foram atualizados para refletir a versão mais recente do [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá a habilitar e desabilitar a identidade gerenciada atribuída pelo sistema para um conjunto de dimensionamento de máquinas virtuais do Azure usando o CLI do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Habilitar a identidade gerenciada atribuída pelo sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais do Azure

Para criar um conjunto de dimensionamento de máquinas virtuais com a identidade gerenciada atribuída pelo sistema habilitada:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar o conjunto de dimensionamento de máquinas virtuais:

   ```azurecli-interactive
   az login
   ```

2. Crie um [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para confinamento e implantação de seu conjunto de dimensionamento de máquinas virtuais e seus recursos relacionados, usando [AZ Group Create](/cli/azure/group/#az-group-create). Você pode ignorar esta etapa se já tiver um grupo de recursos que deseja usar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Crie um conjunto de dimensionamento de máquinas virtuais usando [AZ vmss Create](/cli/azure/vmss/#az-vmss-create) . O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais chamado *myVMSS* com uma identidade gerenciada atribuída pelo sistema, conforme solicitado pelo parâmetro `--assign-identity`. Os parâmetros `--admin-username` e `--admin-password` especificam o nome e a palavra-passe da conta de utilizador administrativo para início de sessão na máquina virtual. Atualize estes valores conforme adequado para o seu ambiente: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Habilitar identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais do Azure existente

Se você precisar habilitar a identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais do Azure existente:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

   ```azurecli-interactive
   az login
   ```

2. Use o comando [AZ vmss Identity Assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) para habilitar uma identidade gerenciada atribuída pelo sistema para uma VM existente:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desabilitar a identidade gerenciada atribuída pelo sistema de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais da identidade gerenciada atribuída pelo sistema, mas ainda precisar de identidades gerenciadas atribuídas pelo usuário, use o seguinte comando:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Se você tiver uma máquina virtual que não precisa mais de uma identidade gerenciada atribuída pelo sistema e não tiver identidades gerenciadas atribuídas pelo usuário, use o seguinte comando:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Ele deve estar em minúsculas. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá a habilitar e a remover uma identidade gerenciada atribuída pelo usuário usando o CLI do Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída pelo usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais

Esta seção orienta a criação de um conjunto de dimensionamento de máquinas virtuais e a atribuição de uma identidade gerenciada atribuída pelo usuário ao conjunto de dimensionamento de máquinas virtuais. Se você já tiver um conjunto de dimensionamento de máquinas virtuais que deseja usar, pule esta seção e vá para a próxima.

1. Você pode ignorar esta etapa se já tiver um grupo de recursos que deseja usar. Crie um [grupo de recursos](~/articles/azure-resource-manager/resource-group-overview.md#terminology) para contenção e implantação de sua identidade gerenciada atribuída pelo usuário, usando [AZ Group Create](/cli/azure/group/#az-group-create). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade gerida atribuída pelo utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade gerida atribuída pelo utilizador é criada e o parâmetro `-n` especifica o respetivo nome. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   A resposta contém detalhes para a identidade gerenciada atribuída pelo usuário criada, semelhante à seguinte. O valor `id` do recurso atribuído à identidade gerenciada atribuída pelo usuário é usado na etapa a seguir.

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

3. Crie um conjunto de dimensionamento de máquinas virtuais usando [AZ vmss Create](/cli/azure/vmss/#az-vmss-create). O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais associado à nova identidade gerenciada atribuída pelo usuário, conforme especificado pelo parâmetro `--assign-identity`. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY>` pelos seus próprios valores. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais existente

1. Crie uma identidade gerida atribuída pelo utilizador com [az identity create](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade gerida atribuída pelo utilizador é criada e o parâmetro `-n` especifica o respetivo nome. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A resposta contém detalhes para a identidade gerenciada atribuída pelo usuário criada, semelhante à seguinte.

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

2. Atribua a identidade gerenciada atribuída pelo usuário ao seu conjunto de dimensionamento de máquinas virtuais usando [AZ vmss Identity Assign](/cli/azure/vmss/identity). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` é a propriedade de `name` de recurso da identidade atribuída pelo usuário, conforme criado na etapa anterior:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remover uma identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais do Azure

Para remover uma identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais, use [AZ vmss Identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove). Se essa for a única identidade gerenciada atribuída pelo usuário atribuída ao conjunto de dimensionamento de máquinas virtuais, `UserAssigned` será removido do valor do tipo de identidade.  Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` será a propriedade `name` da identidade gerenciada atribuída pelo usuário, que pode ser encontrada na seção identidade do conjunto de dimensionamento de máquinas virtuais usando `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Se o conjunto de dimensionamento de máquinas virtuais não tiver uma identidade gerenciada atribuída pelo sistema e você quiser remover todas as identidades gerenciadas atribuídas pelo usuário dela, use o seguinte comando:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Ele deve estar em minúsculas.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se o conjunto de dimensionamento de máquinas virtuais tiver identidades gerenciadas atribuídas pelo sistema e com o usuário, você poderá remover todas as identidades atribuídas pelo usuário alternando para usar somente a identidade gerenciada atribuída pelo sistema. Utilize o seguinte comando:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Passos seguintes

- [Visão geral de identidades gerenciadas para recursos do Azure](overview.md)
- Para obter o início rápido da criação do conjunto de dimensionamento de máquinas virtuais do Azure, consulte: 

  - [Criar um conjunto de dimensionamento de máquinas virtuais com a CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















