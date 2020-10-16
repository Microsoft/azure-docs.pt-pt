---
title: Mobilizar recursos para inquilino
description: Descreve como implantar recursos no âmbito do inquilino num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 48b3fbcedb119ae699624e79f83297f4ecbc9ede
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372396"
---
# <a name="create-resources-at-the-tenant-level"></a>Criar recursos ao nível dos inquilinos

À medida que a sua organização amadurece, poderá ter de definir e atribuir [políticas](../../governance/policy/overview.md) ou [controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) através do seu inquilino Azure AD. Com modelos de nível de inquilino, você pode declarativamente aplicar políticas e atribuir papéis a nível global.

## <a name="supported-resources"></a>Recursos suportados

Nem todos os tipos de recursos podem ser implantados ao nível do inquilino. Esta secção lista quais os tipos de recursos suportados.

Para políticas de Azure, utilize:

* [políticasAssinsagens](/azure/templates/microsoft.authorization/policyassignments)
* [políticasDefinições](/azure/templates/microsoft.authorization/policydefinitions)
* [políticasSetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Para o controlo de acesso baseado em funções Azure (Azure RBAC), utilize:

* [papéAs de assinaturas](/azure/templates/microsoft.authorization/roleassignments)

Para modelos aninhados que se desdobram em grupos de gestão, subscrições ou grupos de recursos, utilize:

* [implementações](/azure/templates/microsoft.resources/deployments)

Para criar grupos de gestão, utilize:

* [grupos de gestão](/azure/templates/microsoft.management/managementgroups)

Para gerir custos, utilize:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [instruções](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [faturaSecções](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

## <a name="schema"></a>Esquema

O esquema que você usa para implantações de inquilinos é diferente do esquema para implantações de grupos de recursos.

Para modelos, use:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

O esquema para um ficheiro de parâmetro é o mesmo para todos os âmbitos de implantação. Para ficheiros de parâmetros, utilize:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Acesso obrigatório

O principal que implementa o modelo deve ter permissões para criar recursos no âmbito do inquilino. O principal deve ter permissão para executar as ações de implantação ( `Microsoft.Resources/deployments/*` ) e para criar os recursos definidos no modelo. Por exemplo, para criar um grupo de gestão, o principal deve ter permissão do Contribuinte no âmbito do arrendatário. Para criar atribuições de funções, o principal deve ter permissão do Proprietário.

O Administrador Global do Diretório Ativo Azure não tem automaticamente permissão para atribuir funções. Para permitir a implementação do modelo no âmbito do inquilino, o Administrador Global deve fazer os seguintes passos:

1. Elevar o acesso à conta para que o Administrador Global possa atribuir funções. Para obter mais informações, consulte [o acesso da Elevate para gerir todas as subscrições e grupos de gestão da Azure.](../../role-based-access-control/elevate-access-global-admin.md)

1. Atribua proprietário ou colaborador ao principal que precisa de implementar os modelos.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

O principal tem agora as permissões necessárias para implementar o modelo.

## <a name="deployment-scopes"></a>Âmbitos de implantação

Ao ser destacado para um inquilino, pode direcionar o inquilino ou grupos de gestão, subscrições e grupos de recursos no arrendatário. O utilizador que implementa o modelo deve ter acesso ao âmbito especificado.

Os recursos definidos na secção de recursos do modelo são aplicados ao arrendatário.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

Para direcionar um grupo de gestão dentro do inquilino, adicione uma implantação aninhada e especifique o `scope` imóvel.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,22":::

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para implantações de inquilinos são diferentes dos comandos para implantações de grupos de recursos.

Para o Azure CLI, utilize [o inquilino de implantação az criar:](/cli/azure/deployment/tenant#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

Para a Azure PowerShell, utilize [o Novo AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

Para REST API, utilize [implementações - Crie ou atualize no âmbito do inquilino.](/rest/api/resources/deployments/createorupdateattenantscope)

## <a name="deployment-location-and-name"></a>Localização e nome de implantação

Para implantações de nível de inquilino, você deve fornecer uma localização para a implantação. A localização da implantação é separada da localização dos recursos que implementa. A localização da implantação especifica onde armazenar dados de implantação.

Pode fornecer um nome para a implementação ou utilizar o nome de implementação predefinido. O nome predefinido é o nome do ficheiro do modelo. Por exemplo, a implementação de um modelo denominado **azuredeploy.jscria** um nome de implementação padrão de **azuredeploy**.

Para cada nome de implantação, a localização é imutável. Não é possível criar uma implantação num local quando há uma implantação existente com o mesmo nome num local diferente. Se obter o código de erro `InvalidDeploymentLocation` , utilize um nome diferente ou o mesmo local que a colocação anterior para esse nome.

## <a name="use-template-functions"></a>Use funções de modelo

Para as implementações de inquilinos, existem algumas considerações importantes ao utilizar funções de modelo:

* A função [grupo de recursos()](template-functions-resource.md#resourcegroup) **não** é suportada.
* A função [de subscrição()](template-functions-resource.md#subscription) **não** é suportada.
* As funções [de referência](template-functions-resource.md#reference) e [lista são](template-functions-resource.md#list) suportadas.
* Não utilize [recursosId()](template-functions-resource.md#resourceid) para obter o ID de recursos para recursos que são implantados ao nível do inquilino.

  Em vez disso, utilize a função [TenantResourceId().](template-functions-resource.md#tenantresourceid)

  Por exemplo, para obter o ID de recurso para uma definição de política incorporada, use:

  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```

  O ID de recurso devolvido tem o seguinte formato:

  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Criar grupo de gestão

O [modelo a seguir](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg) cria um grupo de gestão.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>Atribuir função

O [modelo a seguir](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment) atribui uma função no âmbito do inquilino.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a atribuição de funções, consulte atribuições de [funções Add Azure utilizando modelos do Gestor de Recursos Azure](../../role-based-access-control/role-assignments-template.md).
* Também pode implementar modelos ao [nível de subscrição](deploy-to-subscription.md) ou [ao nível do grupo de gestão.](deploy-to-management-group.md)
