---
title: Como configurar o sistema e atribuído ao utilizador identidades geridas na VM do Azure com REST
description: Instruções passo a passo chama instruções para configurar um sistema e de identidades geridas atribuído ao utilizador na VM do Azure com o CURL para tornar a API de REST.
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
ms.date: 06/25/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09bdffbceafc11d99889cbda1461e4af4d89168e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444621"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Configurar identidades gerida para recursos do Azure numa VM do Azure através de chamadas à REST API

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade de sistema automaticamente gerido no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, com o CURL para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager, aprenderá a efetuar as seguintes identidades geridas para operações de recursos do Azure numa VM do Azure:

- Ativar e desativar a identidade gerida atribuído de sistema numa VM do Azure
- Adicionar e remover uma identidade gerida atribuído ao utilizador numa VM do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Se estiver a utilizar o Windows, instale o [subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou utilizar o [Azure Cloud Shell](../../cloud-shell/overview.md) no portal do Azure.
- [Instalar a consola local da CLI do Azure](/cli/azure/install-azure-cli), se usar o [subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou uma [SO de distribuição de Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Se estiver a utilizar consola local da CLI do Azure, inicie sessão no Azure com `az login` com uma conta que está associada à subscrição do Azure que pretende gerir o sistema ou de identidades geridas atribuído ao utilizador.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Atribuído ao sistema de identidade gerida

Nesta secção, saiba como ativar e desativar a identidade gerida atribuído de sistema numa VM do Azure com o CURL para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Ativar a identidade gerida atribuído ao sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade gerida atribuídos do sistema ativada, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função.  Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Crie uma [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para contenção e implementação da VM e os respetivos recursos relacionados, utilizando [az group create](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Criar uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para a sua VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar a sua VM com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma VM com o CURL para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo seguinte cria uma VM com o nome *myVM* com um atribuído de sistema de identidade gerida, conforme identificado no corpo do pedido pelo valor `"identity":{"type":"SystemAssigned"}`. Substitua `<ACCESS TOKEN>` com o valor que recebeu no passo anterior quando tiver solicitado um token de portador de acesso e o `<SUBSCRIPTION ID>` valor conforme apropriado para o seu ambiente.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Cabeçalhos de pedido**
   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        | 
   
   **Corpo do pedido**

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

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Ativar a identidade atribuída de sistema numa VM do Azure existente

Para ativar a identidade gerida atribuído de sistema numa VM que foi originalmente aprovisionada sem ele, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função.  Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar a sua VM com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utilize o seguinte comando CURL para chamar o ponto de extremidade REST do Azure Resource Manager para ativar a identidade gerida atribuído de sistema na sua VM conforme identificado no corpo do pedido pelo valor `{"identity":{"type":"SystemAssigned"}` para uma VM com o nome *myVM*.  Substitua `<ACCESS TOKEN>` com o valor que recebeu no passo anterior quando tiver solicitado um token de portador de acesso e o `<SUBSCRIPTION ID>` valor conforme apropriado para o seu ambiente.
   
   > [!IMPORTANT]
   > Para garantir não elimina quaisquer existentes atribuído ao utilizador identidades geridas que são atribuídas à VM, tem de listar as identidades geridas atribuído ao utilizador ao utilizar este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se tiver algum atribuído ao utilizador gerido identidades atribuídas à VM, conforme indicado no `identity` valor na resposta, avance para o passo 3, que mostra como reter atribuído ao utilizador identidades geridas ao ativar a identidade gerida atribuído de sistema na sua VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos de pedido**
   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        | 
   
   **Corpo do pedido**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Para ativar atribuído de sistema a identidade gerida uma VM com existentes atribuído ao utilizador identidades geridas, tem de adicionar `SystemAssigned` para o `type` valor.  
   
   Por exemplo, se a VM tem de identidades geridas atribuídas `ID1` e `ID2` atribuídos à mesma, e gostaria de adicionar atribuído de sistema de identidade gerida para a VM, utilize a seguinte chamada CURL. Substitua `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` com valores adequados ao seu ambiente.

   Versão de API `2018-06-01` armazena identidades geridas atribuído ao utilizador no `userAssignedIdentities` valor num formato de dicionário, em vez do `identityIds` valor num formato de matriz, utilizado a versão de API `2017-12-01`.
   
   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        | 

   **Corpo do pedido**

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

   **API VERSÃO 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        | 
   **Corpo do pedido**

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

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Desativar atribuído de sistema de identidade gerida de uma VM do Azure

Para desativar a identidade gerida atribuído de sistema numa VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função.  Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar a sua VM com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Atualize a VM com o CURL para chamar o ponto de extremidade REST do Azure Resource Manager para desativar a identidade gerida atribuído de sistema.  O exemplo seguinte desativa atribuído de sistema de identidade gerida, conforme identificado no corpo do pedido pelo valor `{"identity":{"type":"None"}}` de uma VM chamada *myVM*.  Substitua `<ACCESS TOKEN>` com o valor que recebeu no passo anterior quando tiver solicitado um token de portador de acesso e o `<SUBSCRIPTION ID>` valor conforme apropriado para o seu ambiente.

   > [!IMPORTANT]
   > Para garantir não elimina quaisquer existentes atribuído ao utilizador identidades geridas que são atribuídas à VM, tem de listar as identidades geridas atribuído ao utilizador ao utilizar este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se tiver algum atribuído ao utilizador gerido identidades atribuídas à VM, conforme indicado no `identity` valor na resposta, avance para o passo 3, que mostra como reter atribuído ao utilizador identidades geridas ao desativar a identidade gerida atribuído de sistema na sua VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        | 

   **Corpo do pedido**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Para remover o sistema atribuído a identidade gerida de uma máquina virtual que tenha atribuído ao utilizador identidades geridas, remova `SystemAssigned` partir a `{"identity":{"type:" "}}` valor ao manter o `UserAssigned` valor e o `userAssignedIdentities` dicionário de valores se estiver a utilizar **2018-01 06 de versão de API**. Se estiver a utilizar **API versão 2017-12-01** ou anterior, mantenha o `identityIds` matriz.

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, saiba como adicionar e remover a identidade gerida atribuído ao utilizador na VM do Azure com o CURL para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade gerida atribuído ao utilizador durante a criação de uma VM do Azure

Para atribuir uma identidade de utilizador atribuído a uma VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) atribuições de funções. Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar a sua VM com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Criar uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para a sua VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar a sua VM com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma identidade gerida atribuído ao utilizador com as instruções aqui: [Criar uma identidade gerida atribuído ao utilizador](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Crie uma VM com o CURL para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo seguinte cria uma VM com o nome *myVM* no grupo de recursos *myResourceGroup* com uma identidade gerida atribuído ao utilizador `ID1`, conforme identificado no corpo do pedido pelo valor `"identity":{"type":"UserAssigned"}`. Substitua `<ACCESS TOKEN>` com o valor que recebeu no passo anterior quando tiver solicitado um token de portador de acesso e o `<SUBSCRIPTION ID>` valor conforme apropriado para o seu ambiente.
 
   **API VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        | 

   **Corpo do pedido**

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
  
   **API VERSÃO 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        | 

   **Corpo do pedido**

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerida atribuído ao utilizador a uma VM do Azure existente

Para atribuir uma identidade de utilizador atribuído a uma VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) atribuições de funções. Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar a sua VM com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Criar uma identidade gerida atribuído ao utilizador com as instruções aqui, [criar uma identidade gerida atribuído ao utilizador](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Para garantir que não a eliminar o utilizador existente ou de identidades geridas atribuído de sistema que estão atribuídas à VM, precisa de listar os tipos de identidade atribuídos à VM utilizando o comando CURL seguinte. Se o ter gerido identidades atribuídas ao conjunto de dimensionamento de máquina virtual, eles estão listados no `identity` valor.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos de pedido**
   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.

    Se tiver qualquer utilizador ou atribuído de sistema de identidades geridas atribuídas à VM, conforme indicado no `identity` valor na resposta, avance para o passo 5, que mostra como reter a identidade gerida atribuído de sistema ao adicionar uma identidade gerida atribuído ao utilizador no a VM.

4. Se não tiver quaisquer identidades geridas atribuído ao utilizador atribuídas à sua VM, utilize o seguinte comando CURL para chamar o ponto de extremidade REST do Azure Resource Manager para atribuir a primeira identidade gerida atribuído ao utilizador para a VM.

   Os exemplos seguintes atribui uma identidade gerida atribuído ao utilizador, `ID1` a uma VM com o nome *myVM* no grupo de recursos *myResourceGroup*.  Substitua `<ACCESS TOKEN>` com o valor que recebeu no passo anterior quando tiver solicitado um token de portador de acesso e o `<SUBSCRIPTION ID>` valor conforme apropriado para o seu ambiente.

   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        |
 
   **Corpo do pedido**

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

   **API VERSÃO 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        | 

   **Corpo do pedido**

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

5. Se tiver uma atribuído ao utilizador ou atribuída de sistema de identidade gerida existente atribuída à sua VM:
   
   **API VERSION 2018-06-01**

   Adicionar a identidade gerida atribuído ao utilizador para o `userAssignedIdentities` valor de dicionário.
    
   Por exemplo, se tiver atribuído o sistema de identidade gerida e a identidade gerida atribuídas `ID1` atualmente atribuído à VM e gostaria de adicionar a identidade gerida atribuído ao utilizador `ID2` a ele:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        | 

   **Corpo do pedido**

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

   **API VERSÃO 2017-12-01**

   Manter as identidades geridas atribuído ao utilizador que pretende manter `identityIds` matriz valor ao adicionar a nova identidade gerida atribuído ao utilizador.

   Por exemplo, se tiver atribuído o sistema de identidade gerida e a identidade gerida atribuídas `ID1` atualmente atribuído à VM e gostaria de adicionar a identidade gerida atribuído ao utilizador `ID2` a ele: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        | 

   **Corpo do pedido**

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerida atribuído ao utilizador a partir de uma VM do Azure

Para remover uma identidade de utilizador atribuído a uma VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função.

1. Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar a sua VM com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Para garantir que não elimina quaisquer existentes geridos identidiades atribuídas que gostaria de manter atribuído à VM ou remova a identidade gerida atribuído de sistema, terá de lista de identidades geridas utilizando o comando CURL seguinte: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de pedido**
   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.
 
   Se o ter gerido identidades atribuídas à VM, estão listados na resposta no `identity` valor.

   Por exemplo, se tiver atribuído ao utilizador identidades geridas `ID1` e `ID2` atribuído à VM e quiser manter `ID1` atribuído e reter a identidade atribuída de sistema:
   
   **API VERSION 2018-06-01**

   Adicionar `null` para o atribuído ao utilizador que pretende remover a identidade de gerido:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        | 

   **Corpo do pedido**

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

   **API VERSÃO 2017-12-01**

   Manter apenas os atribuído ao utilizador geridas identity(s) gostaria de manter no `identityIds` matriz:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        | 

   **Corpo do pedido**

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

Se a VM tem ambos atribuído de sistema e identidades geridas atribuído ao utilizador, pode remover todos os geridos identidiades atribuídas ao mudar para utilizar só identidade em gerida atribuído de sistema com o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Cabeçalhos de pedido**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Content-Type*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso. | 

**Corpo do pedido**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Se a VM tem apenas atribuído ao utilizador identidades geridas e pretende removê-los todos os, utilize o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Cabeçalhos de pedido**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Content-Type*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.| 

**Corpo do pedido**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre como criar, listar ou eliminar identidades geridas atribuído ao utilizador com o REST, consulte:

- [Criar, lista ou eliminar um identidades geridas atribuído ao utilizador através de chamadas à REST API](how-to-manage-ua-identity-rest.md)
