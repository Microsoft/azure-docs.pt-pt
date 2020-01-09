---
title: Configurar identidades gerenciadas no Azure VMSS usando REST – Azure AD
description: Instruções passo a passo para configurar um sistema e identidades gerenciadas atribuídas pelo usuário em um VMSS do Azure usando a rotação para fazer chamadas à API REST.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429985"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando chamadas à API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades gerenciadas para recursos do Azure fornece aos serviços do Azure uma identidade de sistema gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, usando a ONDULAção para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager, você aprende a executar as seguintes identidades gerenciadas para operações de recursos do Azure em um conjunto de dimensionamento de máquinas virtuais:

- Habilitar e desabilitar a identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais do Azure
- Adicionar e remover uma identidade gerenciada atribuída pelo usuário em um conjunto de dimensionamento de máquinas virtuais do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Se ainda não tem uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar as operações de gerenciamento neste artigo, sua conta precisa das seguintes atribuições de controle de acesso com base em função do Azure:

    > [!NOTE]
    > Nenhuma atribuição adicional de função de diretório do Azure AD é necessária.

    - [Colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e habilitar e remover o sistema e/ou a identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais.
    - Função [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade gerenciada atribuída pelo usuário.
    - Função de [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade atribuída pelo usuário de e para um conjunto de dimensionamento de máquinas virtuais.
- Se você estiver usando o Windows, instale o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou use o [Azure cloud Shell](../../cloud-shell/overview.md) no portal do Azure.
- [Instale o console local do CLI do Azure](/cli/azure/install-azure-cli), se você usar o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou um [so de distribuição Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Se você estiver usando CLI do Azure console local, entre no Azure usando `az login` com uma conta que esteja associada à assinatura do Azure que você gostaria de gerenciar as identidades gerenciadas atribuídas pelo usuário ou pelo sistema.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá a habilitar e desabilitar a identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais usando a rotação para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Habilitar a identidade gerenciada atribuída pelo sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais

Para criar um conjunto de dimensionamento de máquinas virtuais com identidade gerenciada atribuída pelo sistema habilitada, você precisa criar um conjunto de dimensionamento de máquinas virtuais e recuperar um token de acesso para usar a rotação para chamar o ponto de extremidade do Resource Manager com o valor do tipo de identidade gerenciado atribuído pelo sistema.

1. Crie um [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) para confinamento e implantação de seu conjunto de dimensionamento de máquinas virtuais e seus recursos relacionados, usando [AZ Group Create](/cli/azure/group/#az-group-create). Pode ignorar este passo se já tiver o grupo de recursos que pretende utilizar em vez disso:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para o conjunto de dimensionamento de máquinas virtuais:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie um conjunto de dimensionamento de máquinas virtuais usando a rotação para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais chamado *myVMSS* no grupo *MyResource* com uma identidade gerenciada atribuída pelo sistema, conforme identificado no corpo da solicitação pelo valor `"identity":{"type":"SystemAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor que você recebeu na etapa anterior quando solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

   **Corpo da solicitação**

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

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Habilitar identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais existente

Para habilitar a identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais existente, você precisa adquirir um token de acesso e, em seguida, usar a rotação para chamar o ponto de extremidade REST do Resource Manager para atualizar o tipo de identidade.

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Use o comando de rotação a seguir para chamar o ponto de extremidade REST do Azure Resource Manager para habilitar a identidade gerenciada atribuída pelo sistema em seu conjunto de dimensionamento de máquinas virtuais, conforme identificado no corpo da solicitação pelo valor `{"identity":{"type":"SystemAssigned"}` para um conjunto de dimensionamento de máquinas virtuais chamado *myVMSS*.  Substitua `<ACCESS TOKEN>` pelo valor que você recebeu na etapa anterior quando solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.
   
   > [!IMPORTANT]
   > Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário existente atribuída ao conjunto de dimensionamento de máquinas virtuais, você precisa listar as identidades gerenciadas atribuídas pelo usuário usando esse comando de rotação: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se você tiver qualquer identidade gerenciada atribuída pelo usuário atribuída ao conjunto de dimensionamento de máquinas virtuais, conforme identificado no valor `identity` na resposta, pule para a etapa 3 que mostra como manter identidades gerenciadas atribuídas pelo usuário ao habilitar a identidade gerenciada atribuída pelo sistema em seu conjunto de dimensionamento de máquinas virtuais.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned"
       }
    }
   ```

3. Para habilitar a identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais com identidades gerenciadas atribuídas pelo usuário existentes, você precisa adicionar `SystemAssigned` ao valor de `type`.  
   
   Por exemplo, se o conjunto de dimensionamento de máquinas virtuais tiver as identidades gerenciadas atribuídas pelo usuário `ID1` e `ID2` atribuídas a ela, e você quiser adicionar uma identidade gerenciada atribuída pelo sistema ao conjunto de dimensionamento de máquinas virtuais, use a seguinte chamada de ONDULAção. Substitua `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` com os valores apropriados para o seu ambiente.

   A versão de API `2018-06-01` armazena identidades gerenciadas atribuídas pelo usuário no valor de `userAssignedIdentities` em um formato de dicionário, ao contrário do valor `identityIds` em um formato de matriz usado em `2017-12-01`de versão de API.
   
   **VERSÃO DA API 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. |
 
   **Corpo da solicitação**

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
   
   **VERSÃO DA API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

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

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Desabilitar a identidade gerenciada atribuída pelo sistema de um conjunto de dimensionamento de máquinas virtuais

Para desabilitar uma identidade atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais existente, você precisa adquirir um token de acesso e, em seguida, usar a rotação para chamar o ponto de extremidade REST do Resource Manager para atualizar o tipo de identidade para `None`.

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Atualize o conjunto de dimensionamento de máquinas virtuais usando a rotação para chamar o ponto de extremidade REST Azure Resource Manager para desabilitar a identidade gerenciada atribuída pelo sistema.  O exemplo a seguir desabilita a identidade gerenciada atribuída pelo sistema conforme identificada no corpo da solicitação pelo valor `{"identity":{"type":"None"}}` de um conjunto de dimensionamento de máquinas virtuais chamado *myVMSS*.  Substitua `<ACCESS TOKEN>` pelo valor que você recebeu na etapa anterior quando solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.

   > [!IMPORTANT]
   > Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário existente atribuída ao conjunto de dimensionamento de máquinas virtuais, você precisa listar as identidades gerenciadas atribuídas pelo usuário usando esse comando de rotação: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se você tiver qualquer identidade gerenciada atribuída pelo usuário atribuída ao conjunto de dimensionamento de máquinas virtuais, pule para a etapa 3 que mostra como reter as identidades gerenciadas atribuídas pelo usuário ao remover a identidade gerenciada atribuída pelo sistema do seu conjunto de dimensionamento de máquinas virtuais.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"None"
       }
    }
   ```

   Para remover a identidade gerenciada atribuída pelo sistema de um conjunto de dimensionamento de máquinas virtuais que tenha identidades gerenciadas atribuídas pelo usuário, remova `SystemAssigned` do valor de `{"identity":{"type:" "}}` ao manter o valor de `UserAssigned` e os valores de dicionário de `userAssignedIdentities` se você estiver usando a **versão de API 2018-06-01**. Se você estiver usando a **versão de API 2017-12-01** ou anterior, mantenha a matriz de `identityIds`.

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá a adicionar e remover a identidade gerenciada atribuída pelo usuário em um conjunto de dimensionamento de máquinas virtuais usando a rotação para fazer chamadas para o ponto de extremidade REST do Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída pelo usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) para o conjunto de dimensionamento de máquinas virtuais:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma identidade gerenciada atribuída pelo usuário usando as instruções encontradas aqui: [criar uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Crie um conjunto de dimensionamento de máquinas virtuais usando a rotação para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais chamado *myVMSS* no grupo de recursos *MyResource* Group com uma identidade gerenciada atribuída pelo usuário `ID1`, conforme identificado no corpo da solicitação pelo valor `"identity":{"type":"UserAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor que você recebeu na etapa anterior quando solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.
 
   **VERSÃO DA API 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

   **Corpo da solicitação**

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

   **VERSÃO DA API 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. |
 
   **Corpo da solicitação**

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais do Azure existente

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Crie uma identidade gerenciada atribuída pelo usuário usando as instruções encontradas aqui, [crie uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Para garantir que você não exclua as identidades gerenciadas atribuídas pelo usuário ou pelo sistema que são atribuídas ao conjunto de dimensionamento de máquinas virtuais, você precisa listar os tipos de identidade atribuídos ao conjunto de dimensionamento de máquinas virtuais usando o comando de rotação a seguir. Se você tiver identidades gerenciadas atribuídas ao conjunto de dimensionamento de máquinas virtuais, elas serão listadas no valor `identity`.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. |   
 

4. Se você não tiver nenhuma identidade gerenciada atribuída pelo usuário ou pelo sistema atribuída ao seu conjunto de dimensionamento de máquinas virtuais, use o comando de rotação a seguir para chamar o ponto de extremidade REST do Azure Resource Manager para atribuir a primeira identidade gerenciada atribuída pelo usuário à máquina virtual conjunto de dimensionamento.  Se você tiver um usuário ou identidades gerenciadas atribuídas pelo sistema atribuídas ao conjunto de dimensionamento de máquinas virtuais, pule para a etapa 5 que mostra como adicionar várias identidades gerenciadas atribuídas pelo usuário a um conjunto de dimensionamento de máquinas virtuais, mantendo também o gerenciado atribuído pelo sistema identidade.

   O exemplo a seguir atribui uma identidade gerenciada atribuída pelo usuário, `ID1` a um conjunto de dimensionamento de máquinas virtuais chamado *myVMSS* no grupo de recursos *MyResource*Group.  Substitua `<ACCESS TOKEN>` pelo valor que você recebeu na etapa anterior quando solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` conforme apropriado para o seu ambiente.

   **VERSÃO DA API 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

   **Corpo da solicitação**

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
    
   **VERSÃO DA API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

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

5. Se você tiver uma identidade gerenciada definida pelo usuário ou atribuída pelo sistema existente atribuída ao seu conjunto de dimensionamento de máquinas virtuais:
   
   **VERSÃO DA API 2018-06-01**

   Adicione a identidade gerenciada atribuída pelo usuário ao valor de dicionário de `userAssignedIdentities`.

   Por exemplo, se você tiver uma identidade gerenciada atribuída pelo sistema e a identidade gerenciada atribuída pelo usuário `ID1` atribuída atualmente à sua escala de máquina virtual e quiser adicionar a `ID2` de identidade gerenciada atribuída pelo usuário a ela:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

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

   Por exemplo, se você tiver uma identidade atribuída pelo sistema e a identidade gerenciada atribuída pelo usuário `ID1` atribuída atualmente ao seu conjunto de dimensionamento de máquinas virtuais e quiser adicionar o `ID2` de identidade gerenciada atribuída pelo usuário a ela:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

    **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

   **Corpo da solicitação**

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

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remover uma identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais

1. Recupere um token de acesso de portador, que será usado na próxima etapa no cabeçalho de autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída pelo sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Para garantir que você não exclua nenhuma identidade gerenciada atribuída pelo usuário existente que deseja manter atribuída ao conjunto de dimensionamento de máquinas virtuais ou remova a identidade gerenciada atribuída pelo sistema, é necessário listar as identidades gerenciadas usando o comando de ONDULAção a seguir :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. |
   
   Se você tiver identidades gerenciadas atribuídas à VM, elas serão listadas na resposta no valor `identity`. 
    
   Por exemplo, se você tiver identidades gerenciadas atribuídas pelo usuário `ID1` e `ID2` atribuídas ao conjunto de dimensionamento de máquinas virtuais e quiser manter `ID1` atribuídas e manter a identidade gerenciada atribuída pelo sistema:

   **VERSÃO DA API 2018-06-01**

   Adicione `null` à identidade gerenciada atribuída pelo usuário que você deseja remover:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho do pedido  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Necessário. Definido como `application/json`.        |
   |*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

   **Corpo da solicitação**

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

Se o conjunto de dimensionamento de máquinas virtuais tiver identidades gerenciadas atribuídas ao sistema e ao usuário, você poderá remover todas as identidades gerenciadas atribuídas pelo usuário, alternando para uso somente atribuído pelo sistema usando o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Cabeçalhos de solicitação**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Content-Type*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

**Corpo da solicitação**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Se o conjunto de dimensionamento de máquinas virtuais tiver apenas identidades gerenciadas atribuídas pelo usuário e você quiser removê-las, use o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Cabeçalhos de solicitação**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Content-Type*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido. | 

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
