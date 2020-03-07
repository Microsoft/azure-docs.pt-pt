---
title: Configure identidades geridas em Azure VMSS usando REST - Azure AD
description: Instruções passo a passo para configurar um sistema e identidades geridas atribuídas pelo utilizador num VMSS Azure utilizando o CURL para fazer chamadas REST API.
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
ms.openlocfilehash: dce9894b26d03c351a2209792cc076de91feba54
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375593"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Configure identidades geridas para recursos Azure em um conjunto de escala de máquina virtual usando chamadas REST API

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade de sistema gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código. 

Neste artigo, utilizando o CURL para fazer chamadas para o ponto final do Gestor de Recursos Do Azure, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num conjunto de escala de máquina virtual:

- Ativar e desativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual Azure
- Adicione e remova uma identidade gerida atribuída pelo utilizador num conjunto de escala de máquina virtual Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tem uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gestão neste artigo, a sua conta necessita das seguintes atribuições de controlo de acesso baseadas no papel Azure:

    > [!NOTE]
    > Não são necessárias atribuições adicionais de diretório da AD.

    - [Colaborador de máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de escala de máquina virtual e ativar e remover o sistema e/ou a identidade gerida atribuída pelo utilizador a partir de um conjunto de escala de máquina virtual.
    - [Função de Colaborador de Identidade Gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade gerida atribuída pelo utilizador.
    - [Função do Operador de Identidade Gerido](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade atribuída ao utilizador de e para um conjunto de escala de máquina virtual.
- Se estiver a utilizar o Windows, instale o [Subsistema Windows para o Linux](https://msdn.microsoft.com/commandline/wsl/about) ou utilize a [Casca de Nuvem Azure](../../cloud-shell/overview.md) no portal Azure.
- [Instale a consola local Azure CLI,](/cli/azure/install-azure-cli)se utilizar o [Subsistema Windows para O Linux](https://msdn.microsoft.com/commandline/wsl/about) ou um [SISTEMA de distribuição Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Se estiver a utilizar a consola local Azure CLI, inscreva-se no Azure usando `az login` com uma conta associada à subscrição Azure que deseja gerir o sistema ou as identidades geridas atribuídas pelo utilizador.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprende-se a ativar e desativar a identidade gerida atribuída pelo sistema numa escala de máquina virtual utilizando o CURL para fazer chamadas para o ponto final do Gestor de Recursos Azure REST.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema durante a criação de um conjunto de escala de máquina virtual

Para criar um conjunto de escala de máquina virtual com identidade gerida atribuída pelo sistema, é necessário criar um conjunto de escala de máquina virtual e recuperar um token de acesso para utilizar o CURL para ligar para o ponto final do Gestor de Recursos com o valor de tipo de identidade gerido atribuído pelo sistema.

1. Crie um grupo de [recursos](../../azure-resource-manager/management/overview.md#terminology) para contenção e implantação do seu conjunto de escala de máquina virtual e seus recursos relacionados, utilizando a criação do [grupo Az](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para o seu conjunto de escala de máquina virtual:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso ao Portador, que utilizará no próximo passo do cabeçalho de Autorização para criar a sua escala de máquina virtual definida com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie um conjunto de escala de máquina virtual utilizando o CURL para chamar o ponto final DO REST do Gestor de Recursos Azure. O exemplo seguinte cria um conjunto de escala de máquina virtual denominado *myVMSS* no *myResourceGroup* com uma identidade gerida atribuída pelo sistema, identificada no organismo de pedido pelo valor `"identity":{"type":"SystemAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Portador e o valor `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

   **Solicitar corpo**

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

1. Recupere um token de acesso ao Portador, que utilizará no próximo passo do cabeçalho de Autorização para criar a sua escala de máquina virtual definida com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utilize o seguinte comando CURL para ligar para o ponto final DO Gestor de Recursos Azure PARA ativar a identidade gerida atribuída pelo sistema no seu conjunto de escala de máquina virtual identificado no organismo de pedido pelo valor `{"identity":{"type":"SystemAssigned"}` para um conjunto de escala de máquina virtual chamado *myVMSS*.  Substitua `<ACCESS TOKEN>` pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Portador e o valor `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.
   
   > [!IMPORTANT]
   > Para garantir que não apaga quaisquer identidades geridas atribuídas ao utilizador existentes que sejam atribuídas ao conjunto de escala de máquina virtual, é necessário enumerar as identidades geridas atribuídas pelo utilizador utilizando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se tiver alguma identidade gerida atribuída ao utilizador atribuída à escala de máquina virtual identificada no valor `identity` na resposta, salte para o passo 3 que lhe mostre como reter identidades geridas atribuídas ao utilizador, permitindo a identidade gerida atribuída pelo sistema no seu conjunto de escala de máquina virtual.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

   **Solicitar corpo**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned"
       }
    }
   ```

3. Para permitir a identidade gerida atribuída pelo sistema numa escala de máquina virtual com identidades geridas atribuídas ao utilizador existentes, precisa de adicionar `SystemAssigned` ao valor `type`.  
   
   Por exemplo, se o seu conjunto de escala de máquina virtual tiver as identidades geridas atribuídas ao utilizador `ID1` e `ID2` atribuídas, e deseja adicionar a identidade gerida atribuída pelo sistema ao conjunto de escala de máquina virtual, utilize a seguinte chamada CURL. Substitua `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` por valores adequados ao seu ambiente.

   A versão API `2018-06-01` armazena identidades geridas atribuídas ao utilizador no valor `userAssignedIdentities` num formato dicionário em oposição ao valor `identityIds` num formato de matriz utilizado na versão API `2017-12-01`.
   
   **API VERSÃO 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. |
 
   **Solicitar corpo**

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
   
   **API VERSÃO 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

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

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Desativar a identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual

Para desativar uma identidade atribuída ao sistema num conjunto de escala de máquina virtual existente, é necessário adquirir um token de acesso e, em seguida, utilizar o CURL para ligar para o ponto final do Gestor de Recursos REST para atualizar o tipo de identidade para `None`.

1. Recupere um token de acesso ao Portador, que utilizará no próximo passo do cabeçalho de Autorização para criar a sua escala de máquina virtual definida com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Atualize o conjunto de escala de máquina virtual utilizando o CURL para ligar para o ponto final DO REST do Gestor de Recursos Azure para desativar a identidade gerida atribuída pelo sistema.  O exemplo seguinte desativa a identidade gerida atribuída pelo sistema identificada no organismo de pedido pelo valor `{"identity":{"type":"None"}}` de um conjunto de escala de máquina virtual denominado *myVMSS*.  Substitua `<ACCESS TOKEN>` pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Portador e o valor `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.

   > [!IMPORTANT]
   > Para garantir que não apaga quaisquer identidades geridas atribuídas ao utilizador existentes que sejam atribuídas ao conjunto de escala de máquina virtual, é necessário enumerar as identidades geridas atribuídas pelo utilizador utilizando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se tiver alguma identidade gerida atribuída ao utilizador atribuída ao conjunto de escala de máquina virtual, salte para o passo 3 que lhe mostre como reter as identidades geridas atribuídas pelo utilizador enquanto remove a identidade gerida atribuída pelo sistema do seu conjunto de escala de máquina virtual.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

   **Solicitar corpo**

   ```JSON
    {
       "identity":{
          "type":"None"
       }
    }
   ```

   Para remover a identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual que tenha identidades geridas atribuídas pelo utilizador, remova `SystemAssigned` do valor `{"identity":{"type:" "}}` mantendo o valor `UserAssigned` e os valores do dicionário `userAssignedIdentities` se estiver a utilizar a **versão API 2018-06-01**. Se estiver a utilizar a **versão API 2017-12-01** ou mais cedo, mantenha a `identityIds` matriz.

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída ao utilizador

Nesta secção, aprende-se a adicionar e remover a identidade gerida atribuída pelo utilizador numa escala de máquina virtual utilizando o CURL para fazer chamadas para o ponto final do Gestor de Recursos Azure REST.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuída ao utilizador durante a criação de um conjunto de escala de máquina virtual

1. Recupere um token de acesso ao Portador, que utilizará no próximo passo do cabeçalho de Autorização para criar a sua escala de máquina virtual definida com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para o seu conjunto de escala de máquina virtual:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso ao Portador, que utilizará no próximo passo do cabeçalho de Autorização para criar a sua escala de máquina virtual definida com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma identidade gerida atribuída ao utilizador utilizando as instruções aqui encontradas: [Criar uma identidade gerida atribuída](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)ao utilizador .

5. Crie um conjunto de escala de máquina virtual utilizando o CURL para chamar o ponto final DO REST do Gestor de Recursos Azure. O exemplo seguinte cria um conjunto de escala de máquina virtual denominado *myVMSS* no grupo de recursos *myResourceGroup* com uma identidade gerida atribuída pelo utilizador `ID1`, identificada no organismo de pedido pelo valor `"identity":{"type":"UserAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Portador e o valor `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.
 
   **API VERSÃO 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

   **Solicitar corpo**

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

   **API VERSÃO 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. |
 
   **Solicitar corpo**

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual Azure existente

1. Recupere um token de acesso ao Portador, que utilizará no próximo passo do cabeçalho de Autorização para criar a sua escala de máquina virtual definida com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Criar uma identidade gerida atribuída ao utilizador utilizando as instruções aqui encontradas, [Criar uma identidade gerida atribuída](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)ao utilizador .

3. Para garantir que não apaga as identidades geridas existentes do utilizador ou do sistema que são atribuídas ao conjunto de escala de máquina virtual, é necessário enumerar os tipos de identidade atribuídos à escala de máquina virtual definida utilizando o seguinte comando CURL. Se tiver gerido identidades atribuídas ao conjunto de escala de máquina virtual, estão listados no valor `identity`.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. |   
 

4. Se não tiver identidades geridas atribuídas ao utilizador ou ao sistema atribuídas ao seu conjunto de escala de máquina virtual, utilize o seguinte comando CURL para ligar para o ponto final do Gestor de Recursos Azure REST para atribuir a primeira identidade gerida atribuída ao utilizador à máquina virtual conjunto de escala.  Se tiver uma identidade(s) gerida atribuída ao utilizador ou ao sistema atribuído ao conjunto de escala de máquina virtual, salte para o passo 5 que lhe mostre como adicionar várias identidades geridas ao utilizador a um conjunto de escala de máquina virtual, mantendo também o sistema gerido identidade.

   O exemplo seguinte atribui uma identidade gerida atribuída pelo utilizador, `ID1` a um conjunto de escala de máquina virtual denominado *myVMSS* no grupo de recursos *myResourceGroup*.  Substitua `<ACCESS TOKEN>` pelo valor que recebeu no passo anterior quando solicitou um token de acesso ao Portador e o valor `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.

   **API VERSÃO 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

   **Solicitar corpo**

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
    
   **API VERSÃO 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

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

5. Se tiver uma identidade gerida atribuída ao utilizador ou atribuída ao sistema atribuída ao seu conjunto de escala de máquina virtual:
   
   **API VERSÃO 2018-06-01**

   Adicione a identidade gerida atribuída ao `userAssignedIdentities` valor do dicionário.

   Por exemplo, se tiver uma identidade gerida atribuída pelo sistema e a identidade gerida atribuída ao utilizador `ID1` atualmente atribuída à sua escala de máquina virtual e gostaria de adicionar a identidade gerida atribuída pelo utilizador `ID2` à mesma:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

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

   Mantenha as identidades geridas atribuídas ao utilizador que gostaria de manter no valor de matriz `identityIds`, acrescentando a nova identidade gerida atribuída ao utilizador.

   Por exemplo, se tiver identidade atribuída ao sistema e a identidade gerida atribuída ao utilizador `ID1` atualmente atribuída ao seu conjunto de máquinas virtuais e gostaria de adicionar a identidade gerida atribuída pelo utilizador `ID2` ao mesmo:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

    **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

   **Solicitar corpo**

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

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remova uma identidade gerida atribuída ao utilizador de um conjunto de escala de máquina virtual

1. Recupere um token de acesso ao Portador, que utilizará no próximo passo do cabeçalho de Autorização para criar a sua escala de máquina virtual definida com uma identidade gerida atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Para garantir que não apaga quaisquer identidades geridas atribuídas ao utilizador existentes que deseja manter atribuídas ao conjunto de escala de máquina virtual ou remover a identidade gerida atribuída pelo sistema, precisa de listar as identidades geridas utilizando o seguinte comando CURL :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. |
   
   Se tiver gerido identidades atribuídas ao VM, estão listadas na resposta no valor `identity`. 
    
   Por exemplo, se tiver identidades geridas atribuídas ao utilizador `ID1` e `ID2` atribuídas ao seu conjunto de escala de máquina virtual, e apenas pretende manter `ID1` atribuída e manter a identidade gerida atribuída pelo sistema:

   **API VERSÃO 2018-06-01**

   Adicione `null` à identidade gerida atribuída pelo utilizador que gostaria de remover:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

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

   Mantenha apenas a identidade(s) gerida atribuída pelo utilizador que gostaria de manter na matriz `identityIds`:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Pedir cabeçalhos**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

   **Solicitar corpo**

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

Se o seu conjunto de escala de máquina virtual tiver identidades geridas atribuídas ao sistema e atribuídas ao utilizador, pode remover todas as identidades geridas atribuídas pelo utilizador, mudando para utilização apenas atribuída pelo sistema utilizando o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Pedir cabeçalhos**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

**Solicitar corpo**

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

**Pedir cabeçalhos**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Desemparado com um token de acesso `Bearer` válido. | 

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

- [Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador utilizando chamadas REST API](how-to-manage-ua-identity-rest.md)
