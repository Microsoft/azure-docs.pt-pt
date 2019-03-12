---
title: Criar um plano gráfico com a REST API
description: Utilize planos gráficos do Azure para criar, definir e implementar artefactos com a API REST.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/04/2019
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 347ee8389677e507e299490d86ae9b51ba4f59a6
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731574"
---
# <a name="define-and-assign-an-azure-blueprint-with-rest-api"></a>Definir e Atribuir um Azure Blueprint com a API REST

Aprender a criar e atribuir esquemas permite a definição de padrões comuns para desenvolver configurações reutilizáveis e rapidamente implementáveis com base nos modelos do Resource Manager, política, segurança e muito mais. Neste tutorial, vai aprender a utilizar o Azure Blueprints para realizar algumas das tarefas comuns relacionadas com a criação, publicação e atribuição de um esquema na sua organização, tais como:

> [!div class="checklist"]
> - Criar um novo esquema e adicionar vários artefactos suportados
> - Fazer alterações a um esquema existente ainda no modo de **Rascunho**
> - Marcar um esquema como pronto a atribuir com o estado **Publicado**
> - Atribuir um esquema a uma subscrição existente
> - Verificar o estado e progresso de um esquema atribuído
> - Remover um esquema que foi atribuído a uma subscrição

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="getting-started-with-rest-api"></a>Introdução à API REST

Se não estiver familiarizado com a API REST, comece por rever a [Referência da API REST do Azure](/rest/api/azure/) para obter uma compreensão geral da API REST, especificamente o URI do pedido e o corpo do pedido. Este artigo utiliza estes conceitos para fornecer instruções para trabalhar com o Azure Blueprints e pressupõe um conhecimento prático dos mesmos. Ferramentas como o [ARMClient](https://github.com/projectkudu/ARMClient) e outras podem processar a autorização automaticamente e são recomendadas para iniciantes.

Para as especificações de Esquema, veja [API REST de Esquemas do Azure](/rest/api/blueprints/).

### <a name="rest-api-and-powershell"></a>API REST e PowerShell

Se ainda não tem uma ferramenta para fazer chamadas à API REST, considere utilizar o PowerShell para estas instruções. Segue-se um cabeçalho de exemplo para fazer a autenticação com o Azure. Gere um cabeçalho de autenticação, por vezes denominado **Token de portador**, e forneça o URI da API REST para ligar a parâmetros ou um **Corpo do Pedido**:

```powershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2016-06-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Substitua `{subscriptionId}` na variável **$restUri** acima para obter informações sobre a sua subscrição. A variável $response inclui o resultado do cmdlet `Invoke-RestMethod`, que pode ser analisado com cmdlets, tais como [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Se o ponto final de serviço da API REST esperar um **Corpo do Pedido**, forneça uma variável com o formato JSON para o parâmetro `-Body` de `Invoke-RestMethod`.

## <a name="create-a-blueprint"></a>Criar um esquema

O primeiro passo na definição de um padrão de conformidade é compor um esquema a partir dos recursos disponíveis. Vamos criar um esquema com o nome "MyBlueprint" para configurar as atribuições de função e política para a subscrição. Em seguida, vamos adicionar um grupo de recursos, um modelo do Resource Manager e uma atribuição de função no grupo de recursos.

> [!NOTE]
> Ao utilizar a API REST, o objeto _esquema_ é criado em primeiro lugar. Para cada _artefacto_ a adicionar que tenha parâmetros, os parâmetros precisam de ser definidos com antecedência no _esquema_ inicial.

Em cada URI da API REST, existem variáveis que são utilizadas que precisa de substituir pelos seus próprios valores:

- `{YourMG}` -Substituir com o ID do seu grupo de gestão
- `{subscriptionId}` - substituir pelo ID da subscrição

1. Crie o objeto _esquema_ inicial. O **Corpo do Pedido** inclui propriedades sobre o esquema, grupos de recursos a criar e todos os parâmetros ao nível do esquema. Os parâmetros são definidos durante a atribuição e utilizados pelos artefactos adicionados nos passos posteriores.

   - URI da API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
     ```

   - Corpo do Pedido

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
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
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
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

1. Adicione a atribuição de função no momento da subscrição. O **Corpo do Pedido** define o _tipo_ de artefacto, as propriedades são alinhadas com o identificador de definição de função e as identidades do principal são transmitidas como uma matriz de valores. No exemplo abaixo, as identidades do principal a quem foi concedida a função especificada estão configuradas para um parâmetro que é definido durante a atribuição do esquema. Este exemplo utiliza a _contribuinte_ função incorporada com um GUID de `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - URI da API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2018-11-01-preview
     ```

   - Corpo do Pedido

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. Adicione a atribuição de política no momento da subscrição. O **Corpo do Pedido** define o _tipo_ de artefacto, as propriedades que se alinham com uma definição de política ou iniciativa e configura a atribuição de política para utilizar os parâmetros de esquema definidos para configurar durante a atribuição do esquema. Este exemplo utiliza a _aplicar etiqueta e respetivo valor predefinido para grupos de recursos_ política incorporada com um GUID de `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - URI da API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2018-11-01-preview
     ```

   - Corpo do Pedido

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. Adicione outra atribuição de política para a etiqueta de Armazenamento (reutilizando o parâmetro _storageAccountType_) na subscrição. Este artefacto de atribuição de política adicional demonstra que um parâmetro definido no esquema é utilizável por mais do que um artefacto. No exemplo, o **storageAccountType** é utilizado para definir uma etiqueta no grupo de recursos. Este valor apresenta informações sobre a conta de armazenamento que é criada no passo seguinte. Este exemplo utiliza a _aplicar etiqueta e respetivo valor predefinido para grupos de recursos_ política incorporada com um GUID de `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - URI da API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
     ```

   - Corpo do Pedido

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. Adicione um modelo no grupo de recursos. O **Corpo do Pedido** para um modelo do Resource Manager inclui o componente JSON normal do modelo e define o grupo de recursos de destino com **properties.resourceGroup**. O modelo também reutiliza os parâmetros de esquema **storageAccountType**, **tagName** e **tagValue** ao passar cada um para o modelo. Os parâmetros de esquema são disponibilizados ao modelo através da definição de **properties.parameters** e, dentro do modelo JSON, esse par chave-valor é utilizado para injetar o valor. Os nomes dos parâmetros de esquema e modelo podem ser os mesmos, mas foram diferenciados para ilustrar como cada um deles passa do esquema para o artefacto de modelo.

   - URI da API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2018-11-01-preview
     ```

   - Corpo do Pedido

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
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
                     "location": "[resourceGroups('storageRG').location]",
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
             },
             "resourceGroup": "storageRG",
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
     }
     ```

1. Adicione atribuição de função no grupo de recursos. À semelhança da entrada de atribuição de função anterior, o exemplo abaixo utiliza o identificador de definição para a função **Proprietário** e fornece-lhe um parâmetro diferente do esquema. Este exemplo utiliza a _proprietário_ função incorporada com um GUID de `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - URI da API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
     ```

   - Corpo do Pedido

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

## <a name="publish-a-blueprint"></a>Publicar um esquema

Agora que os artefactos foram adicionados ao esquema, é altura de publicá-lo. A publicação disponibiliza-o para atribuir a uma subscrição.

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2018-11-01-preview
  ```

O valor para `{BlueprintVersion}` é uma cadeia de letras, números e hífenes (sem espaços ou outros carateres especiais) com um comprimento máximo de 20 carateres. Utilize um valor exclusivo e informativo, como **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Atribuir um esquema

Depois de um esquema ser publicado com a API REST, pode ser atribuído a uma subscrição. Atribua o esquema que criou a uma das subscrições na hierarquia do grupo de gestão. Se o plano gráfico é guardado para uma subscrição, só podem ser atribuído a essa subscrição. O **Corpo do Pedido** especifica o esquema a atribuir, indica o nome e a localização de quaisquer grupos de recursos na definição do esquema, e indica todos os parâmetros que foram definidos no esquema e utilizados por um ou mais artefactos associados.

Em cada URI da API REST, existem variáveis que são utilizadas que precisa de substituir pelos seus próprios valores:

- `{tenantId}` -Substituir pelo seu ID de inquilino
- `{YourMG}` -Substituir com o ID do seu grupo de gestão
- `{subscriptionId}` - substituir pelo ID da subscrição

1. Forneça ao principal de serviço do Azure Blueprint a função **Proprietário** na subscrição de destino. O AppId é estático (`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`), mas o ID de principal de serviço varia por inquilino. Pode pedir detalhes para o seu inquilino através da API REST seguinte. Utiliza a [Graph API do Azure Active Directory](../../active-directory/develop/active-directory-graph-api.md) que tem uma autorização diferente.

   - URI da API REST

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. Execute a implementação do esquema, atribuindo-o a uma subscrição. Como os parâmetros **contributors** e **owners** exigem que seja concedida a atribuição de função a uma matriz de objectIds dos principais, utilize a [Graph API do Azure Active Directory](../../active-directory/develop/active-directory-graph-api.md) para recolher os objectIds para utilizar no **Corpo do Pedido** para os seus próprios utilizadores, grupos ou principais de serviço.

   - URI da API REST

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
     ```

   - Corpo do Pedido

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

   - Identidade gerida atribuída pelo utilizador

     Uma atribuição do esquema também pode utilizar um [atribuído ao utilizador a identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md). Neste caso, o **identidade** parte do corpo do pedido é alterado da seguinte forma.  Substitua `{yourRG}` e `{userIdentity}` com seu recurso agrupar nome e o nome da sua identidade gerida atribuído ao utilizador, respectivamente.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     O **atribuído ao utilizador a identidade gerida** podem estar em qualquer subscrição e grupo de recursos do utilizador atribuir o plano gráfico tem permissões para.

     > [!IMPORTANT]
     > Planos gráficos não gere a identidade gerida atribuído ao utilizador. Os usuários são responsáveis pela atribuição de funções suficientes e permissões ou a atribuição do esquema falhará.

## <a name="unassign-a-blueprint"></a>Anular a atribuição de um esquema

Pode remover um esquema de uma subscrição. A remoção é, muitas vezes, feita quando os recursos de artefacto já não são precisos. Quando um esquema é removido, os artefactos atribuídos como parte desse esquema são deixados para trás. Para remover uma atribuição de esquema, utilize a seguinte operação da API REST:

- URI da API REST

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="delete-a-blueprint"></a>Eliminar um esquema

Para remover o próprio esquema, utilize a seguinte operação da API REST:

- URI da API REST

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="next-steps"></a>Passos Seguintes

- Saber mais sobre o [ciclo de vida do esquema](./concepts/lifecycle.md)
- Compreender como utilizar [parâmetros estáticos e dinâmicos](./concepts/parameters.md)
- Aprender a personalizar a [ordem de sequenciação do esquema](./concepts/sequencing-order.md)
- Saber como utilizar o [bloqueio de recursos de esquema](./concepts/resource-locking.md)
- Saber como [atualizar as atribuições existentes](./how-to/update-existing-assignments.md)
- Resolver problemas durante a atribuição de um esquema com [resolução de problemas gerais](./troubleshoot/general.md)