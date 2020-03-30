---
title: Configure identidades geridas em Azure VM usando REST - Azure AD
description: Instruções passo a passo para configurar um sistema e identidades geridas atribuídas pelo utilizador num VM Azure utilizando o CURL para fazer chamadas REST API.
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
ms.openlocfilehash: 9f975595e935a5c0254450168aa295e6e7366a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244163"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Configure identidades geridas para recursos Azure em um VM Azure usando chamadas REST API

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade de sistema gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código. 

Neste artigo, utilizando o CURL para fazer chamadas para o ponto final do Gestor de Recursos Do Azure, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num Azure VM:

- Ativar e desativar a identidade gerida atribuída pelo sistema num VM Azure
- Adicione e remova uma identidade gerida atribuída pelo utilizador num VM Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Se estiver a utilizar o Windows, instale o [Subsistema Windows para o Linux](https://msdn.microsoft.com/commandline/wsl/about) ou utilize a [Casca de Nuvem Azure](../../cloud-shell/overview.md) no portal Azure.
- [Instale a consola local Azure CLI,](/cli/azure/install-azure-cli)se utilizar o [Subsistema Windows para O Linux](https://msdn.microsoft.com/commandline/wsl/about) ou um [SISTEMA de distribuição Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Se estiver a utilizar a consola local Azure `az login` CLI, inscreva-se no Azure utilizando uma conta associada à subscrição Azure que deseja gerir o sistema ou as identidades geridas atribuídas pelo utilizador.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprende-se a ativar e desativar a identidade gerida atribuída pelo sistema num Azure VM utilizando o CURL para fazer chamadas para o ponto final do Gestor de Recursos Azure REST.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Ativar identidade gerida atribuída pelo sistema durante a criação de um VM Azure

Para criar um VM Azure com a identidade gerida atribuída pelo sistema, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

1. Crie uma [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) para contenção e implementação da VM e os respetivos recursos relacionados, utilizando [az group create](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para o seu VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso ao Portador, que utilizará no próximo passo no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie um VM utilizando o CURL para ligar para o ponto final do Gestor de Recursos Azure. O exemplo seguinte cria um VM denominado *myVM* com uma identidade gerida atribuída pelo `"identity":{"type":"SystemAssigned"}`sistema, identificada no organismo de pedido pelo valor . Substitua-o `<ACCESS TOKEN>` pelo valor que recebeu no passo anterior quando `<SUBSCRIPTION ID>` solicitou um token de acesso ao Portador e o valor adequado para o seu ambiente.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Pedir cabeçalhos**
   
   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        | 
   
   **Solicitar corpo**

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

Para permitir a identidade gerida atribuída pelo sistema num VM originalmente provisionado sem ele, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

1. Recupere um token de acesso ao Portador, que utilizará no próximo passo no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utilize o seguinte comando CURL para ligar para o ponto final DO Gestor de Recursos Azure REST para permitir `{"identity":{"type":"SystemAssigned"}` a identidade gerida atribuída pelo sistema no seu VM, identificada no organismo de pedido pelo valor de um VM denominado *myVM*.  Substitua-o `<ACCESS TOKEN>` pelo valor que recebeu no passo anterior quando `<SUBSCRIPTION ID>` solicitou um token de acesso ao Portador e o valor adequado para o seu ambiente.
   
   > [!IMPORTANT]
   > Para garantir que não apaga quaisquer identidades geridas atribuídas ao utilizador existentes que sejam atribuídas ao VM, é necessário `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`enumerar as identidades geridas atribuídas pelo utilizador utilizando este comando CURL: . Se tiver alguma identidade gerida atribuída ao utilizador atribuída ao VM `identity` identificada no valor da resposta, salte para o passo 3 que lhe mostre como reter as identidades geridas atribuídas ao utilizador, permitindo a identidade gerida atribuída pelo sistema no seu VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        | 
   
   **Solicitar corpo**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Para permitir a identidade gerida atribuída pelo sistema num VM com identidades geridas `SystemAssigned` atribuídas `type` ao utilizador existentes, precisa de acrescentar ao valor.  
   
   Por exemplo, se o seu VM tiver as `ID1` `ID2` identidades geridas atribuídas ao utilizador e lhe for atribuída, e quiser adicionar ao VM a identidade gerida atribuída pelo sistema, utilize a seguinte chamada CURL. `<ACCESS TOKEN>` Substitua `<SUBSCRIPTION ID>` e com valores adequados ao seu ambiente.

   A versão `2018-06-01` API armazena identidades `userAssignedIdentities` geridas atribuídas ao utilizador no `identityIds` valor num formato dicionário `2017-12-01`em oposição ao valor num formato de matriz utilizado na versão API .
   
   **API VERSÃO 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        | 

   **Solicitar corpo**

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
    
   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        | 

   **Solicitar corpo**

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

Para desativar a identidade gerida atribuída pelo sistema num VM, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

1. Recupere um token de acesso ao Portador, que utilizará no próximo passo no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Atualize o VM utilizando o CURL para ligar para o ponto final DO REST do Gestor de Recursos Do Azure para desativar a identidade gerida atribuída pelo sistema.  O exemplo seguinte desativa a identidade gerida atribuída pelo sistema `{"identity":{"type":"None"}}` identificada no organismo de pedido pelo valor de um VM denominado *myVM*.  Substitua-o `<ACCESS TOKEN>` pelo valor que recebeu no passo anterior quando `<SUBSCRIPTION ID>` solicitou um token de acesso ao Portador e o valor adequado para o seu ambiente.

   > [!IMPORTANT]
   > Para garantir que não apaga quaisquer identidades geridas atribuídas ao utilizador existentes que sejam atribuídas ao VM, é necessário `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`enumerar as identidades geridas atribuídas pelo utilizador utilizando este comando CURL: . Se tiver alguma identidade gerida atribuída ao Utilizador atribuída ao VM `identity` identificada no valor da resposta, salte para o passo 3 que lhe mostre como reter identidades geridas atribuídas ao utilizador enquanto desativa a identidade gerida atribuída pelo sistema no seu VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        | 

   **Solicitar corpo**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Para remover a identidade gerida atribuída pelo sistema a partir de uma máquina `SystemAssigned` virtual `{"identity":{"type:" "}}` que tenha `UserAssigned` identidades `userAssignedIdentities` geridas atribuídas pelo utilizador, remova do valor mantendo o valor e os valores do dicionário se estiver a utilizar a **versão API 2018-06-01**. Se estiver a utilizar a **versão API 2017-12-01** ou mais cedo, mantenha a `identityIds` matriz.

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída ao utilizador

Nesta secção, aprende-se a adicionar e remover a identidade gerida atribuída pelo utilizador num Azure VM utilizando o CURL para fazer chamadas para o ponto final do Gestor de Recursos Azure REST.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador durante a criação de um VM Azure

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções de Colaborador de [Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e Operador de [Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Não são necessárias atribuições adicionais de diretório da AD.

1. Recupere um token de acesso ao Portador, que utilizará no próximo passo no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para o seu VM:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso ao Portador, que utilizará no próximo passo no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma identidade gerida atribuída ao utilizador utilizando as instruções aqui encontradas: [Criar uma identidade gerida atribuída](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)ao utilizador .

5. Crie um VM utilizando o CURL para ligar para o ponto final do Gestor de Recursos Azure. O exemplo seguinte cria um VM denominado *myVM* no grupo de recursos `ID1` *myResourceGroup* com uma identidade gerida `"identity":{"type":"UserAssigned"}`atribuída ao utilizador, identificada no organismo de pedido pelo valor . Substitua-o `<ACCESS TOKEN>` pelo valor que recebeu no passo anterior quando `<SUBSCRIPTION ID>` solicitou um token de acesso ao Portador e o valor adequado para o seu ambiente.
 
   **API VERSÃO 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        | 

   **Solicitar corpo**

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

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        | 

   **Solicitar corpo**

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador a um VM Azure existente

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções de Colaborador de [Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e Operador de [Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Não são necessárias atribuições adicionais de diretório da AD.

1. Recupere um token de acesso ao Portador, que utilizará no próximo passo no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Criar uma identidade gerida atribuída ao utilizador utilizando as instruções aqui encontradas, [Criar uma identidade gerida atribuída](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)ao utilizador .

3. Para garantir que não apaga as identidades geridas existentes do utilizador ou do sistema que são atribuídas ao VM, é necessário enumerar os tipos de identidade atribuídos ao VM utilizando o seguinte comando CURL. Se tiver gerido identidades atribuídas ao conjunto de escala de `identity` máquina virtual, estão listados no valor.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`

    Se tiver alguma identidade gerida atribuída ao utilizador ou ao sistema atribuída ao `identity` VM como identificada no valor da resposta, salte para o passo 5 que lhe mostre como manter a identidade gerida atribuída ao sistema, adicionando uma identidade gerida atribuída pelo utilizador no seu VM.

4. Se não tiver identidades geridas atribuídas ao utilizador atribuídas ao seu VM, utilize o seguinte comando CURL para ligar para o ponto final do Gestor de Recursos Azure REST para atribuir a primeira identidade gerida atribuída ao VM.

   Os seguintes exemplos atribuem uma identidade `ID1` gerida atribuída pelo utilizador a um VM denominado *myVM* no grupo de recursos *myResourceGroup*.  Substitua-o `<ACCESS TOKEN>` pelo valor que recebeu no passo anterior quando `<SUBSCRIPTION ID>` solicitou um token de acesso ao Portador e o valor adequado para o seu ambiente.

   **API VERSÃO 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        |
 
   **Solicitar corpo**

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
   
   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        | 

   **Solicitar corpo**

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

5. Se tiver uma identidade gerida atribuída ao utilizador ou atribuída ao sistema atribuída ao seu VM:
   
   **API VERSÃO 2018-06-01**

   Adicione a identidade gerida atribuída ao `userAssignedIdentities` utilizador ao valor do dicionário.
    
   Por exemplo, se tiver uma identidade gerida atribuída pelo sistema e `ID1` a identidade gerida atribuída ao utilizador atualmente atribuída ao seu `ID2` VM e quiser adicionar-lhe a identidade gerida atribuída pelo utilizador:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        | 

   **Solicitar corpo**

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

   Mantenha as identidades geridas atribuídas ao utilizador `identityIds` que gostaria de manter no valor de matriz, ao mesmo tempo que adiciona a nova identidade gerida atribuída ao utilizador.

   Por exemplo, se tiver uma identidade gerida atribuída pelo sistema e `ID1` a identidade gerida atribuída ao utilizador atualmente atribuída ao seu `ID2` VM e quiser adicionar-lhe a identidade gerida atribuída pelo utilizador: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        | 

   **Solicitar corpo**

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remova uma identidade gerida atribuída ao utilizador de um VM Azure

Para remover uma identidade atribuída ao utilizador a um VM, a sua conta necessita da atribuição da função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

1. Recupere um token de acesso ao Portador, que utilizará no próximo passo no cabeçalho de Autorização para criar o seu VM com uma identidade gerida atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Para garantir que não apaga quaisquer identidades geridas atribuídas ao utilizador existentes que deseja manter atribuídas ao VM ou remover a identidade gerida atribuída pelo sistema, é necessário enumerar as identidades geridas utilizando o seguinte comando CURL: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`
 
   Se tiver gerido identidades atribuídas ao VM, estão listadas na resposta no `identity` valor.

   Por exemplo, se tiver identidades geridas `ID1` `ID2` atribuídas ao utilizador e atribuída seleções ao seu VM, e apenas pretender manter `ID1` a identidade atribuída e manter a identidade atribuída ao sistema:
   
   **API VERSÃO 2018-06-01**

   Adicione `null` à identidade gerida atribuída pelo utilizador que gostaria de remover:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        | 

   **Solicitar corpo**

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

   Mantenha apenas a identidade(s) gerida atribuída pelo utilizador `identityIds` que deseja manter na matriz:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        | 

   **Solicitar corpo**

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
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Pedir cabeçalhos**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer` | 

**Solicitar corpo**

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

**Pedir cabeçalhos**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`| 

**Solicitar corpo**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como criar, listar ou eliminar identidades geridas atribuídas pelo utilizador utilizando o REST ver:

- [Criar, listar ou eliminar as identidades geridas atribuídas pelo utilizador utilizando chamadas REST API](how-to-manage-ua-identity-rest.md)
