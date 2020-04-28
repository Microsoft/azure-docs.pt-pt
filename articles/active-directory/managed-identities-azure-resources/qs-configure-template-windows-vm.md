---
title: Configure identidades geridas em Azure VM usando modelo - Azure AD
description: Instruções passo a passo para configurar identidades geridas para os recursos Do Azure num VM Azure, utilizando um modelo de Gestor de Recursos Azure.
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
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5540697e8e64586d73e34d253fb95e549fc0301
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75972154"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Configure identidades geridas para recursos Azure em um VM Azure usando um modelo

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código.

Neste artigo, utilizando o modelo de implantação do Gestor de Recursos Azure, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num Azure VM:

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com a utilização do modelo de implementação do Gestor de Recursos Azure, consulte a [secção de visão geral](overview.md). **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Tal como acontece com o portal Azure e a escrita, os modelos do Gestor de [Recursos Azure](../../azure-resource-manager/management/overview.md) fornecem a capacidade de implantar recursos novos ou modificados definidos por um grupo de recursos Azure. Várias opções estão disponíveis para edição e implementação de modelos, tanto locais como baseados em portal, incluindo:

   - Utilizando um [modelo personalizado do Azure Marketplace,](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)que lhe permite criar um modelo de raiz, ou baseá-lo num modelo comum ou [de arranque rápido](https://azure.microsoft.com/documentation/templates/)existente.
   - Derivado de um grupo de recursos existente, exportando um modelo da [implantação original,](../../azure-resource-manager/templates/export-template-portal.md)ou do [estado atual da implantação.](../../azure-resource-manager/templates/export-template-portal.md)
   - Utilizando um [editor local da JSON (como o Código VS)](../../azure-resource-manager/resource-manager-create-first-template.md)e, em seguida, carregar e implementar utilizando powerShell ou CLI.
   - Utilizando o projeto Visual Studio [Azure Resource Group](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) para criar e implementar um modelo.  

Independentemente da opção que escolher, a sintaxe do modelo é a mesma durante a implementação e redistribuição iniciais. Permitir um sistema ou identidade gerida atribuída pelo utilizador num VM novo ou existente é feito da mesma forma. Além disso, por padrão, o Gestor de Recursos Azure faz uma [atualização incremental](../../azure-resource-manager/templates/deployment-modes.md) às implementações.

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, irá ativar e desativar uma identidade gerida atribuída pelo sistema utilizando um modelo de Gestor de Recursos Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Permitir a identidade gerida atribuída pelo sistema durante a criação de um VM Azure ou num VM existente

Para permitir a identidade gerida atribuída pelo sistema num VM, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

1. Quer você assine o Azure localmente ou através do portal Azure, use uma conta que esteja associada à subscrição Azure que contém o VM.

2. Para ativar a identidade gerida atribuída pelo sistema, carregue `Microsoft.Compute/virtualMachines` o modelo num `resources` editor, `"identity"` localize o recurso `"type": "Microsoft.Compute/virtualMachines"` de interesse dentro da secção e adicione a propriedade ao mesmo nível da propriedade. Utilize a seguinte sintaxe:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Quando terminar, as seguintes secções devem `resource` ser adicionadas à secção do seu modelo e devem assemelhar-se às seguintes:

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                },
            },

            //The following appears only if you provisioned the optional VM extension (to be deprecated)
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
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
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Atribuir um papel à identidade gerida atribuída pelo VM

Depois de ter ativado a identidade gerida atribuída pelo sistema no seu VM, pode querer conceder-lhe um papel como o acesso do **Leitor** ao grupo de recursos em que foi criado.

Para atribuir uma função à identidade atribuída ao sistema do seu VM, a sua conta necessita da atribuição da função de Administrador de Acesso ao [Utilizador.](/azure/role-based-access-control/built-in-roles#user-access-administrator)

1. Quer você assine o Azure localmente ou através do portal Azure, use uma conta que esteja associada à subscrição Azure que contém o VM.

2. Carregue o modelo num [editor](#azure-resource-manager-templates) e adicione as seguintes informações para dar ao seu **Leitor** VM acesso ao grupo de recursos em que foi criado.  A estrutura do modelo pode variar consoante o editor e o modelo de implementação que escolher.

   Na `parameters` secção, adicione o seguinte:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    Na `variables` secção, adicione o seguinte:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Na `resources` secção, adicione o seguinte:

    ```JSON
    {
        "apiVersion": "2017-09-01",
        "type": "Microsoft.Authorization/roleAssignments",
        "name": "[parameters('rbacGuid')]",
        "properties": {
            "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
            "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
            "scope": "[resourceGroup().id]"
        },
         "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
        ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Desativar uma identidade gerida atribuída pelo sistema a partir de um VM Azure

Para remover a identidade gerida atribuída pelo sistema a partir de um VM, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

1. Quer você assine o Azure localmente ou através do portal Azure, use uma conta que esteja associada à subscrição Azure que contém o VM.

2. Carregue o modelo [editor](#azure-resource-manager-templates) num editor `Microsoft.Compute/virtualMachines` e localize `resources` o recurso de interesse dentro da secção. Se tiver um VM que só tenha uma identidade gerida atribuída ao sistema, `None`pode desativá-la alterando o tipo de identidade para .  

   **Microsoft.Compute/virtualMachines AVERSÃO 2018-06-01**

   Se o seu VM tiver identidades geridas pelo `SystemAssigned` sistema e pelo `UserAssigned` utilizador, `userAssignedIdentities` remova do tipo de identidade e mantenha-se acompanhado dos valores do dicionário.

   **Microsoft.Compute/virtualMachines AVERSÃO 2018-06-01**

   Se `apiVersion` o `2017-12-01` seu é e o seu VM tiver identidades `SystemAssigned` geridas pelo `UserAssigned` sistema e `identityIds` pelo utilizador, remova do tipo de identidade e mantenha-se juntamente com o conjunto de identidades geridas atribuídas pelo utilizador.  

O exemplo que se segue mostra como remover uma identidade gerida atribuída pelo sistema a partir de um VM sem identidades geridas atribuídas pelo utilizador:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída ao utilizador

Nesta secção, atribui uma identidade gerida atribuída ao utilizador a um Azure VM utilizando o modelo de Gestor de Recursos Azure.

> [!Note]
> Para criar uma identidade gerida atribuída ao utilizador utilizando um modelo de gestor de recursos Azure, consulte [Criar uma identidade gerida atribuída](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)ao utilizador .

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador a um VM Azure

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções de Colaborador de [Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e Operador de [Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Não são necessárias atribuições adicionais de diretório da AD.

1. Sob `resources` o elemento, adicione a seguinte entrada para atribuir uma identidade gerida atribuída ao utilizador ao seu VM.  Certifique-se `<USERASSIGNEDIDENTITY>` de substituir pelo nome da identidade gerida atribuída pelo utilizador que criou.

   **Microsoft.Compute/virtualMachines AVERSÃO 2018-06-01**

   Se `apiVersion` estiver, `2018-06-01`as identidades geridas atribuídas ao `userAssignedIdentities` utilizador são armazenadas no formato dicionário e o `<USERASSIGNEDIDENTITYNAME>` valor deve ser armazenado numa variável definida na `variables` secção do seu modelo.

   ```JSON
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```

   **Microsoft.Compute/virtualMachines AVERSÃO 2017-12-01**

   Se `apiVersion` estiver, `2017-12-01`as identidades geridas atribuídas ao `identityIds` utilizador são `<USERASSIGNEDIDENTITYNAME>` armazenadas na matriz e `variables` o valor deve ser armazenado numa variável definida na secção do seu modelo.

   ```JSON
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```

3. Quando terminar, as seguintes secções devem `resource` ser adicionadas à secção do seu modelo e devem assemelhar-se às seguintes:

   **Microsoft.Compute/virtualMachines AVERSÃO 2018-06-01**    

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        },
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                  
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
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
   ```
   **Microsoft.Compute/virtualMachines AVERSÃO 2017-12-01**

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        },

        //The following appears only if you provisioned the optional VM extension (to be deprecated)                   
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
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
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remova uma identidade gerida atribuída ao utilizador de um VM Azure

Para remover uma identidade atribuída ao utilizador de um VM, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) Não são necessárias atribuições adicionais de diretório da AD.

1. Quer você assine o Azure localmente ou através do portal Azure, use uma conta que esteja associada à subscrição Azure que contém o VM.

2. Carregue o modelo [editor](#azure-resource-manager-templates) num editor `Microsoft.Compute/virtualMachines` e localize `resources` o recurso de interesse dentro da secção. Se tiver um VM que só tenha identidade gerida atribuída ao utilizador, pode `None`desativá-lo alterando o tipo de identidade para .

   O exemplo que se segue mostra como remover todas as identidades geridas atribuídas pelo utilizador de um VM sem identidades geridas atribuídas pelo sistema:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Microsoft.Compute/virtualMachines AVERSÃO 2018-06-01**

   Para remover uma única identidade gerida atribuída ao utilizador de `useraAssignedIdentities` um VM, remova-a do dicionário.

   Se tiver uma identidade gerida atribuída ao sistema, mantenha-a `type` `identity` no valor abaixo do valor.

   **Microsoft.Compute/virtualMachines AVERSÃO 2017-12-01**

   Para remover uma única identidade gerida atribuída ao utilizador de `identityIds` um VM, remova-a da matriz.

   Se tiver uma identidade gerida atribuída ao sistema, mantenha-a `type` `identity` no valor abaixo do valor.

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para a visão geral dos recursos do Azure.](overview.md)
