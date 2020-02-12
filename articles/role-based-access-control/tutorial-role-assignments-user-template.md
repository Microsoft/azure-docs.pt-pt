---
title: 'Tutorial: Conceder acesso ao utilizador aos recursos Azure com modelo RBAC e Gestor de Recursos'
description: Aprenda a conceder a um utilizador o acesso aos recursos Do Azure utilizando o controlo de acesso baseado em papéis (RBAC) utilizando o modelo do Gestor de Recursos Azure neste tutorial.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: 96ca4f65d2def5f5004388c533410f09cc2a71fa
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138207"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Tutorial: Conceder ao utilizador acesso aos recursos Azure utilizando o modelo RBAC e Gestor de Recursos

[O controlo de acesso baseado em funções (RBAC)](overview.md) é a forma como gere o acesso aos recursos do Azure. Neste tutorial, você cria um grupo de recursos e concede ao utilizador acesso para criar e gerir máquinas virtuais no grupo de recursos. Este tutorial centra-se no processo de implementação de um modelo de Gestor de Recursos para conceder o acesso. Para obter mais informações sobre o desenvolvimento de modelos de Gestor de Recursos, consulte a [documentação do Gestor de Recursos](/azure/azure-resource-manager/) e a referência do [modelo](/azure/templates/microsoft.authorization/allversions
).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceder acesso a um utilizador num âmbito de grupo de recursos
> * Validar a implementação
> * Limpeza

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar e remover atribuições de funções, deve ter:

* `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete` permissões, tais como [Administrador de Acesso ao Utilizador](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)

## <a name="grant-access"></a>Conceder acesso

O modelo utilizado neste quickstart é de [modelos de quickstart Azure](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Modelos relacionados com autorização Azure podem ser encontrados [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Para implementar o modelo, selecione **Experimente-o** para abrir a concha Azure Cloud e, em seguida, colar o seguinte script PowerShell na janela da concha. Para colar o código, clique à direita na janela da concha e, em seguida, **selecione Pasta**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Validar a implementação

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos criado no último procedimento. O nome padrão é o nome do projeto com **rg** anexado.
1. Selecione **Controlo de acesso (IAM)** no menu da esquerda.
1. Selecione **Atribuições de funções**. 
1. In **Name**, insira o endereço de e-mail que digitou no último procedimento. Verá que o utilizador com o endereço de e-mail tem a função **de Colaborador de Máquina Virtual.**

## <a name="clean-up"></a>Limpeza

Para remover o grupo de recursos criado no último procedimento, selecione **Experimente-o** para abrir a concha Azure Cloud e, em seguida, colar o seguinte script PowerShell na janela da concha.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Conceder ao utilizador acesso aos recursos Azure utilizando o RBAC e o Azure PowerShell](tutorial-role-assignments-user-powershell.md)