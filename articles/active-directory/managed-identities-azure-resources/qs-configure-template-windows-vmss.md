---
title: Modelo de configuração para usar identidades geridas em conjuntos de escala de máquina virtual - Azure AD
description: Instruções passo a passo para configurar identidades geridas para recursos Azure em um conjunto de escala de máquina virtual, usando um modelo de Gestor de Recursos Azure.
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
ms.openlocfilehash: 377bbb9ce111f3cf2daf8426e128186711c30e5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587456"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Configurar identidades geridas para recursos Azure em uma escala de máquina virtual Azure usando um modelo

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código.

Neste artigo, aprende a executar as seguintes identidades geridas para operações de recursos Azure num conjunto de escala de máquina virtual Azure, utilizando o modelo de implementação do Gestor de Recursos Azure:
- Ativar e desativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual Azure
- Adicione e remova uma identidade gerida atribuída pelo utilizador num conjunto de escala de máquina virtual Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gestão neste artigo, a sua conta necessita das seguintes atribuições de controlo de acesso baseados em funções Azure:

    > [!NOTE]
    > Não são necessárias atribuições adicionais de diretório ad AD.

    - [Contribuinte de máquinas virtuais](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para criar um conjunto de escala de máquina virtual e ativar e remover sistema e/ou identidade gerida atribuída pelo utilizador a partir de um conjunto de escala de máquina virtual.
    - [Papel de Contribuinte de Identidade Gerido](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) para criar uma identidade gerida atribuída pelo utilizador.
    - [Função de Operador de Identidade Gerida](../../role-based-access-control/built-in-roles.md#managed-identity-operator) para atribuir e remover uma identidade gerida atribuída pelo utilizador de e para um conjunto de escala de máquina virtual.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Tal como acontece com o portal Estruição e scripts [Azure,](../../azure-resource-manager/management/overview.md) os modelos do Azure Resource Manager fornecem a capacidade de implantar recursos novos ou modificados definidos por um grupo de recursos Azure. Várias opções estão disponíveis para edição e implementação de modelos, tanto locais como baseados em portal, incluindo:

   - Utilizando um [modelo personalizado do Azure Marketplace,](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)que permite criar um modelo de raiz, ou baseá-lo num modelo comum ou [de arranque rápido](https://azure.microsoft.com/documentation/templates/)existente.
   - Derivado de um grupo de recursos existente, exportando um modelo a partir da [implantação original,](../../azure-resource-manager/templates/export-template-portal.md)ou do [estado atual da implantação](../../azure-resource-manager/templates/export-template-portal.md).
   - Utilizando um editor JSON local [(como o VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)e, em seguida, fazer upload e implantação utilizando o PowerShell ou o CLI.
   - Utilizando o projeto Visual Studio [Azure Resource Group](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) para criar e implementar um modelo.  

Independentemente da opção escolhida, a sintaxe do modelo é a mesma durante a implementação inicial e a redistribuição. Permitir identidades geridas para recursos Azure num VM novo ou existente é feito da mesma forma. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../../azure-resource-manager/templates/deployment-modes.md) às implementações.

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, irá ativar e desativar a identidade gerida atribuída pelo sistema utilizando um modelo de Gestor de Recursos Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema durante a criação da criação de um conjunto de escala de máquinas virtuais ou de um conjunto de escala de máquina virtual existente

1. Quer faça seduca localmente ou através do portal Azure, utilize uma conta associada à subscrição Azure que contenha o conjunto de escala de máquina virtual.
2. Para ativar a identidade gerida atribuída pelo sistema, carregue o modelo num editor, localize o `Microsoft.Compute/virtualMachinesScaleSets` recurso de interesse dentro da secção de recursos e adicione a propriedade ao mesmo `identity` nível que a `"type": "Microsoft.Compute/virtualMachinesScaleSets"` propriedade. Utilize a seguinte sintaxe:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Pode, opcionalmente, prever as identidades geridas para a escala de máquina virtual de recursos Azure, especificando-as no `extensionProfile` elemento do modelo. Este passo é opcional, uma vez que pode utilizar o ponto final de identidade do Serviço de Metadados de Caso Azure (IMDS), para recuperar também fichas.  Para obter mais informações, consulte [Migrar da extensão VM para Azure IMDS para autenticação.](howto-migrate-vm-extension.md)


4. Quando terminar, as seguintes secções devem ser adicionadas à secção de recursos do seu modelo e devem assemelhar-se às seguintes:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desative uma identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual Azure

Se tiver um conjunto de escala de máquina virtual que já não necessita de uma identidade gerida atribuída pelo sistema:

1. Quer faça seduca localmente ou através do portal Azure, utilize uma conta associada à subscrição Azure que contenha o conjunto de escala de máquina virtual.

2. Carregue o modelo num [editor](#azure-resource-manager-templates) e localize o `Microsoft.Compute/virtualMachineScaleSets` recurso de interesse dentro da `resources` secção. Se tiver um VM que só tenha identidade gerida atribuída ao sistema, pode desativá-la alterando o tipo de identidade para `None` .

   **Microsoft.Compute/virtualMachineScaleSets versão API 2018-06-01**

   Se a sua apiversão for `2018-06-01` e o seu VM tiver identidades geridas tanto no sistema como no utilizador, remova `SystemAssigned` do tipo de identidade e `UserAssigned` mantenha-se juntamente com os valores do dicionário de nomeações do utilizador.

   **Microsoft.Compute/virtualMachineScaleSets versão API 2018-06-01**

   Se a sua apiversão for `2017-12-01` e o seu conjunto de escala de máquina virtual tiver identidades geridas do sistema e atribuídas pelo utilizador, remova `SystemAssigned` do tipo de identidade e `UserAssigned` mantenha-se juntamente com a `identityIds` matriz das identidades geridas atribuídas pelo utilizador.



   O exemplo a seguir mostra como remover uma identidade gerida atribuída pelo sistema a partir de uma escala de máquina virtual definida sem identidades geridas atribuídas pelo utilizador:

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

Nesta secção, atribua uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual utilizando o modelo Azure Resource Manager.

> [!Note]
> Para criar uma identidade gerida atribuída ao utilizador utilizando um Modelo de Gestor de Recursos Azure, consulte [Criar uma identidade gerida atribuída pelo utilizador](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Atribua uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual

1. Sob o `resources` elemento, adicione a seguinte entrada para atribuir uma identidade gerida atribuída pelo utilizador ao seu conjunto de escala de máquina virtual.  Certifique-se de que substitui `<USERASSIGNEDIDENTITY>` pelo nome da identidade gerida atribuída ao utilizador que criou.

   **Microsoft.Compute/virtualMachineScaleSets versão API 2018-06-01**

   Se a sua apiversão `2018-06-01` for, as suas identidades geridas atribuídas ao utilizador são armazenadas no `userAssignedIdentities` formato dicionário e o valor deve ser armazenado numa `<USERASSIGNEDIDENTITYNAME>` variável definida na secção do seu `variables` modelo.

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

   **Microsoft.Compute/virtualMachineScaleSets versão API 2017-12-01**

   Se o seu `apiVersion` é `2017-12-01` ou mais cedo, as suas identidades geridas atribuídas ao utilizador são armazenadas na `identityIds` matriz e o valor deve ser armazenado numa `<USERASSIGNEDIDENTITYNAME>` variável definida na secção de variáveis do seu modelo.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ```
> [!NOTE]
> Pode, opcionalmente, prever as identidades geridas para a escala de máquina virtual de recursos Azure, especificando-as no `extensionProfile` elemento do modelo. Este passo é opcional, uma vez que pode utilizar o ponto final de identidade do Serviço de Metadados de Caso Azure (IMDS), para recuperar também fichas.  Para obter mais informações, consulte [Migrar da extensão VM para Azure IMDS para autenticação.](howto-migrate-vm-extension.md)

3. Quando terminar, o seu modelo deve ser semelhante ao seguinte:

   **Microsoft.Compute/virtualMachineScaleSets versão API 2018-06-01**   

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

   **Microsoft.Compute/virtualMachines Versão API 2017-12-01**

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
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remova a identidade gerida atribuída pelo utilizador de um conjunto de escala de máquina virtual Azure

Se tiver um conjunto de escala de máquina virtual que já não necessita de uma identidade gerida atribuída pelo utilizador:

1. Quer faça seduca localmente ou através do portal Azure, utilize uma conta associada à subscrição Azure que contenha o conjunto de escala de máquina virtual.

2. Carregue o modelo num [editor](#azure-resource-manager-templates) e localize o `Microsoft.Compute/virtualMachineScaleSets` recurso de interesse dentro da `resources` secção. Se tiver um conjunto de escala de máquina virtual que só tenha identidade gerida atribuída ao utilizador, pode desativá-la alterando o tipo de identidade para `None` .

   O exemplo a seguir mostra como remover todas as identidades geridas atribuídas pelo utilizador a partir de um VM sem identidades geridas atribuídas pelo sistema:

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

   **Microsoft.Compute/virtualMachineScaleSets versão API 2018-06-01**

   Para remover uma única identidade gerida atribuída pelo utilizador de um conjunto de escala de máquina virtual, remova-a do `userAssignedIdentities` dicionário.

   Se tiver uma identidade atribuída ao sistema, mantenha-a no `type` valor abaixo do `identity` valor.

   **Microsoft.Compute/virtualMachineScaleSets versão API 2017-12-01**

   Para remover uma única identidade gerida atribuída pelo utilizador de um conjunto de escala de máquina virtual, remova-a da `identityIds` matriz.

   Se tiver uma identidade gerida atribuída ao sistema, mantenha-a no `type` valor abaixo do `identity` valor.

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para visão geral dos recursos Azure](overview.md).