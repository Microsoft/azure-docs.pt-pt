---
title: Automatizar a adição de um utilizador de laboratório na Azure DevTest Labs | Microsoft Docs
description: Este artigo mostra-lhe como automatizar a adição de um utilizador a um laboratório em Azure DevTest Labs utilizando modelos de Gestor de Recursos Azure, PowerShell e CLI.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: dc5522cfe694f193b9bbeeb3145808a367a62c12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519406"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatizar a adição de um utilizador de laboratório a um laboratório em Azure DevTest Labs
A Azure DevTest Labs permite-lhe criar rapidamente ambientes de teste de autosserviço utilizando o portal Azure. No entanto, se tiver várias equipas e várias instâncias da DevTest Labs, automatizar o processo de criação pode economizar tempo. [Os modelos Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) permitem-lhe criar laboratórios, VMs de laboratório, imagens personalizadas, fórmulas e adicionar utilizadores de forma automatizada. Este artigo centra-se especificamente em adicionar utilizadores a uma instância de DevTest Labs.

Para adicionar um utilizador a um laboratório, adicione o utilizador à função **de Utilizador de DevTest Labs** para o laboratório. Este artigo mostra-lhe como automatizar a adição de um utilizador a um laboratório utilizando uma das seguintes formas:

- Modelos do Azure Resource Manager
- Cmdlets do Azure PowerShell 
- CLI do Azure.

## <a name="use-azure-resource-manager-templates"></a>Use Azure Resource Manager templates (Utilizar modelos do Azure Resource Manager)
O modelo seguinte do Gestor de Recursos de amostra especifica um utilizador a ser adicionado à função de utilizador de um laboratório da **DevTest Labs.** 

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

Se está a atribuir o papel no mesmo modelo que está a criar o laboratório, lembre-se de adicionar uma dependência entre o recurso de atribuição de funções e o laboratório. Para obter mais informações, consulte [Definindo dependências no artigo de Modelos do Gestor de Recursos Azure.](../azure-resource-manager/templates/define-resource-dependency.md)

### <a name="role-assignment-resource-information"></a>Informação sobre recursos de atribuição de funções
O recurso de atribuição de funções precisa de especificar o tipo e o nome.

A primeira coisa a notar é que o tipo para o recurso não é `Microsoft.Authorization/roleAssignments` como seria para um grupo de recursos.  Em vez disso, o tipo de recurso segue o `{provider-namespace}/{resource-type}/providers/roleAssignments` padrão. Neste caso, o tipo de recurso será `Microsoft.DevTestLab/labs/providers/roleAssignments` .

O nome da atribuição de papéis em si tem de ser globalmente único.  O nome da atribuição usa o `{labName}/Microsoft.Authorization/{newGuid}` padrão. É `newGuid` um valor de parâmetro para o modelo. Garante que o nome de atribuição de funções é único. Como não existem funções de modelo para criar GUIDs, você precisa gerar um GUID você mesmo usando qualquer ferramenta do gerador GUID.  

No modelo, o nome para a atribuição de funções é definido pela `fullDevTestLabUserRoleName` variável. A linha exata do modelo é:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Propriedades de recursos de atribuição de funções
Uma tarefa de função em si define três propriedades. Precisa `roleDefinitionId` `principalId` de, `scope` e.

### <a name="role-definition"></a>Definição de Função
O ID de definição de função é o identificador de cordas para a definição de função existente. O iD de papel está no `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}` formulário. 

O ID de subscrição é obtido utilizando a `subscription().subscriptionId` função de modelo.  

Tens de ter a definição de papel para o `DevTest Labs User` papel incorporado. Para obter o GUID para a função [de Utilizador DevTest Labs,](../role-based-access-control/built-in-roles.md#devtest-labs-user) pode utilizar a [API de Atribuições de Função](/rest/api/authorization/roleassignments) ou o cmdlet [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition)

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

O ID de função é definido na secção de variáveis e nomeado `devTestLabUserRoleId` . No modelo, o ID de função é definido para: 11111111-000-000-1111111111111111111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Principal ID
O ID principal é o iD de objeto do utilizador, grupo ou principal de serviço ativo que pretende adicionar como utilizador de laboratório ao laboratório. O modelo usa o `ObjectId` como parâmetro.

Pode obter o ObjectId utilizando os cmdletos [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup, ou [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell cmdlets. Estes cmdlets devolvem uma única ou lista de objetos do Ative Directory que têm uma propriedade de ID, que é o ID do objeto de que você precisa. O exemplo a seguir mostra como obter o ID do objeto de um único utilizador numa empresa.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Também pode utilizar os cmdlets PowerShell do Diretório Ativo Azure que incluem [Get-MsolUser,](/powershell/module/msonline/get-msoluser?view=azureadps-1.0) [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)e [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Âmbito
O âmbito especifica o grupo de recursos ou recursos para o qual a atribuição de funções deve ser aplicada. Para os recursos, o âmbito está na forma: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}` . O modelo usa a `subscription().subscriptionId` função para preencher a `subscription-id` peça e a `resourceGroup().name` função do modelo para preencher a `resource-group-name` peça. A utilização destas funções significa que o laboratório ao qual está a atribuir uma função deve existir na subscrição atual e no mesmo grupo de recursos a que a implementação do modelo é feita. A última parte `resource-name` é o nome do laboratório. Este valor é recebido através do parâmetro do modelo neste exemplo. 

O âmbito de função no modelo: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Implementar o modelo
Em primeiro lugar, crie um ficheiro de parâmetro (por exemplo: azuredeploy.parameters.js) que passe valores para parâmetros no modelo de Gestor de Recursos. 

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

Em seguida, utilize o cmdlet PowerShell do [New-AzureRmResourceGroup Para](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) implementar o modelo de Gestor de Recursos. O seguinte exemplo de comando atribui uma pessoa, grupo ou um diretor de serviço ao papel de Utilizador de DevTest Labs para um laboratório.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

É importante notar que o nome de implementação do grupo e a atribuição de funções GUID precisam de ser únicos. Se tentar implementar uma atribuição de recursos com um GUID não único, então terá um `RoleAssignmentUpdateNotPermitted` erro.

Se planeia utilizar o modelo várias vezes para adicionar vários objetos ative directory à função de Utilizador DevTest Labs para o seu laboratório, considere utilizar objetos dinâmicos no seu comando PowerShell. O exemplo a seguir utiliza o cmdlet [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid) para especificar o nome de implementação do grupo de recursos e a atribuição de funções GUID dinamicamente.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Como discutido na introdução, cria uma nova atribuição de funções Azure para adicionar um utilizador à função **de Utilizador de DevTest Labs** para o laboratório. No PowerShell, fá-lo utilizando o cmdlet [de assinatura New-AzureRMRoleAssignment.](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) Este cmdlet tem muitos parâmetros opcionais para permitir a flexibilidade. O `ObjectId` , ou pode ser especificado como o objeto que está a ser `SigninName` `ServicePrincipalName` autorizado.  

Aqui está uma amostra do comando Azure PowerShell que adiciona um utilizador à função de Utilizador DevTest Labs no laboratório especificado.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Especificar o recurso a que as permissões estão a ser concedidas pode ser especificado por uma combinação de `ResourceName` `ResourceType` , ou pelo `ResourceGroup` `scope` parâmetro. Qualquer que seja a combinação de parâmetros utilizados, forneça informações suficientes ao cmdlet para identificar de forma única o objeto Ative Directory (utilizador, grupo ou principal de serviço), âmbito (grupo de recursos ou recurso) e definição de função.

## <a name="use-azure-command-line-interface-cli"></a>Utilize a interface da linha de comando Azure (CLI)
No Azure CLI, adicionar um utilizador de laboratório a um laboratório é feito usando o `az role assignment create` comando. Para obter mais informações sobre os cmdlets Azure CLI, consulte [adicionar ou remover atribuições de funções Azure utilizando O Azure CLI](../role-based-access-control/role-assignments-cli.md).

O objeto a quem é concedido acesso pode ser especificado pelos `objectId` `signInName` `spn` parâmetros, parâmetros. O laboratório ao qual o objeto está a ser concedido pode ser identificado pela `scope` url ou uma combinação do , `resource-name` e `resource-type` `resource-group` parâmetros.

O exemplo Azure CLI que se segue mostra-lhe como adicionar uma pessoa à função de Utilizador DevTest Labs para o Laboratório especificado.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Criar e gerir máquinas virtuais com DevTest Labs usando o Azure CLI](devtest-lab-vmcli.md)
- [Crie uma máquina virtual com DevTest Labs usando Azure PowerShell](devtest-lab-vm-powershell.md)
- [Use ferramentas de linha de comando para iniciar e parar máquinas virtuais Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

