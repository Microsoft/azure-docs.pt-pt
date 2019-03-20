---
title: Como configurar identidades geridas para recursos do Azure numa VM do Azure ao utilizar um modelo
description: Instruções passo a passo para configurar identidades geridas para recursos do Azure na VM do Azure, com um modelo Azure Resource Manager.
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
ms.date: 09/14/2017
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c93716d5c8d0c9a74e2cb14a35637faa029c156
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226186"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Configurar identidades geridas para recursos do Azure na VM do Azure com um modelo

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, usando o modelo de implementação Azure Resource Manager, aprenderá a efetuar as seguintes identidades geridas para operações de recursos do Azure numa VM do Azure:

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com a utilização do modelo de implementação Azure Resource Manager, consulte a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Tal como acontece com o portal do Azure e criação de scripts, [do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) os modelos fornecem a capacidade de implementar recursos novos ou modificados definidos por um grupo de recursos do Azure. Várias opções estão disponíveis para edição de modelo e a implantação, local e baseado no portal, incluindo:

   - Utilizar um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite-lhe criar um modelo a partir do zero ou baseá-la num comum existente ou [modelo de início rápido](https://azure.microsoft.com/documentation/templates/).
   - Derivar de um grupo de recursos existente ao exportar um modelo a partir de qualquer uma [implementação original](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates), ou a partir do [estado atual da implantação](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
   - Com um local [editor de JSON (por exemplo, o VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)e, em seguida, carregar e implementar com o PowerShell ou a CLI.
   - Com o Visual Studio [projeto do grupo de recursos do Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) para criar e implementar um modelo.  

Independentemente da opção escolhida, sintaxe do modelo é o mesmo durante a implementação inicial e a reimplementação. Ativar um sistema ou uma identidade gerida atribuído ao utilizador numa VM nova ou existente é feito da mesma forma. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../../azure-resource-manager/deployment-modes.md) para implementações.

## <a name="system-assigned-managed-identity"></a>Atribuído ao sistema de identidade gerida

Nesta secção, irá ativar e desativar uma identidade gerida atribuído ao sistema através de um modelo Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Ativar a identidade gerida atribuído ao sistema durante a criação de uma VM do Azure ou numa VM existente

Para ativar a identidade gerida atribuído de sistema numa VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função.  Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Se iniciar sessão localmente no Azure ou através do portal do Azure, utilize uma conta que está associada à subscrição do Azure que contém a VM.

2. Para ativar a identidade gerida atribuído de sistema, carregar o modelo para um editor, localize a `Microsoft.Compute/virtualMachines` recursos de interesse na `resources` secção e adicione o `"identity"` propriedade no mesmo nível, como o `"type": "Microsoft.Compute/virtualMachines"` propriedade. Utilize a seguinte sintaxe:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   },
   ```

> [!NOTE]
> Opcionalmente, pode aprovisionar as identidades geridas para a extensão VM de recursos do Azure, especificando-o como um `resources` elemento no modelo. Este passo é opcional, como pode usar o ponto de extremidade para a identidade de serviço de metadados de instância do Azure (IMDS), para obtenção de tokens também.  Para obter mais informações, consulte [migre da extensão de VM para o Azure IMDS para autenticação](howto-migrate-vm-extension.md).

3. Quando tiver terminado, as seções a seguir devem ser adicionados para a `resource` secção de seu modelo e ele deve assemelhar-se o seguinte:

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

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Atribuir uma função de identidade gerida de atribuído de sistema da VM

Depois de ter ativado a identidade gerida atribuído de sistema na sua VM, pode querer conceder uma função como **leitor** acesso ao grupo de recursos no qual foi criado.

Para atribuir uma função para a identidade de sistema atribuído da VM, a conta tem do [administrador de acesso de utilizador](/azure/role-based-access-control/built-in-roles#user-access-administrator) atribuição de função.

1. Se iniciar sessão localmente no Azure ou através do portal do Azure, utilize uma conta que está associada à subscrição do Azure que contém a VM.
 
2. Carregar o modelo para um [editor](#azure-resource-manager-templates) e adicione as seguintes informações para dar a sua VM **leitor** acesso ao grupo de recursos no qual foi criado.  A estrutura do modelo pode variar consoante o editor e o modelo de implementação que escolher.
   
   Sob o `parameters` secção adicione o seguinte:

    ```JSON
    "builtInRoleType": {
          "type": "string",
          "defaultValue": "Reader"
        },
        "rbacGuid": {
          "type": "string"
        }
    ```

    Sob o `variables` secção adicione o seguinte:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Sob o `resources` secção adicione o seguinte:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Desativar uma identidade gerida atribuído de sistema de uma VM do Azure

Para remover o sistema atribuído a identidade gerida de uma VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função.  Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Se iniciar sessão localmente no Azure ou através do portal do Azure, utilize uma conta que está associada à subscrição do Azure que contém a VM.

2. Carregar o modelo para um [editor](#azure-resource-manager-templates) e localize a `Microsoft.Compute/virtualMachines` recursos de interesse no `resources` secção. Se tiver uma VM que só tenha atribuído ao sistema de identidade gerida, pode desativá-lo ao alterar o tipo de identidade para `None`.  
   
   **Microsoft.Compute/virtualMachines 2018-01 06 de versão de API**

   Se a VM tem o sistema e as identidades geridas atribuído ao utilizador, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` juntamente com o `userAssignedIdentities` valores de dicionário.

   **Microsoft.Compute/virtualMachines 2018-01 06 de versão de API**
   
   Se sua `apiVersion` é `2017-12-01` e a VM tem o sistema e as identidades geridas atribuído ao utilizador, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` juntamente com o `identityIds` matriz do atribuído ao utilizador gerida identidades.  
   
O exemplo seguinte mostra como remover uma identidade gerida atribuído de sistema a partir de uma VM com não gerenciados identidiades atribuídas:

```JSON
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[parameters('vmName')]",
    "location": "[resourceGroup().location]",
    "identity": { 
        "type": "None"
}
```

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, atribua uma identidade gerida atribuído ao utilizador a uma VM do Azure com o modelo Azure Resource Manager.

> [!Note]
> Para criar uma identidade gerida atribuído ao utilizador com um modelo de Gestor de recursos do Azure, veja [criar uma identidade gerida atribuído ao utilizador](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Atribuir uma identidade gerida atribuído ao utilizador a uma VM do Azure

Para atribuir uma identidade de utilizador atribuído a uma VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) atribuições de funções. Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Sob o `resources` elemento, adicione a seguinte entrada para atribuir uma identidade gerida atribuído ao utilizador para a VM.  Certifique-se de que substitua `<USERASSIGNEDIDENTITY>` com o nome do atribuído ao utilizador que criou a identidade de gerido.

   **Microsoft.Compute/virtualMachines 2018-01 06 de versão de API**

   Se sua `apiVersion` é `2018-06-01`, suas identidades geridas atribuído ao utilizador são armazenadas no `userAssignedIdentities` formato do dicionário e o `<USERASSIGNEDIDENTITYNAME>` valor deve ser armazenado numa variável definida no `variables` secção do seu modelo.

   ```json
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
   
   **Microsoft.Compute/virtualMachines versão 2017-12-01 de API**
    
   Se sua `apiVersion` é `2017-12-01`, suas identidades geridas atribuído ao utilizador são armazenadas no `identityIds` matriz e o `<USERASSIGNEDIDENTITYNAME>` valor deve ser armazenado numa variável definida no `variables` secção do seu modelo.
    
   ```json
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
       
3. Quando tiver terminado, as seções a seguir devem ser adicionados para a `resource` secção de seu modelo e ele deve assemelhar-se o seguinte:
   
   **Microsoft.Compute/virtualMachines 2018-01 06 de versão de API**    

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
   **Microsoft.Compute/virtualMachines versão 2017-12-01 de API**
   
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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerida atribuído ao utilizador a partir de uma VM do Azure

Para remover uma identidade atribuído ao utilizador a partir de uma VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função. Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Se iniciar sessão localmente no Azure ou através do portal do Azure, utilize uma conta que está associada à subscrição do Azure que contém a VM.

2. Carregar o modelo para um [editor](#azure-resource-manager-templates) e localize a `Microsoft.Compute/virtualMachines` recursos de interesse no `resources` secção. Se tiver uma VM que só tenha atribuído ao utilizador a identidade gerida, pode desativá-lo ao alterar o tipo de identidade para `None`.
 
   O exemplo seguinte mostra como remover todas as identidades geridas atribuído ao utilizador a partir de uma VM com nenhuma identidades geridas atribuído de sistema:
   
   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```
   
   **Microsoft.Compute/virtualMachines 2018-01 06 de versão de API**
    
   Para remover uma única identidade gerida atribuído ao utilizador a partir de uma VM, remova-a do `useraAssignedIdentities` dicionário.

   Se tiver uma identidade gerida atribuído de sistema, mantenha-a no no `type` valor sob a `identity` valor.
 
   **Microsoft.Compute/virtualMachines versão 2017-12-01 de API**

   Para remover uma única identidade gerida atribuído ao utilizador a partir de uma VM, remova-a do `identityIds` matriz.

   Se tiver uma identidade gerida atribuído de sistema, mantenha-a no no `type` valor sob a `identity` valor.
   
## <a name="next-steps"></a>Passos Seguintes

- [Gerido identidades para descrição geral de recursos do Azure](overview.md).

