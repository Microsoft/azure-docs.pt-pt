---
title: Automatizar a adição de um utilizador de laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como automatizar a adição de um utilizador de laboratório para um laboratório no Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 0eed874d405fcf99241a702292f8ceadae6c5a07
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65502034"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatizar a adição de um utilizador de laboratório para um laboratório no Azure DevTest Labs
Os laboratórios DevTest do Azure permite-lhe criar rapidamente ambientes de desenvolvimento e teste self-service com o portal do Azure. No entanto, se tiver várias equipes e várias instâncias do DevTest Labs, automatizar o processo de criação pode poupar tempo. [Modelos Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) permitem-lhe criar laboratórios, as VMs do laboratório, imagens personalizadas, fórmulas e adicionar utilizadores de forma automática. Este artigo concentra-se especificamente sobre como adicionar utilizadores a uma instância de DevTest Labs.

Para adicionar um utilizador a um laboratório, adicione o utilizador para o **utilizador de DevTest Labs** função para o laboratório. Este artigo mostra-lhe como automatizar a adição de um utilizador a um laboratório através de uma das seguintes formas:

- Modelos do Azure Resource Manager
- Cmdlets do Azure PowerShell 
- CLI do Azure.

## <a name="use-azure-resource-manager-templates"></a>Use Azure Resource Manager templates (Utilizar modelos do Azure Resource Manager)
O modelo do Resource Manager de exemplo seguinte especifica um usuário a ser adicionado para o **utilizador de DevTest Labs** função de um laboratório. 

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

Se estiver a atribuir a função no mesmo modelo em que está a criar o laboratório, não se esqueça de adicionar uma dependência entre os recursos de atribuição de função e o laboratório. Para obter mais informações, consulte [definir dependências nos modelos do Azure Resource Manager](/azure-resource-manager/resource-group-define-dependencies.md) artigo.

### <a name="role-assignment-resource-information"></a>Informações de recursos de atribuição de função
O recurso de atribuição de função tem de especificar o tipo e nome.

A primeira coisa a observar é que o tipo para o recurso não é `Microsoft.Authorization/roleAssignments` como seria para um grupo de recursos.  Em vez disso, o tipo de recurso segue o padrão `{provider-namespace}/{resource-type}/providers/roleAssignments`. Neste caso, o tipo de recurso será `Microsoft.DevTestLab/labs/providers/roleAssignments`.

O nome da atribuição de função em si tem de ser globalmente exclusivo.  O nome da atribuição usa o padrão de `{labName}/Microsoft.Authorization/{newGuid}`. O `newGuid` é um valor de parâmetro para o modelo. Ele garante que o nome da atribuição de função é exclusivo. Como existem não existem funções de modelo para a criação de GUIDs, terá de gerar um GUID por conta própria usando qualquer ferramenta de gerador GUID.  

No modelo, o nome para a atribuição de função é definido pelo `fullDevTestLabUserRoleName` variável. A linha exata do modelo é:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Propriedades de recursos de atribuição de função
Uma atribuição de função em si define três propriedades. Ele precisa da `roleDefinitionId`, `principalId`, e `scope`.

### <a name="role-definition"></a>Definição de Função
O ID de definição de função é o identificador de cadeia de caracteres para a definição de função existente. A função do ID é o formato `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

A subscrição do ID é obtido através da utilização `subscription().subscriptionId` função de modelo.  

Tem de obter a definição de função para o `DevTest Labs User` função incorporada. Para obter o GUID para o [utilizador de DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) função, pode usar o [API de REST de atribuições de função](/rest/api/authorization/roleassignments) ou o [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) cmdlet.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

O ID da função é definido na secção de variáveis e com o nome `devTestLabUserRoleId`. No modelo, o ID de função está definido como: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>ID de Principal
ID de principal é o ID de objeto do utilizador do Active Directory, grupo ou principal de serviço que pretende adicionar como um utilizador de laboratório para o laboratório. O modelo utiliza o `ObjectId` como um parâmetro.

Pode obter o ObjectId com o [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup, ou [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) cmdlets do PowerShell. Estes cmdlets devolvem um único ou listas de objetos do Active Directory que tem uma propriedade de ID, o que é o ID de objeto que precisa. O exemplo seguinte mostra como obter o ID de objeto de um único utilizador numa empresa.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Também pode utilizar os cmdlets do Azure Active Directory PowerShell que incluem [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0), e [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Scope
Âmbito Especifica o recurso ou grupo de recursos para os quais a atribuição de função deve ser aplicada. Para obter recursos, o escopo é o formato: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. O modelo utiliza a `subscription().subscriptionId` função para preencher a `subscription-id` parte e o `resourceGroup().name` função de modelo para preencher o `resource-group-name` parte. O uso dessas funções significa que o laboratório ao qual está a atribuir uma função tem de existir na subscrição atual e o mesmo grupo de recursos para o qual é feita a implementação do modelo. A última parte, `resource-name`, é o nome do laboratório. Este valor é recebido por meio do parâmetro de modelo neste exemplo. 

Âmbito da função de no modelo: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Implementar o modelo
Primeiro, crie um ficheiro de parâmetros (por exemplo: azuredeploy) que transmite valores para parâmetros no modelo do Resource Manager. 

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

Em seguida, utilize o [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) cmdlet do PowerShell para implementar o modelo do Resource Manager. O comando de exemplo seguinte atribui uma pessoa, grupo ou um principal de serviço à função de utilizador de DevTest Labs para um laboratório.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

É importante observar que a implementação nome e a função de atribuição de grupo GUID tem de ser exclusivo. Se tentar implementar uma atribuição de recursos com um GUID não exclusivo, então obterá um `RoleAssignmentUpdateNotPermitted` erro.

Se planeja usar o modelo várias vezes para adicionar vários objetos do Active Directory à função de utilizador de DevTest Labs para seu laboratório, considere utilizar objetos dinâmicos em seu comando do PowerShell. O exemplo seguinte utiliza a [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) cmdlet para especificar o recurso implantação nome e a função de atribuição de grupo GUID dinamicamente.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Como discutido na introdução, cria uma nova atribuição de função do Azure para adicionar um utilizador para o **utilizador de DevTest Labs** função para o laboratório. No PowerShell, faça isso usando o [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) cmdlet. Este cmdlet tem muitos parâmetros opcionais para permitir flexibilidade. O `ObjectId`, `SigninName`, ou `ServicePrincipalName` pode ser especificado como o objeto a ser concedido permissões.  

Eis um comando do PowerShell do Azure de exemplo que adiciona um utilizador à função de utilizador de DevTest Labs no laboratório especificado.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Para especificar o recurso para os quais permissões estão a ser concedido pode ser especificado por uma combinação de `ResourceName`, `ResourceType`, `ResourceGroup` ou o `scope` parâmetro. Combinação de parâmetros é usada, fornece informações suficientes para o cmdlet para identificar exclusivamente o objeto do Active Directory (utilizador, grupo ou principal de serviço), o âmbito (grupo de recursos ou recurso) e a definição de função.

## <a name="use-azure-command-line-interface-cli"></a>Utilizar a Interface de linha de comandos (CLI) do Azure
Na CLI do Azure, a adição de um utilizador de laboratórios a um laboratório é feita usando o `az role assignment create` comando. Para obter mais informações sobre os cmdlets do CLI do Azure, consulte [gerir o acesso aos recursos do Azure através do RBAC e CLI do Azure](../role-based-access-control/role-assignments-cli.md).

O objeto que está sendo concedido acesso pode ser especificado pela `objectId`, `signInName`, `spn` parâmetros. O laboratório ao qual o objeto está sendo concedido acesso pode ser identificado com o `scope` url ou uma combinação da `resource-name`, `resource-type`, e `resource-group` parâmetros.

O exemplo seguinte da CLI do Azure mostra-lhe como adicionar uma pessoa para a função de utilizador de DevTest Labs para o laboratório especificado.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Criar e gerir máquinas virtuais com o DevTest Labs com a CLI do Azure](devtest-lab-vmcli.md)
- [Criar uma máquina virtual com o DevTest Labs com o Azure PowerShell](devtest-lab-vm-powershell.md)
- [Utilize ferramentas de linha de comandos para iniciar e parar máquinas de virtuais do Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

