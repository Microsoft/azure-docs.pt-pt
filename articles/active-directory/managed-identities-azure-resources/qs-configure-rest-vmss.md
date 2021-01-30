---
title: Configure identidades geridas em conjunto de escala de máquina virtual Azure usando REST - Azure AD
description: Instruções passo a passo para configurar um sistema e identidades geridas atribuídas pelo utilizador num conjunto de escala de máquina virtual Azure utilizando o CURL para fazer chamadas de API REST.
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
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb14a6a82535ac5a7d36213b082d08aedbb44da5
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090625"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Configure identidades geridas para recursos Azure em uma escala de máquina virtual definida usando chamadas REST API

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade de sistema gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código. 

Neste artigo, utilizando o CURL para fazer chamadas para o ponto final do Azure Resource Manager REST, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num conjunto de escala de máquina virtual:

- Ativar e desativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual Azure
- Adicione e remova uma identidade gerida atribuída pelo utilizador num conjunto de escala de máquina virtual Azure

Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com identidades geridas para recursos Azure, veja [o que são identidades geridas para os recursos do Azure?](overview.md) Para saber mais sobre os tipos de identidade geridos atribuídos pelo sistema e atribuídos pelo utilizador, consulte [os tipos de identidade geridos](overview.md#managed-identity-types).

- Para realizar as operações de gestão neste artigo, a sua conta necessita das seguintes atribuições de funções Azure:

  - [Contribuinte de máquinas virtuais](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para criar um conjunto de escala de máquina virtual e ativar e remover sistema e/ou identidade gerida atribuída pelo utilizador a partir de um conjunto de escala de máquina virtual.

  - [Papel de Contribuinte de Identidade Gerido](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) para criar uma identidade gerida atribuída pelo utilizador.

  - [Função de Operador de Identidade Gerida](../../role-based-access-control/built-in-roles.md#managed-identity-operator) para atribuir e remover uma identidade atribuída ao utilizador de e para um conjunto de escala de máquina virtual.

  > [!NOTE]
  > Não são necessárias atribuições adicionais de diretório ad AD.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprende-se a ativar e desativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual utilizando o CURL para fazer chamadas para o ponto final do Azure Resource Manager REST.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema durante a criação de um conjunto de escala de máquina virtual

Para criar um conjunto de escala de máquina virtual com identidade gerida atribuída pelo sistema, é necessário criar um conjunto de escala de máquina virtual e recuperar um token de acesso para usar o CURL para ligar para o ponto final do Gestor de Recursos com o valor do tipo de identidade gerido atribuído pelo sistema.

1. Crie um [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) para a contenção e implantação do seu conjunto de escalas de máquinas virtuais e seus recursos relacionados, utilizando a [criação do grupo AZ](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic#az-network-nic-create) para o seu conjunto de escala de máquina virtual:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso ao Bearer, que utilizará no passo seguinte no cabeçalho de Autorização para criar o conjunto de escala de máquina virtual com uma identidade gerida pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Utilizando a Azure Cloud Shell, crie um conjunto de escala de máquina virtual utilizando o CURL para chamar o ponto final do Azure Resource Manager REST. O exemplo a seguir cria um conjunto de escala de máquina virtual denominado *myVMSS* no *myResourceGroup* com uma identidade gerida atribuída ao sistema, tal como identificado no organismo de pedido pelo valor `"identity":{"type":"SystemAssigned"}` . `<ACCESS TOKEN>`Substitua-o pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Bearer e o `<SUBSCRIPTION ID>` valor adequado para o seu ambiente.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido. | 

   **Corpo de pedido**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual existente

Para ativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual existente, é necessário adquirir um token de acesso e, em seguida, utilizar o CURL para ligar para o ponto final do Gestor de Recursos REST para atualizar o tipo de identidade.

1. Recupere um token de acesso ao Bearer, que utilizará no passo seguinte no cabeçalho de Autorização para criar o conjunto de escala de máquina virtual com uma identidade gerida pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utilize o seguinte comando CURL para ligar para o ponto final do Azure Resource Manager REST para permitir a identidade gerida atribuída pelo sistema no seu conjunto de escala de máquina virtual, tal como identificado no corpo de pedido pelo valor `{"identity":{"type":"SystemAssigned"}` de um conjunto de escala de máquina virtual denominado *myVMSS*.  `<ACCESS TOKEN>`Substitua-o pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Bearer e o `<SUBSCRIPTION ID>` valor adequado para o seu ambiente.
   
   > [!IMPORTANT]
   > Para garantir que não elimina quaisquer identidades geridas atribuídas ao utilizador que sejam atribuídas ao conjunto de escalas de máquinas virtuais, é necessário listar as identidades geridas atribuídas pelo utilizador utilizando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Se tiver alguma identidade gerida atribuída ao utilizador atribuída à escala de máquina virtual identificada como identificada no valor da `identity` resposta, salte para o passo 3 que lhe mostre como reter identidades geridas atribuídas pelo utilizador, permitindo a identidade gerida atribuída pelo sistema no seu conjunto de escala de máquina virtual.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

3. Para ativar a identidade gerida atribuída pelo sistema numa escala de máquina virtual definida com identidades geridas atribuídas pelo utilizador, é necessário adicionar `SystemAssigned` ao `type` valor.  
   
   Por exemplo, se o seu conjunto de escala de máquina virtual tiver as identidades geridas atribuídas pelo utilizador `ID1` e `ID2` lhe for atribuído, e pretender adicionar a identidade gerida atribuída ao conjunto de escala de máquina virtual, utilize a seguinte chamada CURL. Substitua `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` por valores adequados ao seu ambiente.

   A versão API `2018-06-01` armazena identidades geridas atribuídas pelo utilizador `userAssignedIdentities` no valor num formato dicionário em oposição ao `identityIds` valor num formato de matriz utilizado na versão API `2017-12-01` .
   
   **VERSÃO API 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
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

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Desativar a identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual

Para desativar uma identidade atribuída ao sistema num conjunto de escala de máquina virtual existente, é necessário adquirir um token de acesso e, em seguida, utilizar o CURL para ligar para o ponto final do Gestor de Recursos REST para atualizar o tipo de identidade para `None` .

1. Recupere um token de acesso ao Bearer, que utilizará no passo seguinte no cabeçalho de Autorização para criar o conjunto de escala de máquina virtual com uma identidade gerida pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Atualize o conjunto de escala de máquina virtual utilizando o CURL para chamar o ponto final do Azure Resource Manager REST para desativar a identidade gerida atribuída pelo sistema.  O exemplo a seguir desativa a identidade gerida atribuída pelo sistema, identificada no organismo de pedido pelo valor `{"identity":{"type":"None"}}` de um conjunto de escala de máquina virtual denominado *myVMSS*.  `<ACCESS TOKEN>`Substitua-o pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Bearer e o `<SUBSCRIPTION ID>` valor adequado para o seu ambiente.

   > [!IMPORTANT]
   > Para garantir que não elimina quaisquer identidades geridas atribuídas ao utilizador que sejam atribuídas ao conjunto de escalas de máquinas virtuais, é necessário listar as identidades geridas atribuídas pelo utilizador utilizando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Se tiver alguma identidade gerida atribuída ao utilizador atribuído à balança de máquinas virtual, salte para o passo 3 que lhe mostre como reter as identidades geridas atribuídas pelo utilizador, ao mesmo tempo que remove a identidade gerida atribuída pelo sistema do seu conjunto de escala de máquina virtual.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"None"
       }
    }
   ```

   Para remover a identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual que tenha identidades geridas atribuídas ao utilizador, remova `SystemAssigned` do `{"identity":{"type:" "}}` valor mantendo o valor e os `UserAssigned` valores do dicionário se estiver a `userAssignedIdentities` utilizar a versão **API 2018-06-01**. Se estiver a utilizar **a versão API 2017-12-01** ou mais cedo, mantenha a `identityIds` matriz.

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, aprende-se a adicionar e remover a identidade gerida atribuída pelo utilizador num conjunto de escala de máquina virtual utilizando o CURL para fazer chamadas para o ponto final do Azure Resource Manager REST.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuída ao utilizador durante a criação de um conjunto de escala de máquina virtual

1. Recupere um token de acesso ao Bearer, que utilizará no passo seguinte no cabeçalho de Autorização para criar o conjunto de escala de máquina virtual com uma identidade gerida pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic#az-network-nic-create) para o seu conjunto de escala de máquina virtual:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso ao Bearer, que utilizará no passo seguinte no cabeçalho de Autorização para criar o conjunto de escala de máquina virtual com uma identidade gerida pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Criar uma identidade gerida atribuída pelo utilizador utilizando as instruções aqui encontradas: [Criar uma identidade gerida atribuída pelo utilizador](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Crie um conjunto de balança de máquina virtual utilizando o CURL para chamar o ponto final do Azure Resource Manager REST. O exemplo a seguir cria um conjunto de escala de máquina virtual denominado *myVMSS* no grupo de recursos *myResourceGroup* com uma identidade gerida atribuída pelo utilizador `ID1` , tal como identificado no organismo de pedido pelo valor `"identity":{"type":"UserAssigned"}` . `<ACCESS TOKEN>`Substitua-o pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Bearer e o `<SUBSCRIPTION ID>` valor adequado para o seu ambiente.
 
   **VERSÃO API 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido. | 

   **Corpo de pedido**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **VERSÃO API 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido. |
 
   **Corpo de pedido**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Atribua uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual Azure existente

1. Recupere um token de acesso ao Bearer, que utilizará no passo seguinte no cabeçalho de Autorização para criar o conjunto de escala de máquina virtual com uma identidade gerida pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Criar uma identidade gerida atribuída pelo utilizador utilizando as instruções encontradas aqui, [Crie uma identidade gerida atribuída pelo utilizador](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Para garantir que não elimina as identidades geridas do utilizador ou do sistema que são atribuídas ao conjunto de escalas de máquinas virtuais, é necessário listar os tipos de identidade atribuídos à escala de máquina virtual definida utilizando o seguinte comando CURL. Se tiver gerido identidades atribuídas ao conjunto de escala de máquina virtual, estão listadas no `identity` valor.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido. |   
 

4. Se não tiver identidades geridas atribuídas ao utilizador ou sistema atribuído ao seu conjunto de escala de máquina virtual, utilize o seguinte comando CURL para ligar para o ponto final do Azure Resource Manager REST para atribuir a primeira identidade gerida atribuída ao conjunto de escala de máquina virtual.  Se tiver uma identidade gerida atribuída ao utilizador ou ao sistema atribuído à escala de máquina virtual, salte para o passo 5 que lhe mostre como adicionar várias identidades geridas atribuídas ao utilizador a um conjunto de escala de máquina virtual, mantendo também a identidade gerida atribuída pelo sistema.

   O exemplo a seguir atribui uma identidade gerida atribuída ao utilizador a `ID1` um conjunto de escala de máquina virtual denominado *myVMSS* no grupo de recursos *myResourceGroup*.  `<ACCESS TOKEN>`Substitua-o pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Bearer e o `<SUBSCRIPTION ID>` valor adequado para o seu ambiente.

   **VERSÃO API 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **VERSÃO API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Se tiver uma identidade gerida atribuída ao utilizador ou ao sistema atribuída ao seu conjunto de escala de máquina virtual:
   
   **VERSÃO API 2018-06-01**

   Adicione a identidade gerida atribuída ao valor do `userAssignedIdentities` dicionário.

   Por exemplo, se tiver a identidade gerida atribuída ao sistema e a identidade gerida atribuída ao utilizador `ID1` atualmente atribuída à sua escala de máquina virtual e gostaria de lhe adicionar a identidade gerida atribuída `ID2` pelo utilizador:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

   Por exemplo, se tiver identidade atribuída ao sistema e a identidade gerida atribuída ao utilizador `ID1` atualmente atribuída ao seu conjunto de escala de máquina virtual e gostaria de lhe adicionar a identidade gerida atribuída `ID2` pelo utilizador:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remova uma identidade gerida atribuída pelo utilizador de um conjunto de escala de máquina virtual

1. Recupere um token de acesso ao Bearer, que utilizará no passo seguinte no cabeçalho de Autorização para criar o conjunto de escala de máquina virtual com uma identidade gerida pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Para garantir que não elimina quaisquer identidades geridas atribuídas pelo utilizador que gostaria de manter atribuídas ao conjunto de escalas de máquina virtual ou remover a identidade gerida atribuída pelo sistema, tem de listar as identidades geridas utilizando o seguinte comando CURL:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos do pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido. |
   
   Se tiver gerido as identidades atribuídas ao VM, estão listadas na resposta no `identity` valor. 
    
   Por exemplo, se tiver identidades geridas atribuídas ao utilizador `ID1` e `ID2` atribuídas ao seu conjunto de escala de máquina virtual, e apenas pretender manter a identidade gerida atribuída pelo `ID1` sistema:

   **VERSÃO API 2018-06-01**

   Adicione `null` à identidade gerida atribuída ao utilizador que gostaria de remover:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Se o seu conjunto de escala de máquina virtual tiver identidades geridas atribuídas ao sistema e atribuídas pelo utilizador, pode remover todas as identidades geridas atribuídas pelo utilizador, mudando para utilizar apenas o sistema atribuído através do seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
    
Se o seu conjunto de escala de máquina virtual tiver apenas identidades geridas atribuídas pelo utilizador e quiser removê-las todas, utilize o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como criar, listar ou eliminar identidades geridas atribuídas pelo utilizador utilizando o REST consulte:

- [Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador usando chamadas REST API](how-to-manage-ua-identity-rest.md)
