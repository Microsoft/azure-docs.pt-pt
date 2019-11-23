---
title: 'Quickstart: Create a blueprint with PowerShell'
description: In this quickstart, you use Azure Blueprints to create, define, and deploy artifacts using the PowerShell.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: ed337fa040804159dae3194de1befc43ff2b698f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327469"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Quickstart: Define and Assign an Azure Blueprint with PowerShell

Aprender a criar e atribuir esquemas permite a definição de padrões comuns para desenvolver configurações reutilizáveis e rapidamente implementáveis com base nos modelos do Resource Manager, política, segurança e muito mais. Neste tutorial, vai aprender a utilizar o Azure Blueprints para realizar algumas das tarefas comuns relacionadas com a criação, publicação e atribuição de um esquema na sua organização, tais como:

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

If it isn't already installed, follow the instructions in [Add the Az.Blueprint module](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) to install and validate the **Az.Blueprint** module from the PowerShell Gallery.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Criar um esquema

O primeiro passo na definição de um padrão de conformidade é compor um esquema a partir dos recursos disponíveis. Vamos criar um esquema com o nome "MyBlueprint" para configurar as atribuições de função e política para a subscrição. Em seguida, vamos adicionar um grupo de recursos, um modelo do Resource Manager e uma atribuição de função no grupo de recursos.

> [!NOTE]
> When using PowerShell, the _blueprint_ object is created first. Para cada _artefacto_ a adicionar que tenha parâmetros, os parâmetros precisam de ser definidos com antecedência no _esquema_ inicial.

1. Crie o objeto _esquema_ inicial. The **BlueprintFile** parameter takes a JSON file which includes properties about the blueprint, any resource groups to create, and all of the blueprint level parameters. Os parâmetros são definidos durante a atribuição e utilizados pelos artefactos adicionados nos passos posteriores.

   - JSON file - blueprint.json

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "defaultValue": "Standard_LRS",
                     "allowedValues": [
                         "Standard_LRS",
                         "Standard_GRS",
                         "Standard_ZRS",
                         "Premium_LRS"
                     ],
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Use the filename _blueprint.json_ when creating your blueprint definitions programmatically.
     > This file name is used when calling [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     The blueprint object is created in the default subscription by default. To specify the management group, use parameter **ManagementGroupId**. To specify the subscription, use parameter **SubscriptionId**.

1. Adicione a atribuição de função no momento da subscrição. The **ArtifactFile** defines the _kind_ of artifact, the properties align to the role definition identifier, and the principal identities are passed as an array of values. No exemplo abaixo, as identidades do principal a quem foi concedida a função especificada estão configuradas para um parâmetro que é definido durante a atribuição do esquema. This example uses the _Contributor_ built-in role with a GUID of `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - JSON file - \artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Adicione a atribuição de política no momento da subscrição. The **ArtifactFile** defines the _kind_ of artifact, the properties that align to a policy or initiative definition, and configures the policy assignment to use the defined blueprint parameters to configure during blueprint assignment. This example uses the _Apply tag and its default value to resource groups_ built-in policy with a GUID of `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - JSON file - \artifacts\policyTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Adicione outra atribuição de política para a etiqueta de Armazenamento (reutilizando o parâmetro _storageAccountType_) na subscrição. Este artefacto de atribuição de política adicional demonstra que um parâmetro definido no esquema é utilizável por mais do que um artefacto. No exemplo, o **storageAccountType** é utilizado para definir uma etiqueta no grupo de recursos. Este valor apresenta informações sobre a conta de armazenamento que é criada no passo seguinte. This example uses the _Apply tag and its default value to resource groups_ built-in policy with a GUID of `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - JSON file - \artifacts\policyStorageTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Adicione um modelo no grupo de recursos. The **TemplateFile** for a Resource Manager template includes the normal JSON component of the template. O modelo também reutiliza os parâmetros de esquema **storageAccountType**, **tagName** e **tagValue** ao passar cada um para o modelo. The blueprint parameters are available to the template by using parameter **TemplateParameterFile** and inside the template JSON that key-value pair is used to inject the value. The blueprint and template parameter names could be the same.

   - JSON Azure Resource Manager template file - \artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - JSON Azure Resource Manager template parameter file - \artifacts\templateStorageParams.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "value": "[parameters('storageAccountType')]"
             },
             "tagNameFromBP": {
                 "value": "[parameters('tagName')]"
             },
             "tagValueFromBP": {
                 "value": "[parameters('tagValue')]"
             }
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Adicione atribuição de função no grupo de recursos. À semelhança da entrada de atribuição de função anterior, o exemplo abaixo utiliza o identificador de definição para a função **Proprietário** e fornece-lhe um parâmetro diferente do esquema. This example uses the _Owner_ built-in role with a GUID of `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - JSON file - \artifacts\roleOwner.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Publicar um esquema

Agora que os artefactos foram adicionados ao esquema, é altura de publicá-lo. A publicação disponibiliza-o para atribuir a uma subscrição.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

O valor para `{BlueprintVersion}` é uma cadeia de letras, números e hífenes (sem espaços ou outros carateres especiais) com um comprimento máximo de 20 carateres. Utilize um valor exclusivo e informativo, como **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Atribuir um esquema

Once a blueprint is published using PowerShell, it's assignable to a subscription. Atribua o esquema que criou a uma das subscrições na hierarquia do grupo de gestão. If the blueprint is saved to a subscription, it can only be assigned to that subscription. The **Blueprint** parameter specifies the blueprint to assign. To provide name, location, identity, lock, and blueprint parameters, use the matching PowerShell parameters on the `New-AzBlueprintAssignment` cmdlet or provide them in the **AssignmentFile** parameter JSON file.

1. Execute a implementação do esquema, atribuindo-o a uma subscrição. As the **contributors** and **owners** parameters require an array of objectIds of the principals to be granted the role assignment, use [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) for gathering the objectIds for use in the **AssignmentFile** for your own users, groups, or service principals.

   - JSON file - blueprintAssignment.json

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - User-assigned managed identity

     A blueprint assignment can also use a [user-assigned managed identity](../../active-directory/managed-identities-azure-resources/overview.md).
     In this case, the **identity** portion of the JSON assignment file changes as follows. Replace `{tenantId}`, `{subscriptionId}`, `{yourRG}`, and `{userIdentity}` with your tenantId, subscriptionId, resource group name, and the name of your user-assigned managed identity, respectively.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     The **user-assigned managed identity** can be in any subscription and resource group the user assigning the blueprint has permissions to.

     > [!IMPORTANT]
     > Blueprints doesn't manage the user-assigned managed identity. Users are responsible for assigning sufficient roles and permissions or the blueprint assignment will fail.

## <a name="unassign-a-blueprint"></a>Anular a atribuição de um esquema

Pode remover um esquema de uma subscrição. A remoção é, muitas vezes, feita quando os recursos de artefacto já não são precisos. Quando um esquema é removido, os artefactos atribuídos como parte desse esquema são deixados para trás. To remove a blueprint assignment, use the `Remove-AzBlueprintAssignment` cmdlet:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Passos seguintes

In this quickstart, you've created, assigned, and removed a blueprint with PowerShell. To learn more about Azure Blueprints, continue to the blueprint lifecycle article.

> [!div class="nextstepaction"]
> [Learn about the blueprint lifecycle](./concepts/lifecycle.md)