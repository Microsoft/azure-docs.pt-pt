---
title: Configure identidades geridas em Azure VM usando REST - Azure AD
description: Instruções passo a passo para configurar um sistema e identidades geridas atribuídas pelo utilizador num VM Azure usando o CURL para fazer chamadas DESE API.
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: f70229fabe4331adb7740cf09cf2bf8f3e3e4617
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587524"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Configure identidades geridas para recursos Azure em um VM Azure usando chamadas REST API

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade de sistema gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código. 

Neste artigo, utilizando o CURL para fazer chamadas para o ponto final do Azure Resource Manager REST, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num VM Azure:

- Ativar e desativar a identidade gerida atribuída pelo sistema num VM Azure
- Adicione e remova uma identidade gerida atribuída pelo utilizador num VM Azure

Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com identidades geridas para recursos Azure, veja [o que são identidades geridas para os recursos do Azure?](overview.md) Para saber mais sobre os tipos de identidade geridos atribuídos pelo sistema e atribuídos pelo utilizador, consulte [os tipos de identidade geridos](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprende-se a ativar e desativar a identidade gerida atribuída pelo sistema num VM Azure utilizando o CURL para fazer chamadas para o ponto final do Azure Resource Manager REST.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Permitir a identidade gerida atribuída pelo sistema durante a criação de um VM Azure

Para criar um VM Azure com a identidade gerida atribuída pelo sistema, a sua conta precisa da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Crie uma [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) para contenção e implementação da VM e os respetivos recursos relacionados, utilizando [az group create](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic#az-network-nic-create) para o seu VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso ao Portador, que utilizará no passo seguinte no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Utilizando a Azure Cloud Shell, crie um VM utilizando o CURL para chamar o ponto final do Azure Resource Manager REST. O exemplo a seguir cria um VM chamado *myVM* com uma identidade gerida atribuída ao sistema, conforme identificado no organismo de pedido pelo valor `"identity":{"type":"SystemAssigned"}` . `<ACCESS TOKEN>`Substitua-o pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Bearer e o `<SUBSCRIPTION ID>` valor adequado para o seu ambiente.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Cabeçalhos do pedido**
   
   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        | 
   
   **Corpo de pedido**

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

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Ativar a identidade atribuída ao sistema num VM Azure existente

Para permitir a identidade gerida atribuída pelo sistema num VM que foi originalmente a provisionado sem ele, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Recupere um token de acesso ao Portador, que utilizará no passo seguinte no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utilize o seguinte comando CURL para ligar para o ponto final do Azure Resource Manager REST para permitir a identidade gerida atribuída pelo sistema no seu VM, tal como identificado no organismo de pedido pelo valor `{"identity":{"type":"SystemAssigned"}` de um VM nomeado *myVM*.  `<ACCESS TOKEN>`Substitua-o pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Bearer e o `<SUBSCRIPTION ID>` valor adequado para o seu ambiente.
   
   > [!IMPORTANT]
   > Para garantir que não elimina quaisquer identidades geridas atribuídas ao utilizador que sejam atribuídas ao VM, é necessário listar as identidades geridas atribuídas pelo utilizador utilizando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Se tiver alguma identidade gerida atribuída ao utilizador atribuída ao VM como identificada no `identity` valor da resposta, salte para o passo 3 que lhe mostre como reter identidades geridas atribuídas pelo utilizador, permitindo a identidade gerida atribuída pelo sistema no seu VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        | 
   
   **Corpo de pedido**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Para ativar a identidade gerida atribuída pelo sistema num VM com identidades geridas atribuídas pelo utilizador existentes, é necessário adicionar `SystemAssigned` ao `type` valor.  
   
   Por exemplo, se o seu VM tiver as identidades geridas atribuídas pelo utilizador `ID1` e `ID2` lhe for atribuído, e pretender adicionar à VM a identidade gerida atribuída pelo sistema, utilize a seguinte chamada CURL. Substitua `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` por valores adequados ao seu ambiente.

   A versão API `2018-06-01` armazena identidades geridas atribuídas pelo utilizador `userAssignedIdentities` no valor num formato dicionário em oposição ao `identityIds` valor num formato de matriz utilizado na versão API `2017-12-01` .
   
   **VERSÃO API 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        | 

   **Corpo de pedido**

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

   **VERSÃO API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        | 

   **Corpo de pedido**

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

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Desativar a identidade gerida atribuída pelo sistema a partir de um VM Azure

Para desativar a identidade gerida atribuída pelo sistema num VM, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Recupere um token de acesso ao Portador, que utilizará no passo seguinte no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Atualize o VM utilizando o CURL para ligar para o ponto final do Azure Resource Manager REST para desativar a identidade gerida atribuída pelo sistema.  O exemplo seguinte desativa a identidade gerida atribuída pelo sistema, identificada no organismo de pedido pelo valor `{"identity":{"type":"None"}}` de um VM chamado *myVM*.  `<ACCESS TOKEN>`Substitua-o pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Bearer e o `<SUBSCRIPTION ID>` valor adequado para o seu ambiente.

   > [!IMPORTANT]
   > Para garantir que não elimina quaisquer identidades geridas atribuídas ao utilizador que sejam atribuídas ao VM, é necessário listar as identidades geridas atribuídas pelo utilizador utilizando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Se tiver alguma identidade gerida atribuída ao utilizador atribuída ao VM como identificada no `identity` valor da resposta, salte para o passo 3 que lhe mostre como reter identidades geridas atribuídas pelo utilizador, ao mesmo tempo que desativa a identidade gerida atribuída pelo sistema no seu VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        | 

   **Corpo de pedido**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Para remover a identidade gerida atribuída ao sistema a partir de uma máquina virtual que tenha identidades geridas atribuídas ao utilizador, remova `SystemAssigned` do `{"identity":{"type:" "}}` valor mantendo o valor e os `UserAssigned` valores do dicionário se estiver a `userAssignedIdentities` utilizar a versão **API 2018-06-01**. Se estiver a utilizar **a versão API 2017-12-01** ou mais cedo, mantenha a `identityIds` matriz.

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, aprende-se a adicionar e remover a identidade gerida atribuída pelo utilizador num VM Azure utilizando o CURL para fazer chamadas para o ponto final do Azure Resource Manager REST.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador durante a criação de um VM Azure

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções [de Colaborador de Máquinas Virtuais](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e Operador de Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-operator) Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Recupere um token de acesso ao Portador, que utilizará no passo seguinte no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic#az-network-nic-create) para o seu VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso ao Portador, que utilizará no passo seguinte no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Criar uma identidade gerida atribuída pelo utilizador utilizando as instruções aqui encontradas: [Criar uma identidade gerida atribuída pelo utilizador](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Crie um VM utilizando o CURL para chamar o ponto final do Azure Resource Manager REST. O exemplo a seguir cria um VM nomeado *myVM* no grupo de recursos *myResourceGroup* com uma identidade gerida atribuída pelo `ID1` utilizador, tal como identificado no organismo de pedido pelo valor `"identity":{"type":"UserAssigned"}` . `<ACCESS TOKEN>`Substitua-o pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Bearer e o `<SUBSCRIPTION ID>` valor adequado para o seu ambiente.
 
   **VERSÃO API 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        | 

   **Corpo de pedido**

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
  
   **VERSÃO API 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        | 

   **Corpo de pedido**

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador a um Azure VM existente

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções [de Colaborador de Máquinas Virtuais](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e Operador de Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-operator) Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Recupere um token de acesso ao Portador, que utilizará no passo seguinte no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Criar uma identidade gerida atribuída pelo utilizador utilizando as instruções encontradas aqui, [Crie uma identidade gerida atribuída pelo utilizador](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Para garantir que não elimina as identidades geridas pelo utilizador ou pelo sistema que são atribuídas ao VM, é necessário listar os tipos de identidade atribuídos ao VM utilizando o seguinte comando CURL. Se tiver gerido identidades atribuídas ao conjunto de escala de máquina virtual, elas estão listadas no `identity` valor.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.

    Se tiver alguma identidade gerida atribuída ao utilizador ou sistema atribuído ao VM como identificado no `identity` valor da resposta, salte para o passo 5 que lhe mostre como reter a identidade gerida atribuída ao sistema, adicionando uma identidade gerida atribuída ao utilizador no seu VM.

4. Se não tiver identidades geridas atribuídas ao utilizador atribuídas ao seu VM, utilize o seguinte comando CURL para ligar para o ponto final do Azure Resource Manager REST para atribuir a primeira identidade gerida atribuída ao VM.

   O exemplo seguinte atribui uma identidade gerida atribuída ao utilizador `ID1` a um VM nomeado *myVM* no grupo de recursos *myResourceGroup*.  `<ACCESS TOKEN>`Substitua-o pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Bearer e o `<SUBSCRIPTION ID>` valor adequado para o seu ambiente.

   **VERSÃO API 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        |
 
   **Corpo de pedido**

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

   **VERSÃO API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        | 

   **Corpo de pedido**

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

5. Se tiver uma identidade gerida atribuída ao utilizador ou ao sistema atribuído ao seu VM:
   
   **VERSÃO API 2018-06-01**

   Adicione a identidade gerida atribuída ao valor do `userAssignedIdentities` dicionário.
    
   Por exemplo, se tiver a identidade gerida atribuída ao sistema e a identidade gerida atribuída ao utilizador `ID1` atualmente atribuída ao seu VM e gostaria de lhe adicionar a identidade gerida atribuída `ID2` pelo utilizador:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        | 

   **Corpo de pedido**

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

   **VERSÃO API 2017-12-01**

   Mantenha as identidades geridas atribuídas pelo utilizador que pretende manter no valor da `identityIds` matriz, adicionando a nova identidade gerida atribuída pelo utilizador.

   Por exemplo, se tiver a identidade gerida atribuída ao sistema e a identidade gerida atribuída ao utilizador `ID1` atualmente atribuída ao seu VM e gostaria de lhe adicionar a identidade gerida atribuída `ID2` pelo utilizador: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        | 

   **Corpo de pedido**

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerida atribuída pelo utilizador de um VM Azure

Para remover uma identidade atribuída ao utilizador para um VM, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

1. Recupere um token de acesso ao Portador, que utilizará no passo seguinte no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Para garantir que não elimina quaisquer identidades geridas atribuídas pelo utilizador que gostaria de manter atribuídas ao VM ou remover a identidade gerida atribuída pelo sistema, tem de listar as identidades geridas utilizando o seguinte comando CURL: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.
 
   Se tiver gerido as identidades atribuídas ao VM, estão listadas na resposta no `identity` valor.

   Por exemplo, se tiver identidades geridas atribuídas pelo utilizador `ID1` e `ID2` atribuídas ao seu VM, e apenas quiser manter a identidade atribuída ao `ID1` sistema:
   
   **VERSÃO API 2018-06-01**

   Adicione `null` à identidade gerida atribuída ao utilizador que gostaria de remover:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        | 

   **Corpo de pedido**

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

   **VERSÃO API 2017-12-01**

   Reter apenas as identidades geridas atribuídas ao utilizador que pretende manter na `identityIds` matriz:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        | 

   **Corpo de pedido**

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

Se o seu VM tiver identidades geridas atribuídas ao sistema e atribuídas ao utilizador, pode remover todas as identidades geridas atribuídas pelo utilizador, mudando para utilizar apenas a identidade gerida atribuída pelo sistema utilizando o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Cabeçalhos do pedido**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido. | 

**Corpo de pedido**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Se o seu VM tiver apenas identidades geridas atribuídas ao utilizador e quiser removê-las todas, utilize o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Cabeçalhos do pedido**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.| 

**Corpo de pedido**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como criar, listar ou eliminar identidades geridas atribuídas pelo utilizador utilizando o REST consulte:

- [Criar, listar ou eliminar identidades geridas atribuídas pelo utilizador usando chamadas REST API](how-to-manage-ua-identity-rest.md)
