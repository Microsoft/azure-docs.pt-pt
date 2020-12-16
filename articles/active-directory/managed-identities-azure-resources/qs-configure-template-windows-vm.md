---
title: Configurar identidades geridas no Azure VM usando o modelo - Azure AD
description: Instruções passo a passo para configurar identidades geridas para recursos Azure num VM Azure, utilizando um modelo de Gestor de Recursos Azure.
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
ms.openlocfilehash: 960e1fc19567b03024000e84217b3846f89f94f3
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588561"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-templates"></a>Configurar identidades geridas para recursos Azure num VM Azure usando modelos

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código.

Neste artigo, utilizando o modelo de implementação do Gestor de Recursos Azure, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num VM Azure:

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com a utilização do modelo de implementação do Gestor de Recursos Azure, consulte a [secção de visão geral](overview.md). **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Tal como acontece com o portal Estruição e scripts [Azure,](../../azure-resource-manager/management/overview.md) os modelos do Azure Resource Manager fornecem a capacidade de implantar recursos novos ou modificados definidos por um grupo de recursos Azure. Várias opções estão disponíveis para edição e implementação de modelos, tanto locais como baseados em portal, incluindo:

   - Utilizando um [modelo personalizado do Azure Marketplace,](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)que permite criar um modelo de raiz, ou baseá-lo num modelo comum ou [de arranque rápido](https://azure.microsoft.com/documentation/templates/)existente.
   - Derivado de um grupo de recursos existente, exportando um modelo a partir da [implantação original,](../../azure-resource-manager/templates/export-template-portal.md)ou do [estado atual da implantação](../../azure-resource-manager/templates/export-template-portal.md).
   - Utilizando um editor JSON local [(como o VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)e, em seguida, fazer upload e implantação utilizando o PowerShell ou o CLI.
   - Utilizando o projeto Visual Studio [Azure Resource Group](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) para criar e implementar um modelo.  

Independentemente da opção escolhida, a sintaxe do modelo é a mesma durante a implementação inicial e a redistribuição. Permitir um sistema ou identidade gerida atribuída pelo utilizador num VM novo ou existente é feito da mesma forma. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../../azure-resource-manager/templates/deployment-modes.md) às implementações.

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, irá ativar e desativar uma identidade gerida atribuída pelo sistema utilizando um modelo de Gestor de Recursos Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Permitir a identidade gerida atribuída pelo sistema durante a criação de um VM Azure ou num VM existente

Para ativar a identidade gerida atribuída pelo sistema num VM, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Quer faça seduca localmente ou através do portal Azure, utilize uma conta associada à subscrição Azure que contenha o VM.

2. Para ativar a identidade gerida atribuída pelo sistema, carregue o modelo num editor, localize o `Microsoft.Compute/virtualMachines` recurso de interesse dentro da secção e adicione a propriedade ao mesmo `resources` `"identity"` nível que a `"type": "Microsoft.Compute/virtualMachines"` propriedade. Utilize a seguinte sintaxe:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Quando terminar, as seguintes secções devem ser adicionadas à `resource` secção do seu modelo e devem assemelhar-se às seguintes:

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

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Atribuir uma função a identidade gerida atribuída pelo sistema do VM

Depois de ter ativado a identidade gerida atribuída pelo sistema no seu VM, talvez queira conceder-lhe um papel como o acesso do **Reader** ao grupo de recursos em que foi criado.

Para atribuir uma função à identidade atribuída ao sistema do seu VM, a sua conta necessita da atribuição de função de Administrador de Acesso ao [Utilizador.](../../role-based-access-control/built-in-roles.md#user-access-administrator)

1. Quer faça seduca localmente ou através do portal Azure, utilize uma conta associada à subscrição Azure que contenha o VM.

2. Carregue o modelo num [editor](#azure-resource-manager-templates) e adicione as seguintes informações para dar ao seu VM **Reader** acesso ao grupo de recursos em que foi criado.  A estrutura do modelo pode variar dependendo do editor e do modelo de implementação que escolher.

   Na `parameters` secção acrescentamos o seguinte:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    Na `variables` secção acrescentamos o seguinte:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Na `resources` secção acrescentamos o seguinte:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Desative uma identidade gerida atribuída pelo sistema a partir de um VM Azure

Para remover a identidade gerida atribuída pelo sistema a partir de um VM, a sua conta precisa da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Quer faça seduca localmente ou através do portal Azure, utilize uma conta associada à subscrição Azure que contenha o VM.

2. Carregue o modelo num [editor](#azure-resource-manager-templates) e localize o `Microsoft.Compute/virtualMachines` recurso de interesse dentro da `resources` secção. Se tiver um VM que só tenha identidade gerida atribuída ao sistema, pode desativá-la alterando o tipo de identidade para `None` .  

   **Microsoft.Compute/virtualMachines Versão API 2018-06-01**

   Se o seu VM tiver identidades geridas do sistema e atribuídas pelo utilizador, remova `SystemAssigned` do tipo de identidade e `UserAssigned` mantenha-se juntamente com os `userAssignedIdentities` valores do dicionário.

   **Microsoft.Compute/virtualMachines Versão API 2018-06-01**

   Se o seu `apiVersion` S E o seu `2017-12-01` VM tiver identidades geridas do sistema e atribuídas pelo utilizador, remova `SystemAssigned` do tipo de identidade e `UserAssigned` mantenha-se juntamente com o `identityIds` conjunto das identidades geridas atribuídas pelo utilizador.  

O exemplo a seguir mostra como remover uma identidade gerida atribuída ao sistema de um VM sem identidades geridas atribuídas pelo utilizador:

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

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, atribua uma identidade gerida atribuída ao utilizador a um VM Azure utilizando o modelo Azure Resource Manager.

> [!Note]
> Para criar uma identidade gerida atribuída ao utilizador utilizando um Modelo de Gestor de Recursos Azure, consulte [Criar uma identidade gerida atribuída pelo utilizador](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador a um VM Azure

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções [de Colaborador de Máquinas Virtuais](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e Operador de Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-operator) Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Sob o `resources` elemento, adicione a seguinte entrada para atribuir uma identidade gerida atribuída ao seu VM.  Certifique-se de que substitui `<USERASSIGNEDIDENTITY>` pelo nome da identidade gerida atribuída ao utilizador que criou.

   **Microsoft.Compute/virtualMachines Versão API 2018-06-01**

   Se `apiVersion` `2018-06-01` for, as suas identidades geridas atribuídas ao utilizador são armazenadas no `userAssignedIdentities` formato dicionário e o valor deve ser armazenado numa `<USERASSIGNEDIDENTITYNAME>` variável definida na secção do seu `variables` modelo.

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

   **Microsoft.Compute/virtualMachines Versão API 2017-12-01**

   Se o seu `apiVersion` é , as suas `2017-12-01` identidades geridas atribuídas ao utilizador são armazenadas na `identityIds` matriz e o valor deve ser armazenado numa `<USERASSIGNEDIDENTITYNAME>` variável definida na secção do seu `variables` modelo.

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

3. Quando terminar, as seguintes secções devem ser adicionadas à `resource` secção do seu modelo e devem assemelhar-se às seguintes:

   **Microsoft.Compute/virtualMachines Versão API 2018-06-01**    

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
   **Microsoft.Compute/virtualMachines Versão API 2017-12-01**

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerida atribuída pelo utilizador de um VM Azure

Para remover uma identidade atribuída ao utilizador de um VM, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Quer faça seduca localmente ou através do portal Azure, utilize uma conta associada à subscrição Azure que contenha o VM.

2. Carregue o modelo num [editor](#azure-resource-manager-templates) e localize o `Microsoft.Compute/virtualMachines` recurso de interesse dentro da `resources` secção. Se tiver um VM que só tenha identidade gerida atribuída ao utilizador, pode desativá-la alterando o tipo de identidade para `None` .

   O exemplo a seguir mostra como remover todas as identidades geridas atribuídas pelo utilizador de um VM sem identidades geridas atribuídas pelo sistema:

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

   **Microsoft.Compute/virtualMachines Versão API 2018-06-01**

   Para remover uma única identidade gerida atribuída pelo utilizador de um VM, remova-a do `useraAssignedIdentities` dicionário.

   Se tiver uma identidade gerida atribuída ao sistema, mantenha-a no `type` valor abaixo do `identity` valor.

   **Microsoft.Compute/virtualMachines Versão API 2017-12-01**

   Para remover uma única identidade gerida atribuída pelo utilizador de um VM, remova-a da `identityIds` matriz.

   Se tiver uma identidade gerida atribuída ao sistema, mantenha-a no `type` valor abaixo do `identity` valor.

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para visão geral dos recursos Azure](overview.md).