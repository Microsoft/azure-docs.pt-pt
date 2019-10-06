---
title: Criar um plano gráfico com o PowerShell
description: Use os planos gráficos do Azure para criar, definir e implantar artefatos usando o PowerShell.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/21/2019
ms.topic: quickstart
ms.service: blueprints
ms.openlocfilehash: 6a1ef5aece030ac359e9c5811c815bec5ed57d27
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978521"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Início rápido: Definir e atribuir um Azure Blueprint com o PowerShell

Aprender a criar e atribuir esquemas permite a definição de padrões comuns para desenvolver configurações reutilizáveis e rapidamente implementáveis com base nos modelos do Resource Manager, política, segurança e muito mais. Neste tutorial, vai aprender a utilizar o Azure Blueprints para realizar algumas das tarefas comuns relacionadas com a criação, publicação e atribuição de um esquema na sua organização, tais como:

> [!div class="checklist"]
> - Criar um novo esquema e adicionar vários artefactos suportados
> - Fazer alterações a um esquema existente ainda no modo de **Rascunho**
> - Marcar um esquema como pronto a atribuir com o estado **Publicado**
> - Atribuir um esquema a uma subscrição existente
> - Verificar o estado e progresso de um esquema atribuído
> - Remover um esquema que foi atribuído a uma subscrição

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Se ele ainda não estiver instalado, siga as instruções em [Adicionar o módulo AZ. Blueprint](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) para instalar e validar o módulo **AZ. Blueprint** da galeria do PowerShell.

## <a name="create-a-blueprint"></a>Criar um esquema

O primeiro passo na definição de um padrão de conformidade é compor um esquema a partir dos recursos disponíveis. Vamos criar um esquema com o nome "MyBlueprint" para configurar as atribuições de função e política para a subscrição. Em seguida, vamos adicionar um grupo de recursos, um modelo do Resource Manager e uma atribuição de função no grupo de recursos.

> [!NOTE]
> Ao usar o PowerShell, o objeto _Blueprint_ é criado primeiro. Para cada _artefacto_ a adicionar que tenha parâmetros, os parâmetros precisam de ser definidos com antecedência no _esquema_ inicial.

1. Crie o objeto _esquema_ inicial. O parâmetro **blueprintfile** usa um arquivo JSON que inclui propriedades sobre o plano gráfico, todos os grupos de recursos a serem criados e todos os parâmetros de nível Blueprint. Os parâmetros são definidos durante a atribuição e utilizados pelos artefactos adicionados nos passos posteriores.

   - Arquivo JSON-Blueprint. JSON

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
     > Use o FileName _Blueprint. JSON_ ao criar suas definições de plantas programaticamente.
     > Esse nome de arquivo é usado ao chamar [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     O objeto Blueprint é criado na assinatura padrão por padrão. Para especificar o grupo de gerenciamento, use o parâmetro **ManagementGroupId**. Para especificar a assinatura, use o parâmetro **SubscriptionId**.

1. Adicione a atribuição de função no momento da subscrição. O **artefatofile** define o _tipo_ de artefato, as propriedades se alinham ao identificador de definição de função e as identidades de entidade são passadas como uma matriz de valores. No exemplo abaixo, as identidades do principal a quem foi concedida a função especificada estão configuradas para um parâmetro que é definido durante a atribuição do esquema. Este exemplo usa a função interna _colaborador_ com um GUID de `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - Arquivo JSON-\artifacts\roleContributor.json

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

1. Adicione a atribuição de política no momento da subscrição. O **artefatofile** define o _tipo_ de artefato, as propriedades que se alinham a uma definição de política ou iniciativa e configura a atribuição de política para usar os parâmetros de plano gráfico definidos para configurar durante a atribuição do Blueprint. Este exemplo usa a _marca Apply e seu valor padrão para a_ política interna de grupos de recursos com um GUID de `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Arquivo JSON-\artifacts\policyTags.json

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

1. Adicione outra atribuição de política para a etiqueta de Armazenamento (reutilizando o parâmetro _storageAccountType_) na subscrição. Este artefacto de atribuição de política adicional demonstra que um parâmetro definido no esquema é utilizável por mais do que um artefacto. No exemplo, o **storageAccountType** é utilizado para definir uma etiqueta no grupo de recursos. Este valor apresenta informações sobre a conta de armazenamento que é criada no passo seguinte. Este exemplo usa a _marca Apply e seu valor padrão para a_ política interna de grupos de recursos com um GUID de `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Arquivo JSON-\artifacts\policyStorageTags.json

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

1. Adicione um modelo no grupo de recursos. O **modelofile** para um modelo do Resource Manager inclui o componente JSON normal do modelo. O modelo também reutiliza os parâmetros de esquema **storageAccountType**, **tagName** e **tagValue** ao passar cada um para o modelo. Os parâmetros de Blueprint estão disponíveis para o modelo usando o parâmetro **TemplateParameterFile** e dentro do modelo JSON que o par chave-valor é usado para injetar o valor. Os nomes dos parâmetros Blueprint e Template podem ser os mesmos.

   - Arquivo de modelo de Azure Resource Manager JSON – \artifacts\templateStorage.json

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

   - Arquivo de parâmetro de modelo JSON Azure Resource Manager-\artifacts\templateStorageParams.json

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

1. Adicione atribuição de função no grupo de recursos. À semelhança da entrada de atribuição de função anterior, o exemplo abaixo utiliza o identificador de definição para a função **Proprietário** e fornece-lhe um parâmetro diferente do esquema. Este exemplo usa a função interna de _proprietário_ com um GUID de `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - Arquivo JSON-\artifacts\roleOwner.json

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

Depois que um plano gráfico é publicado usando o PowerShell, ele é atribuível a uma assinatura. Atribua o esquema que criou a uma das subscrições na hierarquia do grupo de gestão. Se o plano gráfico for salvo em uma assinatura, ele só poderá ser atribuído a essa assinatura. O parâmetro **Blueprint** especifica o plano gráfico a ser atribuído. Para fornecer parâmetros de nome, localização, identidade, bloqueio e plano gráfico, use os parâmetros correspondentes do PowerShell no cmdlet `New-AzBlueprintAssignment` ou forneça-os no arquivo JSON do parâmetro **assignmentfile** .

1. Execute a implementação do esquema, atribuindo-o a uma subscrição. Como os parâmetros de **colaboradores** e **proprietários** exigem uma matriz de ObjectIDs das entidades de segurança para receber a atribuição de função, use [Azure Active Directory API do Graph](../../active-directory/develop/active-directory-graph-api.md) para coletar as ObjectIDs para uso no **assignmentfile** para seus próprios usuários, grupos ou entidades de serviço.

   - Arquivo JSON-blueprintAssignment. JSON

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

   - Identidade gerenciada atribuída pelo usuário

     Uma atribuição de Blueprint também pode usar uma [identidade gerenciada atribuída pelo usuário](../../active-directory/managed-identities-azure-resources/overview.md).
     Nesse caso, a parte de **identidade** do arquivo de atribuição JSON é alterada da seguinte maneira. Substitua `{tenantId}`, `{subscriptionId}`, `{yourRG}` e `{userIdentity}` por seu tenantid, SubscriptionId, nome do grupo de recursos e o nome da sua identidade gerenciada atribuída pelo usuário, respectivamente.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     A **identidade gerenciada atribuída pelo usuário** pode estar em qualquer assinatura e grupo de recursos ao qual o usuário que atribui o plano gráfico tenha permissões.

     > [!IMPORTANT]
     > Os planos gráficos não gerenciam a identidade gerenciada atribuída pelo usuário. Os usuários são responsáveis por atribuir funções e permissões suficientes, ou a atribuição Blueprint falhará.

## <a name="unassign-a-blueprint"></a>Anular a atribuição de um esquema

Pode remover um esquema de uma subscrição. A remoção é, muitas vezes, feita quando os recursos de artefacto já não são precisos. Quando um esquema é removido, os artefactos atribuídos como parte desse esquema são deixados para trás. Para remover uma atribuição de plano gráfico, use o cmdlet `Remove-AzBlueprintAssignment`:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](./concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](./concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](./concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](./concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](./how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](./troubleshoot/general.md).