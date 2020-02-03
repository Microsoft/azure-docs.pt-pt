---
title: Automatizar a adição de um usuário de laboratório no Azure DevTest Labs | Microsoft Docs
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
ms.openlocfilehash: 70a6359923734c83590d4677bb2c93966c925d14
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718144"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatize a adição de um usuário de laboratório a um laboratório no Azure DevTest Labs
Azure DevTest Labs permite criar rapidamente ambientes de desenvolvimento e teste de autoatendimento usando o portal do Azure. No entanto, se você tiver várias equipes e várias instâncias do DevTest Labs, a automatização do processo de criação poderá poupar tempo. Os [modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) permitem criar laboratórios, VMs de laboratório, imagens personalizadas, fórmulas e adicionar utilizadores de forma automatizada. Este artigo se concentra especificamente na adição de usuários a uma instância do DevTest Labs.

Para adicionar um utilizador a um laboratório, adicione o utilizador à função de Utilizador do **DevTest Labs** para o laboratório. Este artigo mostra como automatizar a adição de um usuário a um laboratório usando uma das seguintes maneiras:

- Modelos do Azure Resource Manager
- Cmdlets do Azure PowerShell 
- CLI do Azure.

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

Se você estiver atribuindo a função no mesmo modelo que está criando o laboratório, lembre-se de adicionar uma dependência entre o recurso de atribuição de função e o laboratório. Para mais informações, consulte a [Definição de dependências no](../azure-resource-manager/templates/define-resource-dependency.md) artigo modelos de gestor de recursos do Azure.

### <a name="role-assignment-resource-information"></a>Informações de recurso de atribuição de função
O recurso de atribuição de função precisa especificar o tipo e o nome.

A primeira coisa a notar é que o tipo para o recurso não é `Microsoft.Authorization/roleAssignments` como seria para um grupo de recursos.  Em vez disso, o tipo de recurso segue o padrão `{provider-namespace}/{resource-type}/providers/roleAssignments`. Neste caso, o tipo de recurso será `Microsoft.DevTestLab/labs/providers/roleAssignments`.

O nome da atribuição de função em si precisa ser globalmente exclusivo.  O nome da atribuição usa o padrão `{labName}/Microsoft.Authorization/{newGuid}`. O `newGuid` é um valor de parâmetro para o modelo. Ele garante que o nome da atribuição de função seja exclusivo. Como não há nenhuma função de modelo para criar GUIDs, você precisa gerar um GUID por conta própria usando qualquer ferramenta de gerador de GUID.  

No modelo, o nome para a atribuição de funções é definido pela variável `fullDevTestLabUserRoleName`. A linha exata do modelo é:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Propriedades do recurso de atribuição de função
Uma atribuição de função em si define três propriedades. Precisa da `roleDefinitionId`, `principalId`e `scope`.

### <a name="role-definition"></a>Definição de função
A ID de definição de função é o identificador de cadeia de caracteres para a definição de função existente. O papel id está na forma `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

O ID de subscrição é obtido utilizando `subscription().subscriptionId` função de modelo.  

É preciso ter a definição de papel para o papel `DevTest Labs User` incorporado. Para obter o GUID para a função [de Utilizador de DevTest Labs,](../role-based-access-control/built-in-roles.md#devtest-labs-user) pode utilizar as Atribuições de [Funções REST API](/rest/api/authorization/roleassignments) ou o cmdlet [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0)

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

O ID de função é definido na secção variáveis e nomeado `devTestLabUserRoleId`. No modelo, a ID da função é definida como: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>ID da entidade
ID da entidade de segurança é a ID de objeto do Active Directory usuário, grupo ou entidade de serviço que você deseja adicionar como um usuário do laboratório ao laboratório. O modelo usa o `ObjectId` como parâmetro.

Pode obter o ObjectId utilizando os Cmdlets [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup ou [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell. Esses cmdlets retornam uma única lista de Active Directory objetos que têm uma propriedade de ID, que é a ID de objeto de que você precisa. O exemplo a seguir mostra como obter a ID de objeto de um único usuário em uma empresa.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Também pode utilizar os cmdlets PowerShell do Diretório Ativo Azure que incluem [Get-MsolUser,](/powershell/module/msonline/get-msoluser?view=azureadps-1.0) [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)e [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Âmbito
Escopo Especifica o recurso ou grupo de recursos para o qual a atribuição de função deve ser aplicada. Para os recursos, o âmbito está na forma: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. O modelo utiliza a função `subscription().subscriptionId` para preencher a parte `subscription-id` e a função do modelo `resourceGroup().name` para preencher a parte `resource-group-name`. O uso dessas funções significa que o laboratório ao qual você está atribuindo uma função deve existir na assinatura atual e no mesmo grupo de recursos no qual a implantação do modelo é feita. A última parte, `resource-name`, é o nome do laboratório. Esse valor é recebido por meio do parâmetro de modelo neste exemplo. 

O escopo da função no modelo: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Implementar o modelo
Primeiro, crie um arquivo de parâmetro (por exemplo: azuredeploy. Parameters. JSON) que transmita valores para parâmetros no modelo do Resource Manager. 

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

Em seguida, utilize o cmdlet PowerShell powerShell de implantação do [New-AzureRmResourceGroup para](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) implementar o modelo de Gestor de Recursos. O comando de exemplo a seguir atribui uma pessoa, um grupo ou uma entidade de serviço à função de usuário do DevTest Labs para um laboratório.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

É importante observar que o nome da implantação do grupo e o GUID de atribuição de função precisam ser exclusivos. Se tentar implementar uma atribuição de recursos com um GUID não único, terá um erro `RoleAssignmentUpdateNotPermitted`.

Se você planeja usar o modelo várias vezes para adicionar vários objetos Active Directory à função de usuário do DevTest Labs para seu laboratório, considere o uso de objetos dinâmicos no comando do PowerShell. O exemplo que se segue utiliza o cmdlet [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) para especificar o nome de implantação do grupo de recursos e a atribuição de funções GUID dinamicamente.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Conforme discutido na introdução, cria-se uma nova função azure para adicionar um utilizador à função de Utilizador de **DevTest Labs** para o laboratório. Na PowerShell, fá-lo utilizando o cmdlet [New-AzureRMRoleAssignment.](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) Esse cmdlet tem muitos parâmetros opcionais para permitir a flexibilidade. A `ObjectId`, `SigninName`ou `ServicePrincipalName` pode ser especificada como permissão do objeto.  

Aqui está um exemplo Azure PowerShell comando que adiciona um usuário à função de usuário do DevTest Labs no laboratório especificado.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Especificar o recurso a que estão a ser concedidas permissões pode ser especificado por uma combinação de `ResourceName`, `ResourceType`, `ResourceGroup` ou pelo parâmetro `scope`. Qualquer combinação de parâmetros é usada, forneça informações suficientes ao cmdlet para identificar exclusivamente o objeto de Active Directory (usuário, grupo ou entidade de serviço), escopo (grupo de recursos ou recurso) e definição de função.

## <a name="use-azure-command-line-interface-cli"></a>Usar a CLI (interface de linha de comando) do Azure
No Azure CLI, adicionar um utilizador de laboratório a um laboratório é feito usando o comando `az role assignment create`. Para obter mais informações sobre os cmdlets Azure CLI, consulte [Gerir o acesso aos recursos Azure utilizando o RBAC e o Azure CLI](../role-based-access-control/role-assignments-cli.md).

O objeto que está a ser concedido pode ser especificado pelo `objectId`, `signInName`, `spn` parâmetros. O laboratório ao qual o objeto está a ser concedido pode ser identificado pelo url `scope` ou por uma combinação dos parâmetros `resource-name`, `resource-type`e `resource-group`.

O exemplo a seguir CLI do Azure mostra como adicionar uma pessoa à função de usuário do DevTest Labs para o laboratório especificado.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Crie e gereça máquinas virtuais com Laboratórios DevTest utilizando o Azure CLI](devtest-lab-vmcli.md)
- [Crie uma máquina virtual com Laboratórios DevTest usando o Azure PowerShell](devtest-lab-vm-powershell.md)
- [Utilize ferramentas de linha de comando para iniciar e parar máquinas virtuais Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

