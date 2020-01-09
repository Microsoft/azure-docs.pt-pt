---
title: Configurar o modelo para usar identidades gerenciadas em conjuntos de dimensionamento de máquinas virtuais-Azure AD
description: Instruções passo a passo para a configuração de identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais, usando um modelo de Azure Resource Manager.
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
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5f006832fd1f1386adaf89b0045272a70db2df3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429954"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Configurar identidades gerenciadas para recursos do Azure em uma escala de máquina virtual do Azure usando um modelo

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, você aprende a executar as seguintes identidades gerenciadas para operações de recursos do Azure em um conjunto de dimensionamento de máquinas virtuais do Azure, usando Azure Resource Manager modelo de implantação:
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
    - Função de [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade gerenciada atribuída pelo usuário de e para um conjunto de dimensionamento de máquinas virtuais.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Assim como ocorre com o portal do Azure e o script, os modelos de [Azure Resource Manager](../../azure-resource-manager/management/overview.md) fornecem a capacidade de implantar recursos novos ou modificados definidos por um grupo de recursos do Azure. Várias opções estão disponíveis para edição e implantação de modelos, tanto locais quanto baseadas em portal, incluindo:

   - Usando um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), que permite que você crie um modelo a partir do zero ou baseie-o em um modelo comum ou de [início rápido](https://azure.microsoft.com/documentation/templates/)existente.
   - Derivando de um grupo de recursos existente, exportando um modelo da [implantação original](../../azure-resource-manager/templates/export-template-portal.md)ou do [estado atual da implantação](../../azure-resource-manager/templates/export-template-portal.md).
   - Usando um [Editor de JSON local (como vs Code)](../../azure-resource-manager/resource-manager-create-first-template.md)e, em seguida, carregando e implantando usando o PowerShell ou a CLI.
   - Usando o projeto do [grupo de recursos do Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) do Visual Studio para criar e implantar um modelo.  

Independentemente da opção escolhida, a sintaxe do modelo é a mesma durante a implantação inicial e a reimplantação. A habilitação de identidades gerenciadas para recursos do Azure em uma VM nova ou existente é feita da mesma maneira. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../../azure-resource-manager/deployment-modes.md) para implantações.

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você habilitará e desabilitará a identidade gerenciada atribuída pelo sistema usando um modelo de Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Habilitar a identidade gerenciada atribuída pelo sistema durante a criação da criação de um conjunto de dimensionamento de máquinas virtuais ou um conjunto de dimensionamento de máquinas virtuais existente

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.
2. Para habilitar a identidade gerenciada atribuída pelo sistema, carregue o modelo em um editor, localize o `Microsoft.Compute/virtualMachinesScaleSets` recurso de interesse na seção recursos e adicione a propriedade `identity` no mesmo nível que a propriedade `"type": "Microsoft.Compute/virtualMachinesScaleSets"`. Use a seguinte sintaxe:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Opcionalmente, você pode provisionar as identidades gerenciadas para a extensão do conjunto de dimensionamento de máquinas virtuais do Azure, especificando-o no elemento `extensionProfile` do modelo. Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do IMDS (serviço de metadados de instância) do Azure para recuperar Tokens também.  Para obter mais informações, consulte [migrar da extensão de VM para o Azure IMDS para autenticação](howto-migrate-vm-extension.md).


4. Quando terminar, as seções a seguir devem ser adicionadas à seção de recursos do seu modelo e devem ser semelhantes às seguintes:

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
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desabilitar uma identidade gerenciada atribuída pelo sistema de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais de uma identidade gerenciada atribuída pelo sistema:

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Carregue o modelo em um [Editor](#azure-resource-manager-templates) e localize o `Microsoft.Compute/virtualMachineScaleSets` recurso de interesse na seção `resources`. Se você tiver uma VM que tem apenas identidade gerenciada atribuída pelo sistema, poderá desabilitá-la alterando o tipo de identidade para `None`.

   **API Microsoft. Compute/virtualMachineScaleSets versão 2018-06-01**

   Se o seu apiVersion for `2018-06-01` e sua VM tiver identidades gerenciadas atribuídas ao sistema e ao usuário, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` junto com os valores de dicionário userAssignedIdentities.

   **API Microsoft. Compute/virtualMachineScaleSets versão 2018-06-01**

   Se seu apiVersion for `2017-12-01` e o conjunto de dimensionamento de máquinas virtuais tiver identidades gerenciadas atribuídas ao sistema e ao usuário, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` junto com a matriz `identityIds` das identidades gerenciadas atribuídas pelo usuário. 
   
    

   O exemplo a seguir mostra como remover uma identidade gerenciada atribuída pelo sistema de um conjunto de dimensionamento de máquinas virtuais sem identidades gerenciadas atribuídas pelo usuário:
   
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

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você atribui uma identidade gerenciada atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais usando Azure Resource Manager modelo.

> [!Note]
> Para criar uma identidade gerenciada atribuída pelo usuário usando um modelo de Azure Resource Manager, consulte [criar uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais

1. No elemento `resources`, adicione a seguinte entrada para atribuir uma identidade gerenciada atribuída pelo usuário ao seu conjunto de dimensionamento de máquinas virtuais.  Certifique-se de substituir `<USERASSIGNEDIDENTITY>` pelo nome da identidade gerenciada atribuída pelo usuário que você criou.
   
   **API Microsoft. Compute/virtualMachineScaleSets versão 2018-06-01**

   Se seu apiVersion for `2018-06-01`, suas identidades gerenciadas atribuídas pelo usuário serão armazenadas no formato de dicionário de `userAssignedIdentities` e o valor `<USERASSIGNEDIDENTITYNAME>` deverá ser armazenado em uma variável definida na seção `variables` do seu modelo.

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

   **API Microsoft. Compute/virtualMachineScaleSets versão 2017-12-01**
    
   Se o `apiVersion` for `2017-12-01` ou anterior, suas identidades gerenciadas atribuídas pelo usuário serão armazenadas na matriz de `identityIds` e o valor de `<USERASSIGNEDIDENTITYNAME>` deverá ser armazenado em uma variável definida na seção de variáveis do seu modelo.

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
> [!NOTE]
> Opcionalmente, você pode provisionar as identidades gerenciadas para a extensão do conjunto de dimensionamento de máquinas virtuais do Azure, especificando-o no elemento `extensionProfile` do modelo. Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do IMDS (serviço de metadados de instância) do Azure para recuperar Tokens também.  Para obter mais informações, consulte [migrar da extensão de VM para o Azure IMDS para autenticação](howto-migrate-vm-extension.md).

3. Quando terminar, o modelo deverá ser semelhante ao seguinte:
   
   **API Microsoft. Compute/virtualMachineScaleSets versão 2018-06-01**   

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
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
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

   **API Microsoft. Compute/virtualMachines versão 2017-12-01**

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
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
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
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remover identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais de uma identidade gerenciada atribuída pelo usuário:

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Carregue o modelo em um [Editor](#azure-resource-manager-templates) e localize o `Microsoft.Compute/virtualMachineScaleSets` recurso de interesse na seção `resources`. Se você tiver um conjunto de dimensionamento de máquinas virtuais que tenha apenas identidade gerenciada atribuída pelo usuário, você poderá desabilitá-lo alterando o tipo de identidade para `None`.

   O exemplo a seguir mostra como remover todas as identidades gerenciadas atribuídas pelo usuário de uma VM sem identidades gerenciadas atribuídas pelo sistema:

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
   
   **API Microsoft. Compute/virtualMachineScaleSets versão 2018-06-01**
    
   Para remover uma única identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais, remova-a do dicionário de `userAssignedIdentities`.

   Se você tiver uma identidade atribuída pelo sistema, mantenha-a no `type` valor abaixo do valor `identity`.

   **API Microsoft. Compute/virtualMachineScaleSets versão 2017-12-01**

   Para remover uma única identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais, remova-a da matriz de `identityIds`.

   Se você tiver uma identidade gerenciada atribuída pelo sistema, mantenha-a no `type` valor no valor `identity`.
   
## <a name="next-steps"></a>Passos seguintes

- [Identidades gerenciadas para visão geral dos recursos do Azure](overview.md).

