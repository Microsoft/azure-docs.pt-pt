---
title: Configure modelo para usar identidades geridas em conjuntos de escala de máquina virtual - Azure AD
description: Instruções passo a passo para configurar identidades geridas para os recursos Do Azure num conjunto de escala de máquina virtual, utilizando um modelo de Gestor de Recursos Azure.
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
ms.openlocfilehash: 2d5e324ea20b2ea82fac5b5132893d3558bd3b41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77425566"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Configure identidades geridas para recursos Azure em uma escala de máquina virtual Azure usando um modelo

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código.

Neste artigo, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num conjunto de escala de máquina seleção azul, utilizando o modelo de implementação do Gestor de Recursos Azure:
- Ativar e desativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual Azure
- Adicione e remova uma identidade gerida atribuída pelo utilizador num conjunto de escala de máquina virtual Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gestão neste artigo, a sua conta necessita das seguintes atribuições de controlo de acesso baseadas no papel Azure:

    > [!NOTE]
    > Não são necessárias atribuições adicionais de diretório da AD.

    - [Colaborador de máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de escala de máquina virtual e ativar e remover o sistema e/ou a identidade gerida atribuída pelo utilizador a partir de um conjunto de escala de máquina virtual.
    - [Função de Colaborador de Identidade Gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade gerida atribuída pelo utilizador.
    - [Função do Operador de Identidade Gerido](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade gerida atribuída pelo utilizador de e para um conjunto de escala de máquina virtual.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Tal como acontece com o portal Azure e a escrita, os modelos do Gestor de [Recursos Azure](../../azure-resource-manager/management/overview.md) fornecem a capacidade de implantar recursos novos ou modificados definidos por um grupo de recursos Azure. Várias opções estão disponíveis para edição e implementação de modelos, tanto locais como baseados em portal, incluindo:

   - Utilizando um [modelo personalizado do Azure Marketplace,](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)que lhe permite criar um modelo de raiz, ou baseá-lo num modelo comum ou [de arranque rápido](https://azure.microsoft.com/documentation/templates/)existente.
   - Derivado de um grupo de recursos existente, exportando um modelo da [implantação original,](../../azure-resource-manager/templates/export-template-portal.md)ou do [estado atual da implantação.](../../azure-resource-manager/templates/export-template-portal.md)
   - Utilizando um [editor local da JSON (como o Código VS)](../../azure-resource-manager/resource-manager-create-first-template.md)e, em seguida, carregar e implementar utilizando powerShell ou CLI.
   - Utilizando o projeto Visual Studio [Azure Resource Group](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) para criar e implementar um modelo.  

Independentemente da opção que escolher, a sintaxe do modelo é a mesma durante a implementação e redistribuição iniciais. Permitir identidades geridas para os recursos do Azure num VM novo ou existente é feito da mesma forma. Além disso, por padrão, o Gestor de Recursos Azure faz uma [atualização incremental](../../azure-resource-manager/templates/deployment-modes.md) às implementações.

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, irá ativar e desativar a identidade gerida atribuída pelo sistema utilizando um modelo de Gestor de Recursos Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema durante a criação da criação de um conjunto de escala de máquinas virtuais ou de um conjunto de escala de máquina virtual existente

1. Quer você inscreva-se no Azure localmente ou através do portal Azure, use uma conta que esteja associada à subscrição Azure que contenha o conjunto de escala de máquina virtual.
2. Para ativar a identidade gerida atribuída pelo sistema, carregue o `Microsoft.Compute/virtualMachinesScaleSets` modelo num editor, localize `identity` o recurso de `"type": "Microsoft.Compute/virtualMachinesScaleSets"` interesse dentro da secção de recursos e adicione a propriedade ao mesmo nível da propriedade. Utilize a seguinte sintaxe:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Pode fornecer opcionalmente as identidades geridas para a extensão da escala `extensionProfile` virtual de máquinas de recursos Azure, especificando-as no elemento do modelo. Este passo é opcional, pois pode utilizar o ponto final de identidade do Serviço de Metadados de Instância Supor (IMDS) azure, para recuperar também fichas.  Para mais informações, consulte [Migrate da extensão VM para Azure IMDS para autenticação](howto-migrate-vm-extension.md).


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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desativar uma identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual Azure

Se tiver um conjunto de escala de máquina virtual que já não precisa de uma identidade gerida atribuída pelo sistema:

1. Quer você inscreva-se no Azure localmente ou através do portal Azure, use uma conta que esteja associada à subscrição Azure que contenha o conjunto de escala de máquina virtual.

2. Carregue o modelo [editor](#azure-resource-manager-templates) num editor `Microsoft.Compute/virtualMachineScaleSets` e localize `resources` o recurso de interesse dentro da secção. Se tiver um VM que só tenha uma identidade gerida atribuída ao sistema, `None`pode desativá-la alterando o tipo de identidade para .

   **Microsoft.Compute/virtualMachineScaleSets AVERSÃO versão 2018-06-01**

   Se a sua `2018-06-01` apiVersão for e o seu VM tiver identidades geridas de sistema e de utilização atribuídas ao utilizador, remova `SystemAssigned` do tipo de identidade e mantenha-se `UserAssigned` juntamente com os valores do dicionário Asidentidades atribuídos ao utilizador.

   **Microsoft.Compute/virtualMachineScaleSets AVERSÃO versão 2018-06-01**

   Se o seu `2017-12-01` apiVersion for e o seu conjunto de escala de `SystemAssigned` máquina virtual tiver `UserAssigned` identidades `identityIds` geridas de sistema e de utilização atribuídas ao utilizador, remova do tipo de identidade e mantenha-se juntamente com o conjunto de identidades geridas atribuídas pelo utilizador.



   O exemplo que se segue mostra como remover uma identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual sem identidades geridas atribuídas pelo utilizador:

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

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída ao utilizador

Nesta secção, atribui uma identidade gerida atribuída pelo utilizador a um conjunto de escala de máquina virtual utilizando o modelo do Gestor de Recursos Azure.

> [!Note]
> Para criar uma identidade gerida atribuída ao utilizador utilizando um modelo de gestor de recursos Azure, consulte [Criar uma identidade gerida atribuída](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)ao utilizador .

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual

1. Sob `resources` o elemento, adicione a seguinte entrada para atribuir uma identidade gerida atribuída ao utilizador ao seu conjunto de escala de máquina virtual.  Certifique-se `<USERASSIGNEDIDENTITY>` de substituir pelo nome da identidade gerida atribuída pelo utilizador que criou.

   **Microsoft.Compute/virtualMachineScaleSets AVERSÃO versão 2018-06-01**

   Se a sua `2018-06-01`apiVersão for, as identidades geridas `userAssignedIdentities` atribuídas ao `<USERASSIGNEDIDENTITYNAME>` utilizador são armazenadas no formato `variables` dicionário e o valor deve ser armazenado numa variável definida na secção do seu modelo.

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

   **Microsoft.Compute/virtualMachineScaleSets AVERSÃO versão 2017-12-01**

   Se `apiVersion` for `2017-12-01` ou mais cedo, as identidades geridas atribuídas `identityIds` ao `<USERASSIGNEDIDENTITYNAME>` utilizador são armazenadas na matriz e o valor deve ser armazenado numa variável definida na secção variável do seu modelo.

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
> Pode fornecer opcionalmente as identidades geridas para a extensão da escala `extensionProfile` virtual de máquinas de recursos Azure, especificando-as no elemento do modelo. Este passo é opcional, pois pode utilizar o ponto final de identidade do Serviço de Metadados de Instância Supor (IMDS) azure, para recuperar também fichas.  Para mais informações, consulte [Migrate da extensão VM para Azure IMDS para autenticação](howto-migrate-vm-extension.md).

3. Quando terminar, o seu modelo deve parecer semelhante ao seguinte:

   **Microsoft.Compute/virtualMachineScaleSets AVERSÃO versão 2018-06-01**   

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

   **Microsoft.Compute/virtualMachines AVERSÃO 2017-12-01**

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

Se tiver um conjunto de escala de máquina virtual que já não necessite de uma identidade gerida atribuída ao utilizador:

1. Quer você inscreva-se no Azure localmente ou através do portal Azure, use uma conta que esteja associada à subscrição Azure que contenha o conjunto de escala de máquina virtual.

2. Carregue o modelo [editor](#azure-resource-manager-templates) num editor `Microsoft.Compute/virtualMachineScaleSets` e localize `resources` o recurso de interesse dentro da secção. Se tiver um conjunto de escala de máquina virtual que só tenha uma identidade gerida `None`atribuída pelo utilizador, pode desativá-la alterando o tipo de identidade para .

   O exemplo que se segue mostra como remover todas as identidades geridas atribuídas pelo utilizador de um VM sem identidades geridas atribuídas pelo sistema:

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

   **Microsoft.Compute/virtualMachineScaleSets AVERSÃO versão 2018-06-01**

   Para remover uma única identidade gerida atribuída ao utilizador de um `userAssignedIdentities` conjunto de escala de máquina virtual, remova-a do dicionário.

   Se tiver uma identidade atribuída ao sistema, mantenha-a `type` `identity` no valor abaixo do valor.

   **Microsoft.Compute/virtualMachineScaleSets AVERSÃO versão 2017-12-01**

   Para remover uma única identidade gerida atribuída ao utilizador de um `identityIds` conjunto de escala de máquina virtual, remova-a da matriz.

   Se tiver uma identidade gerida atribuída ao sistema, mantenha-a `type` `identity` no valor abaixo do valor.

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para a visão geral dos recursos do Azure.](overview.md)
