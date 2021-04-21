---
title: Mobilizar recursos para inquilino
description: Descreve como implantar recursos no âmbito do inquilino num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 0b17b8741d1701720de86d8039be3b6cd28ace5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781906"
---
# <a name="tenant-deployments-with-arm-templates"></a>Implantações de inquilinos com modelos ARM

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

Para criar subscrições, utilize:

* [pseudónimos](/azure/templates/microsoft.subscription/aliases)

Para gerir custos, utilize:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [instruções](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [faturaSecções](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

Para configurar o portal, utilize:

* [inquilinoConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>Esquema

O esquema que você usa para implantações de inquilinos é diferente do esquema para implantações de grupos de recursos.

Para modelos, use:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

O esquema para um ficheiro de parâmetro é o mesmo para todos os âmbitos de implantação. Para ficheiros de parâmetros, utilize:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
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

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para implantações de inquilinos são diferentes dos comandos para implantações de grupos de recursos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para o Azure CLI, utilize [o inquilino de implantação az criar:](/cli/azure/deployment/tenant#az_deployment_tenant_create)

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para a Azure PowerShell, utilize [o Novo AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

Para obter informações mais detalhadas sobre comandos de implantação e opções para a implementação de modelos ARM, consulte:

* [Implementar recursos com modelos ARM e portal Azure](deploy-portal.md)
* [Implementar recursos com modelos ARM e Azure CLI](deploy-cli.md)
* [Implementar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md)
* [Implementar recursos com modelos ARM e AZure Resource Manager REST API](deploy-rest.md)
* [Use um botão de implementação para implementar modelos do repositório GitHub](deploy-to-azure-button.md)
* [Implementar modelos ARM da Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Localização e nome de implantação

Para implantações de nível de inquilino, você deve fornecer uma localização para a implantação. A localização da implantação é separada da localização dos recursos que implementa. A localização da implantação especifica onde armazenar dados de implantação. As implementações de grupos [de subscrição](deploy-to-subscription.md) e [gestão](deploy-to-management-group.md) também requerem uma localização. Para implementações [de grupos](deploy-to-resource-group.md) de recursos, a localização do grupo de recursos é usada para armazenar os dados de implantação.

Pode fornecer um nome para a implementação ou utilizar o nome de implementação predefinido. O nome predefinido é o nome do ficheiro do modelo. Por exemplo, a implementação de um modelo denominado _azuredeploy.jscria_ um nome de implementação padrão de **azuredeploy**.

Para cada nome de implantação, a localização é imutável. Não é possível criar uma implantação num local quando há uma implantação existente com o mesmo nome num local diferente. Por exemplo, se criar uma implantação de inquilino com o nome **implantado1** em **central,** não pode mais tarde criar outra implantação com o nome **de implantação1,** mas uma localização de **Westus**. Se obter o código de erro `InvalidDeploymentLocation` , utilize um nome diferente ou o mesmo local que a colocação anterior para esse nome.

## <a name="deployment-scopes"></a>Âmbitos de implantação

Ao ser destacado para um inquilino, pode mobilizar recursos para:

* o inquilino
* grupos de gestão dentro do inquilino
* assinaturas
* grupos de recursos

Um [recurso de extensão](scope-extension-resources.md) pode ser traçado para um alvo diferente do alvo de implantação.

O utilizador que implementa o modelo deve ter acesso ao âmbito especificado.

Esta secção mostra como especificar diferentes âmbitos. Você pode combinar estes diferentes âmbitos em um único modelo.

### <a name="scope-to-tenant"></a>Âmbito para inquilino

Os recursos definidos na secção de recursos do modelo são aplicados ao arrendatário.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Âmbito para grupo de gestão

Para direcionar um grupo de gestão dentro do inquilino, adicione uma implantação aninhada e especifique o `scope` imóvel.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Âmbito de subscrição

Também pode direcionar as subscrições dentro do inquilino. O utilizador que implementa o modelo deve ter acesso ao âmbito especificado.

Para direcionar uma subscrição dentro do inquilino, use uma implantação aninhada e a `subscriptionId` propriedade.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Âmbito para grupo de recursos

Você também pode direcionar grupos de recursos dentro do inquilino. O utilizador que implementa o modelo deve ter acesso ao âmbito especificado.

Para direcionar um grupo de recursos dentro do inquilino, use uma implantação aninhada. Desa estação `subscriptionId` e `resourceGroup` propriedades. Não desloque um local para a implantação aninhada porque está implantado na localização do grupo de recursos.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>Criar grupo de gestão

O modelo a seguir cria um grupo de gestão.

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

Se a sua conta não tiver permissão para implantar no inquilino, ainda pode criar grupos de gestão implantando-se para outro âmbito. Para mais informações, consulte [o Grupo de Gestão.](deploy-to-management-group.md#management-group)

## <a name="assign-role"></a>Atribuir função

O modelo a seguir atribui uma função no âmbito do inquilino.

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a atribuição de funções, consulte atribuições de [funções Add Azure utilizando modelos do Gestor de Recursos Azure](../../role-based-access-control/role-assignments-template.md).
* Também pode implementar modelos ao [nível de subscrição](deploy-to-subscription.md) ou [ao nível do grupo de gestão.](deploy-to-management-group.md)
