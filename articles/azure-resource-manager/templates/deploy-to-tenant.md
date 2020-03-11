---
title: Desloque recursos para o inquilino
description: Descreve como implementar recursos no âmbito do inquilino num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 64090f1a0bac4b2b5f18d8dec14be0c3b051ac17
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968885"
---
# <a name="create-resources-at-the-tenant-level"></a>Criar recursos ao nível dos inquilinos

À medida que a sua organização amadurece, poderá ter de definir e atribuir [políticas](../../governance/policy/overview.md) ou [controlos de acesso baseados em papéis](../../role-based-access-control/overview.md) em todo o seu inquilino DaD Azure. Com modelos de nível de inquilino, você pode aplicar declarativamente políticas e atribuir papéis a nível global.

## <a name="supported-resources"></a>Recursos suportados

Pode implementar os seguintes tipos de recursos ao nível do arrendatário:

* [implementações](/azure/templates/microsoft.resources/deployments) - para modelos aninhados que se desdobram em grupos de gestão ou subscrições.
* [políticasAtribuis](/azure/templates/microsoft.authorization/policyassignments)
* [definições políticas](/azure/templates/microsoft.authorization/policydefinitions)
* [definições políticasSetDefinições](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [definições de papel](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Esquema

O esquema que usa para implantações de inquilinos é diferente do esquema para implantações de grupos de recursos.

Para os modelos, utilize:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

O esquema para um ficheiro de parâmetros é o mesmo para todos os âmbitos de implantação. Para ficheiros de parâmetros, utilize:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Acesso obrigatório

O principal desdobramento do modelo deve ter permissões para criar recursos no âmbito do arrendatário. O diretor deve ter permissão para executar as ações de implantação (`Microsoft.Resources/deployments/*`) e para criar os recursos definidos no modelo. Por exemplo, para criar um grupo de gestão, o diretor deve ter autorização do Contribuinte no âmbito do arrendatário. Para criar atribuições de funções, o diretor deve ter permissão do Proprietário.

O Administrador Global do Diretório Ativo Azure não tem automaticamente permissão para atribuir funções. Para permitir a implantação do modelo no âmbito do arrendatário, o Administrador Global deve fazer os seguintes passos:

1. Elevar o acesso à conta para que o Administrador Global possa atribuir funções. Para mais informações, consulte [O Acesso Elevado para gerir todas as subscrições e grupos de gestão do Azure.](../../role-based-access-control/elevate-access-global-admin.md)

1. Atribuir proprietário ou colaborador ao principal que precisa de implementar os modelos.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

O diretor tem agora as permissões necessárias para implementar o modelo.

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para destacamentos de inquilinos são diferentes dos comandos para implantações de grupos de recursos.

Para a Azure PowerShell, utilize [new-azTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Para rest API, use [Implementações - Criar ou atualizar no âmbito](/rest/api/resources/deployments/createorupdateattenantscope)do inquilino .

## <a name="deployment-location-and-name"></a>Localização e nome de implantação

Para implementações de nível de inquilino, você deve fornecer um local para a implementação. A localização da implantação é separada da localização dos recursos que implementa. O local de implementação especifica onde armazenar dados de implementação.

Pode fornecer um nome para a implementação ou utilizar o nome de implementação predefinido. O nome padrão é o nome do ficheiro do modelo. Por exemplo, a implementação de um modelo chamado **azuredeploy.json** cria um nome de implantação padrão de **azuredeploy**.

Para cada nome de implantação, a localização é imutável. Não se pode criar uma implantação num local quando existe uma implantação existente com o mesmo nome num local diferente. Se obtê-lo com o código de erro `InvalidDeploymentLocation`, utilize um nome diferente ou o mesmo local que a implementação anterior para esse nome.

## <a name="use-template-functions"></a>Funções de modelo de utilização

Para as implementações de inquilinos, existem algumas considerações importantes ao utilizar funções de modelo:

* A função de [recurso Group()](template-functions-resource.md#resourcegroup) **não** é suportada.
* A função [de subscrição()](template-functions-resource.md#subscription) **não** é suportada.
* As funções de [referência](template-functions-resource.md#reference) e [lista](template-functions-resource.md#list) são suportadas.
* Utilize a função de [inquilinoResourceId()](template-functions-resource.md#tenantresourceid) para obter o ID de recursos que são implantados ao nível do arrendatário.

  Por exemplo, para obter o ID de recurso para uma definição de política, use:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  O ID de recurso devolvido tem o seguinte formato:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Criar grupo de gestão

O [seguinte modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) cria um grupo de gestão.

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

## <a name="assign-role"></a>Papel de atribuição

O [seguinte modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) atribui um papel no âmbito do inquilino.

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

* Para aprender sobre a atribuição de funções, consulte [Gerir o acesso aos recursos do Azure utilizando modelos RBAC e Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Também pode implementar modelos ao [nível de subscrição](deploy-to-subscription.md) ou ao nível do grupo de [gestão.](deploy-to-management-group.md)
