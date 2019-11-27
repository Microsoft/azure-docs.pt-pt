---
title: Configurar identidades gerenciadas na VM do Azure usando REST-Azure AD
description: Instruções passo a passo para configurar um sistema e identidades gerenciadas atribuídas pelo usuário em uma VM do Azure usando a rotação para fazer chamadas à API REST.
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
ms.date: 06/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d78ddaaae886a33b4d22e8724ade04ab63508f1
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547329"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando chamadas à API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades gerenciadas para recursos do Azure fornece aos serviços do Azure uma identidade de sistema gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, usando a rotação para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager, você aprende a executar as seguintes identidades gerenciadas para operações de recursos do Azure em uma VM do Azure:

- Habilitar e desabilitar a identidade gerenciada atribuída pelo sistema em uma VM do Azure
- Adicionar e remover uma identidade gerenciada atribuída pelo usuário em uma VM do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção visão geral](overview.md). **Certifique-se de examinar a [diferença entre uma identidade gerenciada atribuída pelo sistema e](overview.md#how-does-the-managed-identities-for-azure-resources-work)** atribuída pelo usuário.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Se você estiver usando o Windows, instale o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou use o [Azure cloud Shell](../../cloud-shell/overview.md) no portal do Azure.
- [Instale o console local do CLI do Azure](/cli/azure/install-azure-cli), se você usar o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou um [so de distribuição Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Se você estiver usando CLI do Azure console local, entre no Azure usando `az login` com uma conta que esteja associada à assinatura do Azure que você gostaria de gerenciar as identidades gerenciadas atribuídas pelo usuário ou pelo sistema.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá a habilitar e desabilitar a identidade gerenciada atribuída pelo sistema em uma VM do Azure usando a rotação para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Habilitar a identidade gerenciada atribuída pelo sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade gerenciada atribuída pelo sistema habilitada, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Crie uma [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para contenção e implementação da VM e os respetivos recursos relacionados, utilizando [az group create](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para sua VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma VM usando a rotação para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo a seguir cria uma VM chamada *myVM* com uma identidade gerenciada atribuída pelo sistema, conforme identificado no corpo da solicitação pelo valor `"identity":{"type":"SystemAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor que você recebeu na etapa anterior quando solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Cabeçalhos de solicitação**
   
   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        | 
   
   **Corpo da solicitação**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Habilitar a identidade atribuída pelo sistema em uma VM do Azure existente

Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM que foi originalmente provisionada sem ela, sua conta precisa da atribuição de função de [colaborador de máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Use o comando de rotação a seguir para chamar o ponto de extremidade REST do Azure Resource Manager para habilitar a identidade gerenciada atribuída pelo sistema em sua VM, conforme identificado no corpo da solicitação pelo valor `{"identity":{"type":"SystemAssigned"}` para uma VM chamada *myVM*.  Substitua `<ACCESS TOKEN>` pelo valor que você recebeu na etapa anterior quando solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.
   
   > [!IMPORTANT]
   > Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário existente atribuída à VM, é necessário listar as identidades gerenciadas atribuídas pelo usuário usando esse comando de rotação: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se você tiver qualquer identidade gerenciada atribuída pelo usuário atribuída à VM, conforme identificado no valor `identity` na resposta, pule para a etapa 3 que mostra como manter as identidades gerenciadas atribuídas pelo usuário ao habilitar a identidade gerenciada atribuída pelo sistema em sua VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        | 
   
   **Corpo da solicitação**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM com identidades gerenciadas atribuídas pelo usuário existentes, você precisará adicionar `SystemAssigned` ao valor de `type`.  
   
   Por exemplo, se sua VM tiver as identidades gerenciadas atribuídas pelo usuário `ID1` e `ID2` atribuídas a ela, e você quiser adicionar a identidade gerenciada atribuída pelo sistema à VM, use a seguinte chamada de rotação. Substitua `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` com os valores apropriados para o seu ambiente.

   A versão de API `2018-06-01` armazena identidades gerenciadas atribuídas pelo usuário no valor de `userAssignedIdentities` em um formato de dicionário, ao contrário do valor `identityIds` em um formato de matriz usado em `2017-12-01`de versão de API.
   
   **VERSÃO DA API 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        | 

   **Corpo da solicitação**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **VERSÃO DA API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        | 

   **Corpo da solicitação**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Desabilitar a identidade gerenciada atribuída pelo sistema de uma VM do Azure

Para desabilitar a identidade gerenciada atribuída pelo sistema em uma VM, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Atualize a VM usando a rotação para chamar o ponto de extremidade REST do Azure Resource Manager para desabilitar a identidade gerenciada atribuída pelo sistema.  O exemplo a seguir desabilita a identidade gerenciada atribuída pelo sistema conforme identificada no corpo da solicitação pelo valor `{"identity":{"type":"None"}}` de uma VM denominada *myVM*.  Substitua `<ACCESS TOKEN>` pelo valor que você recebeu na etapa anterior quando solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.

   > [!IMPORTANT]
   > Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário existente atribuída à VM, é necessário listar as identidades gerenciadas atribuídas pelo usuário usando esse comando de rotação: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se você tiver qualquer identidade gerenciada atribuída pelo usuário atribuída à VM, conforme identificado no valor `identity` na resposta, pule para a etapa 3 que mostra como manter as identidades gerenciadas atribuídas pelo usuário ao desabilitar a identidade gerenciada atribuída pelo sistema em sua VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        | 

   **Corpo da solicitação**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Para remover a identidade gerenciada atribuída pelo sistema de uma máquina virtual que tenha identidades gerenciadas atribuídas pelo usuário, remova `SystemAssigned` do valor de `{"identity":{"type:" "}}` ao manter o valor de `UserAssigned` e os valores de dicionário de `userAssignedIdentities` se você estiver usando a **versão de API 2018-06-01**. Se você estiver usando a **versão de API 2017-12-01** ou anterior, mantenha a matriz de `identityIds`.

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá a adicionar e remover a identidade gerenciada atribuída pelo usuário em uma VM do Azure usando a rotação para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade gerenciada atribuída pelo usuário durante a criação de uma VM do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para sua VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma identidade gerenciada atribuída pelo usuário usando as instruções encontradas aqui: [criar uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Crie uma VM usando a rotação para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo a seguir cria uma VM chamada *myVM* no grupo de recursos *MyResource* Group com uma identidade gerenciada atribuída pelo usuário `ID1`, conforme identificado no corpo da solicitação pelo valor `"identity":{"type":"UserAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor que você recebeu na etapa anterior quando solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.
 
   **VERSÃO DA API 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        | 

   **Corpo da solicitação**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **VERSÃO DA API 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        | 

   **Corpo da solicitação**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerenciada atribuída pelo usuário a uma VM do Azure existente

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Crie uma identidade gerenciada atribuída pelo usuário usando as instruções encontradas aqui, [crie uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Para garantir que você não exclua as identidades gerenciadas atribuídas pelo usuário ou pelo sistema que são atribuídas à VM, é necessário listar os tipos de identidade atribuídos à VM usando o comando de rotação a seguir. Se você tiver identidades gerenciadas atribuídas ao conjunto de dimensionamento de máquinas virtuais, elas serão listadas em no valor `identity`.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.

    Se você tiver qualquer usuário ou identidades gerenciadas atribuídas pelo sistema atribuídas à VM, conforme identificado no valor de `identity` na resposta, pule para a etapa 5 que mostra como manter a identidade gerenciada atribuída pelo sistema ao adicionar uma identidade gerenciada atribuída pelo usuário em sua VM.

4. Se você não tiver nenhuma identidade gerenciada atribuída pelo usuário atribuída à sua VM, use o comando de rotação a seguir para chamar o ponto de extremidade de Azure Resource Manager REST para atribuir a primeira identidade gerenciada atribuída pelo usuário à VM.

   Os exemplos a seguir atribui uma identidade gerenciada atribuída pelo usuário, `ID1` a uma VM chamada *myVM* no grupo de recursos *MyResource*Group.  Substitua `<ACCESS TOKEN>` pelo valor que você recebeu na etapa anterior quando solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.

   **VERSÃO DA API 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        |
 
   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **VERSÃO DA API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. Se você tiver uma identidade gerenciada atribuída pelo usuário ou atribuído pelo sistema existente atribuída à sua VM:
   
   **VERSÃO DA API 2018-06-01**

   Adicione a identidade gerenciada atribuída pelo usuário ao valor de dicionário de `userAssignedIdentities`.
    
   Por exemplo, se você tiver uma identidade gerenciada atribuída pelo sistema e a identidade gerenciada atribuída pelo usuário `ID1` atribuída atualmente à sua VM e quiser adicionar a identidade gerenciada atribuída pelo usuário `ID2` a ela:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **VERSÃO DA API 2017-12-01**

   Mantenha as identidades gerenciadas atribuídas pelo usuário que você deseja manter no valor da matriz de `identityIds` ao adicionar a nova identidade gerenciada atribuída pelo usuário.

   Por exemplo, se você tiver uma identidade gerenciada atribuída pelo sistema e a identidade gerenciada atribuída pelo usuário `ID1` atribuída atualmente à sua VM e quiser adicionar a identidade gerenciada atribuída pelo usuário `ID2` a ela: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerenciada atribuída pelo usuário de uma VM do Azure

Para remover uma identidade atribuída pelo usuário a uma VM, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar sua VM com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário existente que deseja manter atribuída à VM ou remova a identidade gerenciada atribuída pelo sistema, é necessário listar as identidades gerenciadas usando o seguinte comando de ONDULAção: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.
 
   Se você tiver identidades gerenciadas atribuídas à VM, elas serão listadas na resposta no valor `identity`.

   Por exemplo, se você tiver identidades gerenciadas atribuídas pelo usuário `ID1` e `ID2` atribuídas à sua VM e quiser manter `ID1` atribuídas e manter a identidade atribuída pelo sistema:
   
   **VERSÃO DA API 2018-06-01**

   Adicione `null` à identidade gerenciada atribuída pelo usuário que você deseja remover:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **VERSÃO DA API 2017-12-01**

   Reter somente as identidades gerenciadas atribuídas pelo usuário que você deseja manter na matriz de `identityIds`:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Se sua VM tiver identidades gerenciadas atribuídas pelo sistema e com o usuário, você poderá remover todas as identidades gerenciadas atribuídas pelo usuário alternando para usar somente a identidade gerenciada atribuída pelo sistema usando o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Cabeçalhos de solicitação**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

**Corpo da solicitação**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Se sua VM tiver apenas identidades gerenciadas atribuídas pelo usuário e você quiser removê-las, use o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Cabeçalhos de solicitação**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.| 

**Corpo da solicitação**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como criar, listar ou excluir identidades gerenciadas atribuídas pelo usuário usando o REST, consulte:

- [Criar, listar ou excluir uma identidade gerenciada atribuída pelo usuário usando chamadas à API REST](how-to-manage-ua-identity-rest.md)
