---
title: Configurar identidades geridas para recursos do Azure num conjunto de dimensionamento com um modelo
description: Conjunto de instruções passo a passo para configurar identidades geridas para recursos do Azure em dimensionamento de máquinas virtuais, com um modelo Azure Resource Manager.
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
ms.date: 02/20/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 075672fb6d132258b04936aa20129fa6f8c82572
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819245"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-azure-virtual-machine-scale-using-a-template"></a>Configurar identidades geridas para recursos do Azure numa escala de máquina virtual do Azure através de um modelo

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, aprenderá a efetuar as seguintes identidades geridas para operações de recursos do Azure num conjunto de dimensionamento de máquina virtual do Azure, usando o modelo de implementação Azure Resource Manager:
- Ativar e desativar a identidade gerida atribuído de sistema num conjunto de dimensionamento de máquina virtual do Azure
- Adicionar e remover uma identidade gerida atribuído ao utilizador num conjunto de dimensionamento de máquina virtual do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para efetuar as operações de gestão neste artigo, a conta tem das atribuições de controlo de acesso baseado em funções do Azure seguintes:

    > [!NOTE]
    > Nenhum adicionais do Azure AD directory as atribuições de funções necessárias.

    - [Contribuinte de máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e ativar e remover o sistema e/ou atribuído ao utilizador a identidade gerida a partir de um conjunto de dimensionamento de máquina virtual.
    - [Contribuidor de identidade de geridos](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) identidade gerida de função para criar um atribuído ao utilizador.
    - [Gerido operador de identidade](/azure/role-based-access-control/built-in-roles#managed-identity-operator) função para atribuir e remover um atribuído ao utilizador identidade gerida de e para um conjunto de dimensionamento de máquina virtual.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Tal como acontece com o portal do Azure e criação de scripts, [do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) os modelos fornecem a capacidade de implementar recursos novos ou modificados definidos por um grupo de recursos do Azure. Várias opções estão disponíveis para edição de modelo e a implantação, local e baseado no portal, incluindo:

   - Utilizar um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite-lhe criar um modelo a partir do zero ou baseá-la num comum existente ou [modelo de início rápido](https://azure.microsoft.com/documentation/templates/).
   - Derivar de um grupo de recursos existente ao exportar um modelo a partir de qualquer uma [implementação original](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates), ou a partir do [estado atual da implantação](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
   - Com um local [editor de JSON (por exemplo, o VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)e, em seguida, carregar e implementar com o PowerShell ou a CLI.
   - Com o Visual Studio [projeto do grupo de recursos do Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) para criar e implementar um modelo.  

Independentemente da opção escolhida, sintaxe do modelo é o mesmo durante a implementação inicial e a reimplementação. Ativar identidades geridas para recursos do Azure numa VM nova ou existente é feito da mesma forma. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../../azure-resource-manager/deployment-modes.md) para implementações.

## <a name="system-assigned-managed-identity"></a>Atribuído ao sistema de identidade gerida

Nesta secção, irá ativar e desativar a identidade gerida atribuído ao sistema através de um modelo Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-a-existing-virtual-machine-scale-set"></a>Ativar atribuído de sistema de identidade gerido durante a criação a criação de um conjunto de dimensionamento de máquinas virtuais ou um conjunto de dimensionamento de máquina virtual existente

1. Se iniciar sessão para o Azure localmente ou através do portal do Azure, utilize uma conta que está associada à subscrição do Azure que contém o conjunto de dimensionamento de máquina virtual.
2. Para ativar a identidade gerida atribuído de sistema, carregar o modelo para um editor, localize a `Microsoft.Compute/virtualMachinesScaleSets` recurso de interesse nos recursos secção e adicione o `identity` propriedade no mesmo nível, como o `"type": "Microsoft.Compute/virtualMachinesScaleSets"` propriedade. Utilize a seguinte sintaxe:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

3. (Opcional) Adicionar conjunto de dimensionamento da máquina virtual de identidades geridas para a extensão de recursos do Azure como um `extensionsProfile` elemento. Este passo é opcional, como pode usar a identidade de serviço de metadados de instância do Azure (IMDS), para obtenção de tokens também.  Utilize a seguinte sintaxe:

   >[!NOTE] 
   > O exemplo a seguir supõe a extensão do conjunto de dimensionamento de máquinas virtuais do Windows (`ManagedIdentityExtensionForWindows`) está a ser implementado. Também pode configurar para Linux, utilizando `ManagedIdentityExtensionForLinux` em vez disso, para o `"name"` e `"type"` elementos.
   >

   ```json
   "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
   ```

4. Quando tiver terminado, as seções a seguir devem ser adicionados para a secção de recursos do seu modelo e devem assemelhar-se ao seguinte:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            } 
                        ]
                    }
                }
            }
        }
    ]
   ``` 

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desativar uma identidade gerida atribuído de sistema de um conjunto de dimensionamento de máquina virtual do Azure

Se tiver um conjunto de dimensionamento de máquina virtual que já não necessita de uma identidade gerida atribuído de sistema:

1. Se iniciar sessão para o Azure localmente ou através do portal do Azure, utilize uma conta que está associada à subscrição do Azure que contém o conjunto de dimensionamento de máquina virtual.

2. Carregar o modelo para um [editor](#azure-resource-manager-templates) e localize a `Microsoft.Compute/virtualMachineScaleSets` recursos de interesse no `resources` secção. Se tiver uma VM que só tenha atribuído ao sistema de identidade gerida, pode desativá-lo ao alterar o tipo de identidade para `None`.

   **Compute/virtualmachinescalesets 2018-01 06 de versão de API**

   Se estiver a apiVersion `2018-06-01` e a VM tem o sistema e as identidades geridas atribuído ao utilizador, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` juntamente com os valores de dicionário userAssignedIdentities.

   **Compute/virtualmachinescalesets 2018-01 06 de versão de API**

   Se estiver a apiVersion `2017-12-01` e o conjunto de dimensionamento de máquina virtual tem o sistema e as identidades geridas atribuído ao utilizador, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` juntamente com o `identityIds` matriz do utilizador atribuído geridos identidades. 
   
    

   O exemplo seguinte mostra como remover uma identidade gerida atribuído de sistema de um conjunto de dimensionamento com não gerenciados identidiades atribuídas:
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, atribua uma identidade gerida atribuído ao utilizador a um conjunto de dimensionamento com o modelo Azure Resource Manager.

> [!Note]
> Para criar uma identidade gerida atribuído ao utilizador com um modelo de Gestor de recursos do Azure, veja [criar uma identidade gerida atribuído ao utilizador](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuído ao utilizador a um conjunto de dimensionamento de máquinas virtuais

1. Sob o `resources` elemento, adicione a seguinte entrada para atribuir uma identidade gerida atribuído ao utilizador para o conjunto de dimensionamento de máquina virtual.  Certifique-se de que substitua `<USERASSIGNEDIDENTITY>` com o nome do atribuído ao utilizador que criou a identidade de gerido.
   
   **Compute/virtualmachinescalesets 2018-01 06 de versão de API**

   Se estiver a apiVersion `2018-06-01`, suas identidades geridas atribuído ao utilizador são armazenadas no `userAssignedIdentities` formato do dicionário e o `<USERASSIGNEDIDENTITYNAME>` valor deve ser armazenado numa variável definida no `variables` secção do seu modelo.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }
    
   }
   ```   

   **Compute/virtualmachinescalesets versão 2017-12-01 de API**
    
   Se sua `apiVersion` é `2017-12-01` ou anteriormente, as suas identidades geridas atribuído ao utilizador são armazenadas no `identityIds` matriz e o `<USERASSIGNEDIDENTITYNAME>` valor deve ser armazenado numa variável definida na secção de variáveis do seu modelo.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ``` 

2. (Opcional) Adicione a entrada seguinte sob o `extensionProfile` elemento para atribuir as identidades geridas para a extensão de recursos do Azure para o VMSS. Este passo é opcional, como pode usar o ponto de extremidade para a identidade de serviço de metadados de instância do Azure (IMDS), para obtenção de tokens também. Utilize a seguinte sintaxe:
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "ManagedIdentityWindowsExtension",
                    "properties": {
                        "publisher": "Microsoft.ManagedIdentity",
                        "type": "ManagedIdentityExtensionForWindows",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "port": 50342
                        },
                        "protectedSettings": {}
                    }
                }
    ```

3. Quando tiver terminado, o modelo deve ter um aspeto semelhante ao seguinte:
   
   **Compute/virtualmachinescalesets 2018-01 06 de versão de API**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            } 
                        ]
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines versão 2017-12-01 de API**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            } 
                        ]
                    }
                }
            }
        }
    ]
   ```
### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remover a identidade gerida atribuído ao utilizador um conjunto de dimensionamento de máquina virtual do Azure

Se tiver um conjunto de dimensionamento de máquina virtual que já não necessita de uma identidade gerida atribuído ao utilizador:

1. Se iniciar sessão para o Azure localmente ou através do portal do Azure, utilize uma conta que está associada à subscrição do Azure que contém o conjunto de dimensionamento de máquina virtual.

2. Carregar o modelo para um [editor](#azure-resource-manager-templates) e localize a `Microsoft.Compute/virtualMachineScaleSets` recursos de interesse no `resources` secção. Se tiver um conjunto de dimensionamento de máquinas virtuais que só tenha atribuído ao utilizador a identidade gerida, pode desativá-lo ao alterar o tipo de identidade para `None`.

   O exemplo seguinte mostra como remover todas as identidades geridas atribuído ao utilizador a partir de uma VM com nenhuma identidades geridas atribuído de sistema:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```
   
   **Compute/virtualmachinescalesets 2018-01 06 de versão de API**
    
   Para remover uma única identidade gerida atribuído ao utilizador a partir de um conjunto de dimensionamento de máquina virtual, remova-a do `userAssignedIdentities` dicionário.

   Se tiver uma identidade atribuída de sistema, mantenha-a no no `type` valor sob a `identity` valor.

   **Compute/virtualmachinescalesets versão 2017-12-01 de API**

   Para remover uma única identidade gerida atribuído ao utilizador a partir de um conjunto de dimensionamento de máquina virtual, remova-a do `identityIds` matriz.

   Se tiver uma identidade gerida atribuído de sistema, mantenha-a no no `type` valor sob a `identity` valor.
   
## <a name="next-steps"></a>Passos Seguintes

- [Gerido identidades para descrição geral de recursos do Azure](overview.md).

