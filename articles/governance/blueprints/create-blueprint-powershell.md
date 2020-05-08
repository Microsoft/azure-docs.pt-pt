---
title: 'Quickstart: Criar uma planta com powerShell'
description: Neste arranque rápido, utiliza-se plantas Azure para criar, definir e implementar artefactos utilizando a PowerShell.
ms.date: 05/06/2020
ms.topic: quickstart
ms.openlocfilehash: 79feafa48d5d180949b8a23163f2ee9b686e6076
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864118"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Quickstart: Defina e atribua um projeto azure com PowerShell

Aprender a criar e atribuir esquemas permite a definição de padrões comuns para desenvolver configurações reutilizáveis e rapidamente implementáveis com base nos modelos do Resource Manager, política, segurança e muito mais. Neste tutorial, vai aprender a utilizar o Azure Blueprints para realizar algumas das tarefas comuns relacionadas com a criação, publicação e atribuição de um esquema na sua organização, tais como:

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

Se ainda não estiver instalado, siga as instruções em [Adicionar o módulo Az.Blueprint](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) para instalar e validar o módulo **Az.Blueprint** da Galeria PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Criar um esquema

O primeiro passo na definição de um padrão de conformidade é compor um esquema a partir dos recursos disponíveis. Vamos criar um esquema com o nome "MyBlueprint" para configurar as atribuições de função e política para a subscrição. Em seguida, vamos adicionar um grupo de recursos, um modelo do Resource Manager e uma atribuição de função no grupo de recursos.

> [!NOTE]
> Ao utilizar o PowerShell, o objeto de _planta_ é criado primeiro. Para cada _artefacto_ a adicionar que tenha parâmetros, os parâmetros precisam de ser definidos com antecedência no _esquema_ inicial.

1. Crie o objeto _esquema_ inicial. O parâmetro **BlueprintFile** leva um ficheiro JSON que inclui propriedades sobre a planta, quaisquer grupos de recursos para criar, e todos os parâmetros de nível de planta. Os parâmetros são definidos durante a atribuição e utilizados pelos artefactos adicionados nos passos posteriores.

   - Arquivo JSON - blueprint.json

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

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Utilize a planta do nome de _ficheiro.json_ ao criar as definições de plantas programáticas.
     > Este nome de ficheiro é utilizado quando se chama [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     O objeto de planta é criado na subscrição predefinida por padrão. Para especificar o grupo de gestão, utilize o parâmetro **ManagementGroupId**. Para especificar a subscrição, utilize o parâmetro **SubscriptionId**.

1. Adicione a atribuição de função no momento da subscrição. O **ArtifactFile** define o _tipo_ de artefacto, as propriedades alinham-se com o identificador de definição de papel, e as principais identidades são passadas como uma variedade de valores. No exemplo abaixo, as identidades do principal a quem foi concedida a função especificada estão configuradas para um parâmetro que é definido durante a atribuição do esquema. Este exemplo _Contributor_ utiliza o papel integrado do `b24988ac-6180-42a0-ab88-20f7382dd24c`Contribuinte com um GUID de .

   - Arquivo JSON - \artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Adicione a atribuição de política no momento da subscrição. O **ArtifactFile** define o _tipo_ de artefacto, as propriedades que se alinham com uma definição de política ou de iniciativa, e configura a atribuição de políticas para usar os parâmetros de planta definidos para configurar durante a atribuição do projeto. Este exemplo utiliza a etiqueta Apply e o seu valor padrão `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`para grupos de recursos _integrados_ na política com um GUID de .

   - Arquivo JSON - \artifacts\policyTags.json

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

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Adicione outra atribuição de política para a etiqueta de Armazenamento (reutilizando o parâmetro _storageAccountType_) na subscrição. Este artefacto de atribuição de política adicional demonstra que um parâmetro definido no esquema é utilizável por mais do que um artefacto. No exemplo, o **storageAccountType** é utilizado para definir uma etiqueta no grupo de recursos. Este valor apresenta informações sobre a conta de armazenamento que é criada no passo seguinte. Este exemplo utiliza a etiqueta Apply e o seu valor padrão `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`para grupos de recursos _integrados_ na política com um GUID de .

   - Arquivo JSON - \artifacts\policyStorageTags.json

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

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Adicione um modelo no grupo de recursos. O **ModeloFile** para um modelo de Gestor de Recursos inclui o componente JSON normal do modelo. O modelo também reutiliza os parâmetros de esquema **storageAccountType**, **tagName** e **tagValue** ao passar cada um para o modelo. Os parâmetros da planta estão disponíveis para o modelo utilizando o modelo de **parâmetroParameterFile** e dentro do modelo JSON que o par de valor-chave é usado para injetar o valor. Os nomes dos parâmetros de modelo e modelo podem ser os mesmos.

   - Ficheiro de modelo do Gestor de Recursos JSON Azure - \artifacts\templateStorage.json

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

   - Ficheiro de parâmetro de modelo jSON Azure Resource Manager - \artifacts\templateStorageParams.json

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

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Adicione atribuição de função no grupo de recursos. À semelhança da entrada de atribuição de função anterior, o exemplo abaixo utiliza o identificador de definição para a função **Proprietário** e fornece-lhe um parâmetro diferente do esquema. Este exemplo utiliza o papel _do_ Proprietário `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`incorporado com um GUID de .

   - Arquivo JSON - \artifacts\roleOwner.json

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

   - Comando do PowerShell

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

Uma vez que uma planta é publicada usando powerShell, é atribuível a uma subscrição. Atribua o esquema que criou a uma das subscrições na hierarquia do grupo de gestão. Se a planta for guardada para uma subscrição, só pode ser atribuída a essa subscrição. O parâmetro **Blueprint** especifica a planta para atribuir. Para fornecer os parâmetros de nome, localização, identidade, bloqueio e `New-AzBlueprintAssignment` planta, utilize os parâmetros PowerShell correspondentes no cmdlet ou forneça-os no ficheiro JSON do parâmetro **De atribuiçãoFile.**

1. Execute a implementação do esquema, atribuindo-o a uma subscrição. Dado que os parâmetros dos **contribuintes** e **proprietários** exigem que seja concedida uma série de objectids dos principais a atribuição da função, utilize a API do Gráfico [de Diretório Ativo Azure](../../active-directory/develop/active-directory-graph-api.md) para recolher os objectids para utilização no **AssignmentFile** para os seus próprios utilizadores, grupos ou diretores de serviço.

   - Arquivo JSON - blueprintAssignment.json

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

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Identidade gerida atribuída ao utilizador

     Uma atribuição de plantas também pode usar uma [identidade gerida atribuída pelo utilizador.](../../active-directory/managed-identities-azure-resources/overview.md)
     Neste caso, a parte de **identidade** do ficheiro de atribuição da JSON muda da seguinte forma. `{tenantId}`Substitua, `{subscriptionId}` `{yourRG}`e `{userIdentity}` com o seu inquilinoId, subscriçãoId, nome de grupo de recursos, e o nome da sua identidade gerida atribuída ao utilizador, respectivamente.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     A **identidade gerida atribuída pelo utilizador** pode estar em qualquer grupo de subscrição e recursos a que o utilizador que atribui a planta tem permissões.

     > [!IMPORTANT]
     > A Azure Blueprints não gere a identidade gerida atribuída pelo utilizador. Os utilizadores são responsáveis pela atribuição de funções e permissões suficientes ou a atribuição do projeto falhará.

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="unassign-a-blueprint"></a>Anular a atribuição de um esquema

Pode remover um esquema de uma subscrição. A remoção é, muitas vezes, feita quando os recursos de artefacto já não são precisos. Quando um esquema é removido, os artefactos atribuídos como parte desse esquema são deixados para trás. Para remover uma atribuição `Remove-AzBlueprintAssignment` de plantas, utilize o cmdlet:

atribuir MyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste, atribuiste e removeste uma planta com a PowerShell. Para saber mais sobre as Plantas Azure, continue o artigo blueprint lifecycle.

> [!div class="nextstepaction"]
> [Conheça o ciclo de vida da planta](./concepts/lifecycle.md)