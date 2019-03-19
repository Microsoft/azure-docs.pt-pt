---
title: Como configurar o sistema e de identidades geridas atribuído ao utilizador um VMSS do Azure com REST
description: Instruções passo a passo chama instruções para configurar um sistema e de identidades geridas atribuído ao utilizador num VMSS do Azure com o CURL para tornar a API de REST.
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
ms.openlocfilehash: b35470daf8ef037a72516690c75980a6d07e8e29
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097171"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Configurar identidades geridas para recursos do Azure num conjunto de dimensionamento através de chamadas à REST API

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade de sistema automaticamente gerido no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, com o CURL para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager, aprenderá a efetuar as seguintes identidades geridas para operações de recursos do Azure num conjunto de dimensionamento de máquina virtual:

- Ativar e desativar a identidade gerida atribuído de sistema num conjunto de dimensionamento de máquina virtual do Azure
- Adicionar e remover uma identidade gerida atribuído ao utilizador num conjunto de dimensionamento de máquina virtual do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para efetuar as operações de gestão neste artigo, a conta tem das atribuições de controlo de acesso baseado em funções do Azure seguintes:

    > [!NOTE]
    > Nenhum adicionais do Azure AD directory as atribuições de funções necessárias.

    - [Contribuinte de máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e ativar e remover o sistema de e/ou atribuído ao utilizador a identidade gerida de um conjunto de dimensionamento de máquina virtual.
    - [Contribuidor de identidade de geridos](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) identidade gerida de função para criar um atribuído ao utilizador.
    - [Gerido operador de identidade](/azure/role-based-access-control/built-in-roles#managed-identity-operator) função para atribuir e remover uma identidade de utilizador atribuída de e para um conjunto de dimensionamento de máquina virtual.
- Se estiver a utilizar o Windows, instale o [subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou utilizar o [Azure Cloud Shell](../../cloud-shell/overview.md) no portal do Azure.
- [Instalar a consola local da CLI do Azure](/cli/azure/install-azure-cli), se usar o [subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou uma [SO de distribuição de Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Se estiver a utilizar consola local da CLI do Azure, inicie sessão no Azure com `az login` com uma conta que está associada à subscrição do Azure que pretende gerir o sistema ou de identidades geridas atribuído ao utilizador.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Atribuído ao sistema de identidade gerida

Nesta secção, saiba como ativar e desativar a identidade gerida atribuído de sistema num conjunto de dimensionamento com o CURL para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuído de sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais

Para criar um conjunto de dimensionamento com a identidade gerida atribuídos do sistema ativada, tem de criar um conjunto de dimensionamento de máquina virtual e obter um token de acesso para utilizar CURL para chamar o ponto de final do Gestor de recursos com o valor do tipo atribuído de sistema de identidade gerida.

1. Criar uma [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para a contenção e a implantação de seu conjunto de dimensionamento de máquina virtual e respetivos recursos relacionados, usando [criar grupo az](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Criar uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para o dimensionamento de máquinas virtuais definido:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar o seu conjunto de dimensionamento com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie um conjunto de dimensionamento com o CURL para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo seguinte cria um conjunto nomeado de dimensionamento de máquina virtual *myVMSS* no *myResourceGroup* com um atribuído de sistema de identidade gerida, conforme identificado no corpo do pedido pelo valor `"identity":{"type":"SystemAssigned"}`. Substitua `<ACCESS TOKEN>` com o valor que recebeu no passo anterior quando tiver solicitado um token de portador de acesso e o `<SUBSCRIPTION ID>` valor conforme apropriado para o seu ambiente.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso. | 

   **Corpo do pedido**

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

### <a name="enable-system-assigned-managed-identity-on-a-existing-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuído de sistema num conjunto de dimensionamento de máquina virtual existente

Para ativar a identidade gerida atribuído de sistema num conjunto de dimensionamento de máquina virtual existente, terá de adquirir um token de acesso e, em seguida, utilize o CURL para chamar o ponto de extremidade REST do Resource Manager para atualizar o tipo de identidade.

1. Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar o seu conjunto de dimensionamento com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utilize o seguinte comando CURL para chamar o ponto de extremidade REST do Azure Resource Manager para ativar a identidade gerida atribuído de sistema no seu dimensionamento de máquina virtual definido conforme identificado no corpo do pedido pelo valor `{"identity":{"type":"SystemAssigned"}` para um conjunto de dimensionamento com o nome *myVMSS*.  Substitua `<ACCESS TOKEN>` com o valor que recebeu no passo anterior quando tiver solicitado um token de portador de acesso e o `<SUBSCRIPTION ID>` valor conforme apropriado para o seu ambiente.
   
   > [!IMPORTANT]
   > Para garantir não elimina quaisquer existentes atribuído ao utilizador identidades geridas que são atribuídas ao conjunto de dimensionamento de máquina virtual, tem de listar as identidades geridas atribuído ao utilizador ao utilizar este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se tiver algum atribuído ao utilizador gerido identidades atribuídas ao conjunto conforme indicado de dimensionamento de máquina virtual a `identity` valor na resposta, avance para o passo 3, que mostra como reter atribuído ao utilizador identidades geridas ao ativar o sistema atribuído identidade gerida no seu conjunto de dimensionamento de máquina virtual.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

3. Para ativar a identidade gerida atribuído de sistema num conjunto com existentes atribuído ao utilizador identidades geridas de dimensionamento de máquina virtual, tem de adicionar `SystemAssigned` para o `type` valor.  
   
   Por exemplo, se o conjunto de dimensionamento de máquinas virtuais tem a atribuído ao utilizador geridos identidades `ID1` e `ID2` atribuídos à mesma, e gostaria de adicionar atribuído de sistema de identidade gerida para o conjunto de dimensionamento de máquina virtual, utilize a seguinte chamada CURL. Substitua `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` com valores adequados ao seu ambiente.

   Versão de API `2018-06-01` armazena identidades geridas atribuído ao utilizador no `userAssignedIdentities` valor num formato de dicionário, em vez do `identityIds` valor num formato de matriz, utilizado a versão de API `2017-12-01`.
   
   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso. | 

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

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Desativar atribuído de sistema de identidade gerida de um conjunto de dimensionamento de máquina virtual

Para desativar uma identidade atribuída de sistema num conjunto de dimensionamento de máquina virtual existente, terá de adquirir um token de acesso e, em seguida, utilize o CURL para chamar o ponto de extremidade REST do Resource Manager para atualizar o tipo de identidade para `None`.

1. Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar o seu conjunto de dimensionamento com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Atualize o conjunto de dimensionamento com o CURL para chamar o ponto de extremidade REST do Azure Resource Manager para desativar a identidade gerida atribuído de sistema.  O exemplo seguinte desativa atribuído de sistema de identidade gerida, conforme identificado no corpo do pedido pelo valor `{"identity":{"type":"None"}}` de um conjunto nomeado de dimensionamento de máquina virtual *myVMSS*.  Substitua `<ACCESS TOKEN>` com o valor que recebeu no passo anterior quando tiver solicitado um token de portador de acesso e o `<SUBSCRIPTION ID>` valor conforme apropriado para o seu ambiente.

   > [!IMPORTANT]
   > Para garantir não elimina quaisquer existentes atribuído ao utilizador identidades geridas que são atribuídas ao conjunto de dimensionamento de máquina virtual, tem de listar as identidades geridas atribuído ao utilizador ao utilizar este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se tiver qualquer identidade gerida atribuído ao utilizador atribuída para o conjunto de dimensionamento de máquinas virtuais, avance para o passo 3, que mostra como manter as identidades geridas atribuído ao utilizador ao remover a identidade gerida atribuído de sistema do seu conjunto de dimensionamento de máquina virtual.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"None"
       }
    }
   ```

   Para remover a identidade gerida atribuído de sistema de um conjunto de dimensionamento de máquina virtual que tenha atribuído ao utilizador identidades geridas, remova `SystemAssigned` do `{"identity":{"type:" "}}` valor ao manter o `UserAssigned` valor e o `userAssignedIdentities` dicionário de valores se está a utilizar **2018-01 06 de versão de API**. Se estiver a utilizar **API versão 2017-12-01** ou anterior, mantenha o `identityIds` matriz.

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, saiba como adicionar e remover a identidade gerida atribuído ao utilizador num conjunto de dimensionamento com o CURL para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuído ao utilizador durante a criação de um conjunto de dimensionamento de máquinas virtuais

1. Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar o seu conjunto de dimensionamento com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Criar uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para o dimensionamento de máquinas virtuais definido:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar o seu conjunto de dimensionamento com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma identidade gerida atribuído ao utilizador com as instruções aqui: [Criar uma identidade gerida atribuído ao utilizador](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Crie um conjunto de dimensionamento com o CURL para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo seguinte cria um conjunto nomeado de dimensionamento de máquina virtual *myVMSS* no grupo de recursos *myResourceGroup* com uma identidade gerida atribuído ao utilizador `ID1`, conforme identificado no corpo do pedido pelo valor `"identity":{"type":"UserAssigned"}`. Substitua `<ACCESS TOKEN>` com o valor que recebeu no passo anterior quando tiver solicitado um token de portador de acesso e o `<SUBSCRIPTION ID>` valor conforme apropriado para o seu ambiente.
 
   **API VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso. | 

   **Corpo do pedido**

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

   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso. |
 
   **Corpo do pedido**

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuído ao utilizador a um conjunto de dimensionamento de máquina virtual do Azure existente

1. Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar o seu conjunto de dimensionamento com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Criar uma identidade gerida atribuído ao utilizador com as instruções aqui, [criar uma identidade gerida atribuído ao utilizador](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Para garantir que não a eliminar o utilizador existente ou de identidades geridas atribuído de sistema que estão atribuídas ao conjunto de dimensionamento de máquina virtual, precisa de listar a identidade do conjunto de tipos atribuídos para o dimensionamento de máquinas virtuais utilizando o comando CURL seguinte. Se o ter gerido identidades atribuídas ao conjunto de dimensionamento de máquina virtual, que são apresentadas no `identity` valor.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso. |   
 

4. Se não tiver qualquer utilizador ou atribuído o sistema gerido identidades atribuídas à sua dimensionamento de máquinas virtuais definido, utilize o seguinte comando CURL para chamar o ponto de extremidade REST do Azure Resource Manager para atribuir a primeira identidade gerida atribuído ao utilizador para a máquina virtual conjunto de dimensionamento.  Se tiver um utilizador ou sistema atribuído identity(s) geridos atribuídos ao conjunto de dimensionamento de máquina virtual, avance para o passo 5, que lhe mostra como adicionar várias identidades geridas atribuído ao utilizador a um conjunto de dimensionamento de máquina virtual enquanto mantém o sistema atribuído geridos identidade.

   O exemplo seguinte atribui uma identidade gerida atribuído ao utilizador, `ID1` para um conjunto nomeado de dimensionamento de máquina virtual *myVMSS* no grupo de recursos *myResourceGroup*.  Substitua `<ACCESS TOKEN>` com o valor que recebeu no passo anterior quando tiver solicitado um token de portador de acesso e o `<SUBSCRIPTION ID>` valor conforme apropriado para o seu ambiente.

   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
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

   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso. | 

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

5. Se tiver uma atribuído ao utilizador ou atribuída de sistema de identidade gerida existente atribuída ao conjunto de dimensionamento de máquina virtual:
   
   **API VERSION 2018-06-01**

   Adicionar a identidade gerida atribuído ao utilizador para o `userAssignedIdentities` valor de dicionário.

   Por exemplo, se tiver atribuído o sistema de identidade gerida e a identidade gerida atribuídas `ID1` atualmente atribuída ao seu dimensionamento de máquinas virtuais e gostaria de adicionar a identidade gerida atribuído ao utilizador `ID2` a ele:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

   Por exemplo, se tiver atribuído o sistema de identidade e a identidade gerida atribuído ao utilizador `ID1` atualmente atribuídos ao conjunto de dimensionamento de máquina virtual e gostaria de adicionar a identidade gerida atribuído ao utilizador `ID2` a ele:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remover uma identidade gerida atribuído ao utilizador a partir de um conjunto de dimensionamento de máquina virtual

1. Obter um token de acesso de portador, que irá utilizar no próximo passo no cabeçalho de autorização para criar o seu conjunto de dimensionamento com uma identidade gerida atribuído de sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Para garantir que não elimina quaisquer existentes geridos identidiades atribuídas que gostaria de manter atribuídos ao conjunto de dimensionamento de máquina virtual ou remova a identidade gerida atribuído de sistema, precisa listar as identidades geridas utilizando o comando CURL seguinte :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de pedido**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso. |
   
   Se o ter gerido identidades atribuídas à VM, estão listados na resposta no `identity` valor. 
    
   Por exemplo, se tiver atribuído ao utilizador identidades geridas `ID1` e `ID2` atribuídos ao conjunto de dimensionamento de máquina virtual e pretender manter `ID1` atribuído e reter a identidade gerida atribuído de sistema:

   **API VERSION 2018-06-01**

   Adicionar `null` para o atribuído ao utilizador que pretende remover a identidade de gerido:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Se o conjunto de dimensionamento de máquinas virtuais tem ambos atribuído de sistema e identidades geridas atribuído ao utilizador, pode remover todas as identidades geridas atribuído ao utilizador alternando para utilizar apenas sistema atribuídas com o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
    
Se o conjunto de dimensionamento de máquinas virtuais tem apenas atribuído ao utilizador identidades geridas e gostaria de remover todos eles, utilize o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre como criar, listar ou eliminar identidades geridas atribuído ao utilizador com o REST, consulte:

- [Criar, lista ou eliminar uma identidade gerida atribuído ao utilizador através de chamadas à REST API](how-to-manage-ua-identity-rest.md)
