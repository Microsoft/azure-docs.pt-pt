---
title: Tutorial - conceder acesso de um utilizador aos recursos do Azure com o modelo RBAC e do Resource Manager | Documentos da Microsoft
description: Saiba como conceder um acesso de utilizador para recursos do Azure com o controlo de acesso baseado em funções (RBAC) com o modelo Azure Resource Manager.
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
ms.openlocfilehash: edb20221862e6439b3bc574995f4037cbc95f8f9
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668857"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Tutorial: Conceder um acesso de utilizador aos recursos do Azure com o modelo RBAC e do Resource Manager

[Controlo de acesso baseado em funções (RBAC)](overview.md) é a maneira que gerencie o acesso aos recursos do Azure. Neste tutorial, crie um grupo de recursos e conceder um acesso de utilizador para criar e gerir máquinas virtuais no grupo de recursos. Este tutorial concentra-se sobre o processo de implantação de um modelo do Resource Manager para conceder o acesso. Para obter mais informações sobre como desenvolver modelos do Resource Manager, consulte [documentação do Resource Manager](/azure/azure-resource-manager/) e o [referência de modelo](/azure/templates/microsoft.authorization/allversions
).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceder acesso para um utilizador com um âmbito de grupo de recursos
> * Validar a implementação
> * Limpeza

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar e remover atribuições de função, tem de ter:

* `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete` permissões, tal como [administrador de acesso de utilizador](built-in-roles.md#user-access-administrator) ou [proprietário](built-in-roles.md#owner)

## <a name="grant-access"></a>Conceder acesso

O modelo utilizado neste início rápido é partir [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Autorização mais Azure relacionadas com modelos podem ser encontrados [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Para implementar o modelo, selecione **experimente** para abrir o Azure Cloud shell e, em seguida, cole o seguinte script do PowerShell para a janela do shell. Colar o código, com o botão direito da janela do shell e, em seguida, selecione **colar**.

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

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos criado no último procedimento. O nome predefinido é o nome do projeto com **rg** anexado.
1. Selecione **Controlo de acesso (IAM)** no menu da esquerda.
1. Selecione **atribuições de funções**. 
1. Na **nome**, introduza o endereço de e-mail que escreveu no último procedimento. Deverá ver o utilizador com o endereço de e-mail tem o **contribuinte de Máquina Virtual** função.

## <a name="clean-up"></a>Limpeza

Para remover o grupo de recursos criado no último procedimento, selecione **experimente** para abrir o Azure Cloud shell e, em seguida, cole o seguinte script do PowerShell para a janela do shell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Conceder um acesso de utilizador aos recursos do Azure utilizando o RBAC e o Azure PowerShell](tutorial-role-assignments-user-powershell.md)