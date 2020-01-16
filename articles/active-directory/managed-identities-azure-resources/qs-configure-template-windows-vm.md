---
title: Configurar identidades gerenciadas na VM do Azure usando o modelo-Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas para recursos do Azure em uma VM do Azure, usando um modelo de Azure Resource Manager.
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75972154"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando modelos

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código.

Neste artigo, usando o modelo de implantação Azure Resource Manager, você aprende a executar as seguintes identidades gerenciadas para operações de recursos do Azure em uma VM do Azure:

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com o uso do modelo de implantação Azure Resource Manager, confira a [seção visão geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Assim como ocorre com o portal do Azure e o script, os modelos de [Azure Resource Manager](../../azure-resource-manager/management/overview.md) fornecem a capacidade de implantar recursos novos ou modificados definidos por um grupo de recursos do Azure. Várias opções estão disponíveis para edição e implantação de modelos, tanto locais quanto baseadas em portal, incluindo:

   - Usando um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), que permite que você crie um modelo a partir do zero ou baseie-o em um modelo comum ou de [início rápido](https://azure.microsoft.com/documentation/templates/)existente.
   - Derivando de um grupo de recursos existente, exportando um modelo da [implantação original](../../azure-resource-manager/templates/export-template-portal.md)ou do [estado atual da implantação](../../azure-resource-manager/templates/export-template-portal.md).
   - Usando um [Editor de JSON local (como vs Code)](../../azure-resource-manager/resource-manager-create-first-template.md)e, em seguida, carregando e implantando usando o PowerShell ou a CLI.
   - Usando o projeto do [grupo de recursos do Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) do Visual Studio para criar e implantar um modelo.  

Independentemente da opção escolhida, a sintaxe do modelo é a mesma durante a implantação inicial e a reimplantação. A habilitação de uma identidade gerenciada atribuída pelo usuário ou pelo sistema em uma VM nova ou existente é feita da mesma maneira. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../../azure-resource-manager/templates/deployment-modes.md) para implantações.

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você habilitará e desabilitará uma identidade gerenciada atribuída pelo sistema usando um modelo de Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Habilitar a identidade gerenciada atribuída pelo sistema durante a criação de uma VM do Azure ou em uma VM existente

Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém a VM.

2. Para habilitar a identidade gerenciada atribuída pelo sistema, carregue o modelo em um editor, localize o `Microsoft.Compute/virtualMachines` recurso de interesse na seção `resources` e adicione a propriedade `"identity"` no mesmo nível que a propriedade `"type": "Microsoft.Compute/virtualMachines"`. Use a seguinte sintaxe:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Quando terminar, as seções a seguir devem ser adicionadas à seção `resource` do seu modelo e devem ser semelhantes ao seguinte:

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

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Atribuir uma função à identidade gerenciada atribuída pelo sistema da VM

Depois de habilitar a identidade gerenciada atribuída pelo sistema em sua VM, talvez você queira conceder a ela uma função como acesso de **leitor** ao grupo de recursos no qual ela foi criada.

Para atribuir uma função à identidade atribuída pelo sistema de sua VM, sua conta precisa da atribuição de função de [administrador de acesso do usuário](/azure/role-based-access-control/built-in-roles#user-access-administrator) .

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém a VM.

2. Carregue o modelo em um [Editor](#azure-resource-manager-templates) e adicione as informações a seguir para dar acesso de **leitor** de VM ao grupo de recursos no qual ele foi criado.  A estrutura do modelo pode variar dependendo do editor e do modelo de implantação que você escolher.

   Na seção `parameters`, adicione o seguinte:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    Na seção `variables`, adicione o seguinte:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Na seção `resources`, adicione o seguinte:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Desabilitar uma identidade gerenciada atribuída pelo sistema de uma VM do Azure

Para remover a identidade gerenciada atribuída pelo sistema de uma VM, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém a VM.

2. Carregue o modelo em um [Editor](#azure-resource-manager-templates) e localize o `Microsoft.Compute/virtualMachines` recurso de interesse na seção `resources`. Se você tiver uma VM que tem apenas identidade gerenciada atribuída pelo sistema, poderá desabilitá-la alterando o tipo de identidade para `None`.  

   **API Microsoft. Compute/virtualMachines versão 2018-06-01**

   Se sua VM tiver identidades gerenciadas atribuídas ao sistema e ao usuário, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` junto com os valores de dicionário de `userAssignedIdentities`.

   **API Microsoft. Compute/virtualMachines versão 2018-06-01**

   Se o `apiVersion` for `2017-12-01` e sua VM tiver identidades gerenciadas atribuídas ao sistema e ao usuário, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` junto com a matriz `identityIds` das identidades gerenciadas atribuídas pelo usuário.  

O exemplo a seguir mostra como remover uma identidade gerenciada atribuída pelo sistema de uma VM sem identidades gerenciadas atribuídas pelo usuário:

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

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você atribui uma identidade gerenciada atribuída pelo usuário a uma VM do Azure usando Azure Resource Manager modelo.

> [!Note]
> Para criar uma identidade gerenciada atribuída pelo usuário usando um modelo de Azure Resource Manager, consulte [criar uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Atribuir uma identidade gerenciada atribuída pelo usuário a uma VM do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. No elemento `resources`, adicione a seguinte entrada para atribuir uma identidade gerenciada atribuída pelo usuário à sua VM.  Certifique-se de substituir `<USERASSIGNEDIDENTITY>` pelo nome da identidade gerenciada atribuída pelo usuário que você criou.

   **API Microsoft. Compute/virtualMachines versão 2018-06-01**

   Se o `apiVersion` for `2018-06-01`, suas identidades gerenciadas atribuídas pelo usuário serão armazenadas no formato de dicionário `userAssignedIdentities` e o valor de `<USERASSIGNEDIDENTITYNAME>` deverá ser armazenado em uma variável definida na seção `variables` do seu modelo.

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

   **API Microsoft. Compute/virtualMachines versão 2017-12-01**

   Se o `apiVersion` for `2017-12-01`, suas identidades gerenciadas atribuídas pelo usuário serão armazenadas na matriz de `identityIds` e o valor de `<USERASSIGNEDIDENTITYNAME>` deverá ser armazenado em uma variável definida na seção `variables` do seu modelo.

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

3. Quando terminar, as seções a seguir devem ser adicionadas à seção `resource` do seu modelo e devem ser semelhantes ao seguinte:

   **API Microsoft. Compute/virtualMachines versão 2018-06-01**    

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
   **API Microsoft. Compute/virtualMachines versão 2017-12-01**

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerenciada atribuída pelo usuário de uma VM do Azure

Para remover uma identidade atribuída pelo usuário de uma VM, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém a VM.

2. Carregue o modelo em um [Editor](#azure-resource-manager-templates) e localize o `Microsoft.Compute/virtualMachines` recurso de interesse na seção `resources`. Se você tiver uma VM que tenha apenas uma identidade gerenciada atribuída pelo usuário, você poderá desabilitá-la alterando o tipo de identidade para `None`.

   O exemplo a seguir mostra como remover todas as identidades gerenciadas atribuídas pelo usuário de uma VM sem identidades gerenciadas atribuídas pelo sistema:

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

   **API Microsoft. Compute/virtualMachines versão 2018-06-01**

   Para remover uma única identidade gerenciada atribuída pelo usuário de uma VM, remova-a do dicionário de `useraAssignedIdentities`.

   Se você tiver uma identidade gerenciada atribuída pelo sistema, mantenha-a no `type` valor no valor `identity`.

   **API Microsoft. Compute/virtualMachines versão 2017-12-01**

   Para remover uma única identidade gerenciada atribuída pelo usuário de uma VM, remova-a da matriz de `identityIds`.

   Se você tiver uma identidade gerenciada atribuída pelo sistema, mantenha-a no `type` valor no valor `identity`.

## <a name="next-steps"></a>Passos seguintes

- [Identidades gerenciadas para visão geral dos recursos do Azure](overview.md).
