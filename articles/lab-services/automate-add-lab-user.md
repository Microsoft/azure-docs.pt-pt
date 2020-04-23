---
title: Automatizar adicionar um utilizador de laboratório em Azure DevTest Labs [ Microsoft Docs
description: Este artigo mostra-lhe como automatizar adicionar um utilizador a um laboratório em Azure DevTest Labs usando modelos de Gestor de Recursos Azure, PowerShell e CLI.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 81a8c5030f716246caf3dcd8b540bb47fcaf6520
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023627"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatizar adicionar um utilizador de laboratório a um laboratório em Azure DevTest Labs
A Azure DevTest Labs permite-lhe criar rapidamente ambientes de teste de dev self-service utilizando o portal Azure. No entanto, se tiver várias equipas e vários casos de DevTest Labs, automatizar o processo de criação pode economizar tempo. Os [modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) permitem criar laboratórios, VMs de laboratório, imagens personalizadas, fórmulas e adicionar utilizadores de forma automatizada. Este artigo foca-se especificamente em adicionar utilizadores a uma instância de DevTest Labs.

Para adicionar um utilizador a um laboratório, adicione o utilizador à função de Utilizador do **DevTest Labs** para o laboratório. Este artigo mostra-lhe como automatizar adicionar um utilizador a um laboratório usando uma das seguintes formas:

- Modelos do Azure Resource Manager
- Cmdlets do Azure PowerShell 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Use Azure Resource Manager templates (Utilizar modelos do Azure Resource Manager)
O seguinte modelo de Gestor de Recursos da amostra especifica um utilizador a ser adicionado ao papel de utilizador de Um laboratório de **Laboratórios DevTest.** 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Se você está atribuindo o papel no mesmo modelo que está criando o laboratório, lembre-se de adicionar uma dependência entre o recurso de atribuição de papel e o laboratório. Para mais informações, consulte a [Definição de dependências no](../azure-resource-manager/templates/define-resource-dependency.md) artigo modelos de gestor de recursos do Azure.

### <a name="role-assignment-resource-information"></a>Informação sobre recursos de atribuição de funções
O recurso de atribuição de funções precisa especificar o tipo e o nome.

A primeira coisa a notar é que o `Microsoft.Authorization/roleAssignments` tipo para o recurso não é como seria para um grupo de recursos.  Em vez disso, o `{provider-namespace}/{resource-type}/providers/roleAssignments`tipo de recurso segue o padrão . Neste caso, o tipo `Microsoft.DevTestLab/labs/providers/roleAssignments`de recurso será .

O nome de atribuição de papéis em si tem de ser globalmente único.  O nome da atribuição `{labName}/Microsoft.Authorization/{newGuid}`usa o padrão. O `newGuid` é um valor de parâmetro para o modelo. Garante que o nome de atribuição de papéis é único. Como não existem funções de modelo para criar GUIDs, você precisa gerar um GUID por si mesmo usando qualquer ferramenta geradora GUID.  

No modelo, o nome para a atribuição `fullDevTestLabUserRoleName` de funções é definido pela variável. A linha exata do modelo é:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Propriedades de recursos de atribuição de funções
Uma atribuição de funções em si define três propriedades. Precisa `roleDefinitionId`do. `principalId` `scope`

### <a name="role-definition"></a>Definição de Papel
O ID de definição de função é o identificador de cadeias para a definição de função existente. O id do papel `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`está na forma. 

O ID de subscrição `subscription().subscriptionId` é obtido utilizando a função de modelo.  

Tens de obter a definição de papel para o `DevTest Labs User` papel incorporado. Para obter o GUID para a função [de Utilizador de DevTest Labs,](../role-based-access-control/built-in-roles.md#devtest-labs-user) pode utilizar as Atribuições de [Funções REST API](/rest/api/authorization/roleassignments) ou o cmdlet [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0)

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

O ID de função é definido `devTestLabUserRoleId`na secção variáveis e nomeado . No modelo, o ID de função está definido para: 11111111-0000-0000-111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Principal ID
O ID principal é o id do utilizador, grupo ou diretor de serviço do Ative Directy que pretende adicionar como utilizador de laboratório ao laboratório. O modelo `ObjectId` usa o como parâmetro.

Pode obter o ObjectId utilizando os Cmdlets [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup ou [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell. Estes cmdlets devolvem uma única ou lista de objetos de Diretório Ativo que têm uma propriedade id, que é o ID do objeto que você precisa. O exemplo que se segue mostra como obter a identificação do objeto de um único utilizador numa empresa.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Também pode utilizar os cmdlets PowerShell do Diretório Ativo Azure que incluem [Get-MsolUser,](/powershell/module/msonline/get-msoluser?view=azureadps-1.0) [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)e [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Âmbito
O âmbito especifica o grupo de recursos ou recursos para o qual a atribuição de funções deve ser aplicada. Para os recursos, o âmbito `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`está na forma: . O modelo `subscription().subscriptionId` utiliza a função para preencher a `subscription-id` peça e a função do `resourceGroup().name` modelo para preencher a `resource-group-name` peça. Usar estas funções significa que o laboratório ao qual atribui uma função deve existir na subscrição atual e no mesmo grupo de recursos para o qual a implementação do modelo é feita. A última `resource-name`parte é o nome do laboratório. Este valor é recebido através do parâmetro do modelo neste exemplo. 

O âmbito de função no modelo: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Implementar o modelo
Em primeiro lugar, crie um ficheiro de parâmetro (por exemplo: azuredeploy.parâmetros.json) que passe valores para parâmetros no modelo de Gestor de Recursos. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Em seguida, utilize o cmdlet PowerShell powerShell de implantação do [New-AzureRmResourceGroup para](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) implementar o modelo de Gestor de Recursos. O comando de exemplo seguinte atribui uma pessoa, grupo ou um diretor de serviço à função de Utilizador de DevTest Labs para um laboratório.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

É importante notar que o nome de implantação do grupo e a atribuição de papéis GUID precisam ser únicos. Se tentar implementar uma atribuição de recursos com um GUID não `RoleAssignmentUpdateNotPermitted` único, terá um erro.

Se planeia utilizar o modelo várias vezes para adicionar vários objetos de Diretório Ativo à função de Utilizador do DevTest Labs para o seu laboratório, considere utilizar objetos dinâmicos no comando PowerShell. O exemplo que se segue utiliza o cmdlet [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) para especificar o nome de implantação do grupo de recursos e a atribuição de funções GUID dinamicamente.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Conforme discutido na introdução, cria-se uma nova função azure para adicionar um utilizador à função de Utilizador de **DevTest Labs** para o laboratório. Na PowerShell, fá-lo utilizando o cmdlet [New-AzureRMRoleAssignment.](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) Este cmdlet tem muitos parâmetros opcionais para permitir flexibilidade. `ObjectId`O, `SigninName`ou `ServicePrincipalName` pode ser especificado como o objeto que lhe é concedida permissão.  

Aqui está uma amostra do comando Azure PowerShell que adiciona um utilizador à função de Utilizador de DevTest Labs no laboratório especificado.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Especificar o recurso a que estão a ser concedidas `ResourceName` `ResourceType`permissões pode ser especificado por uma combinação de , ou `ResourceGroup` pelo `scope` parâmetro. Qualquer combinação de parâmetros, forneça informações suficientes ao cmdlet para identificar exclusivamente o objeto de Diretório Ativo (utilizador, grupo ou diretor de serviço), âmbito (grupo de recursos ou recurso) e definição de funções.

## <a name="use-azure-command-line-interface-cli"></a>Utilize interface de linha de comando Azure (CLI)
No Azure CLI, adicionar um utilizador de laboratório `az role assignment create` a um laboratório é feito usando o comando. Para obter mais informações sobre os cmdlets Azure CLI, consulte [Gerir o acesso aos recursos Azure utilizando o RBAC e o Azure CLI](../role-based-access-control/role-assignments-cli.md).

O objeto que está a ser concedido `objectId` `signInName`pode `spn` ser especificado pelos parâmetros. O laboratório a que o objeto está a `scope` ser concedido pode `resource-name`ser `resource-type`identificado `resource-group` pelo url ou por uma combinação dos, e parâmetros.

O exemplo do Azure CLI mostra-lhe como adicionar uma pessoa à função de Utilizador de DevTest Labs para o laboratório especificado.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Crie e gereça máquinas virtuais com Laboratórios DevTest utilizando o Azure CLI](devtest-lab-vmcli.md)
- [Crie uma máquina virtual com Laboratórios DevTest usando o Azure PowerShell](devtest-lab-vm-powershell.md)
- [Utilize ferramentas de linha de comando para iniciar e parar máquinas virtuais Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

