---
title: Tutorial – conceder a um usuário acesso aos recursos do Azure usando o modelo RBAC e Resource Manager
description: Saiba como conceder a um usuário acesso aos recursos do Azure usando o RBAC (controle de acesso baseado em função) usando o modelo Azure Resource Manager neste tutorial.
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
ms.openlocfilehash: ed143f85b4372348baa1d74b4ec7a7447943a74f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74418482"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Tutorial: conceder a um usuário acesso aos recursos do Azure usando o modelo RBAC e Resource Manager

O [RBAC (controle de acesso baseado em função)](overview.md) é a maneira como você gerencia o acesso aos recursos do Azure. Neste tutorial, você cria um grupo de recursos e concede a um usuário acesso para criar e gerenciar máquinas virtuais no grupo de recursos. Este tutorial se concentra no processo de implantação de um modelo do Resource Manager para conceder o acesso. Para obter mais informações sobre como desenvolver modelos do Resource Manager, consulte a [documentação do Resource Manager](/azure/azure-resource-manager/) e a [referência de modelo](/azure/templates/microsoft.authorization/allversions
).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceder acesso para um usuário em um escopo de grupo de recursos
> * Validar a implementação
> * Limpeza

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar e remover atribuições de função, você deve ter:

* permissões de `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) ou [proprietário](built-in-roles.md#owner)

## <a name="grant-access"></a>Conceder acesso

O modelo usado neste guia de início rápido é de [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Mais modelos relacionados à autorização do Azure podem ser encontrados [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Para implantar o modelo, selecione **Experimente-** o para abrir o Azure cloud Shell e cole o seguinte script do PowerShell na janela do Shell. Para colar o código, clique com o botão direito do mouse na janela do Shell e selecione **colar**.

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

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos criado no último procedimento. O nome padrão é o nome do projeto com **RG** acrescentado.
1. Selecione **Controlo de acesso (IAM)** no menu da esquerda.
1. Selecione **Atribuições de funções**. 
1. Em **nome**, insira o endereço de email que você digitou no último procedimento. Você deverá ver o usuário com o endereço de email com a função **colaborador da máquina virtual** .

## <a name="clean-up"></a>Limpeza

Para remover o grupo de recursos criado no último procedimento, selecione **Experimente-** o para abrir o Azure cloud Shell e cole o seguinte script do PowerShell na janela do Shell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: conceder a um usuário acesso aos recursos do Azure usando RBAC e Azure PowerShell](tutorial-role-assignments-user-powershell.md)