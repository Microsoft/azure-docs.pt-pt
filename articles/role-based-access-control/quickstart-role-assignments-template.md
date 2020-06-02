---
title: 'Quickstart: Adicione uma atribuição de função Azure usando um modelo de Gestor de Recursos Azure - Azure RBAC'
description: Saiba como conceder acesso aos recursos Azure para um utilizador no âmbito do grupo de recursos utilizando modelos de Gestor de Recursos Azure e controlo de acesso baseado em funções Azure (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.custom: subject-armqs
ms.workload: identity
ms.date: 05/21/2020
ms.author: rolyon
ms.openlocfilehash: 4cb25b0398dca05f32f944962e2fc8630ea10870
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267652"
---
# <a name="quickstart-add-an-azure-role-assignment-using-an-azure-resource-manager-template"></a>Quickstart: Adicione uma atribuição de função Azure usando um modelo de Gestor de Recursos Azure

[O controlo de acesso baseado em funções Azure (Azure RBAC)](overview.md) é a forma como gere o acesso aos recursos do Azure. Neste arranque rápido, cria-se um grupo de recursos e concede-se a um utilizador acesso para criar e gerir máquinas virtuais no grupo de recursos. Este quickstart utiliza um modelo de Gestor de Recursos para conceder o acesso.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar atribuições de funções, você deve ter:

* `Microsoft.Authorization/roleAssignments/write`e `Microsoft.Authorization/roleAssignments/delete` permissões, tais como [Administrador de Acesso ao Utilizador](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)

## <a name="create-a-role-assignment"></a>Criar uma atribuição de funções

Para adicionar uma atribuição de funções, deve especificar três elementos: principal de segurança, definição de função e âmbito. Para este arranque rápido, o principal de segurança é você ou outro utilizador no seu diretório, a definição de função é [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor), e o âmbito é um grupo de recursos que especifica.

### <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). O modelo tem três parâmetros e uma secção de recursos. Na secção de recursos, note que tem os três elementos de uma atribuição de funções: principal de segurança, definição de função e âmbito. 

:::code language="json" source="~/quickstart-templates/101-rbac-builtinrole-resourcegroup/azuredeploy.json" highlight="30-32":::

### <a name="deploy-the-template"></a>Implementar o modelo

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Determine o seu endereço de e-mail que esteja associado à sua subscrição Azure. Ou determine o endereço de e-mail de outro utilizador no seu diretório.

1. Abra a casca de nuvem Azure para powerShell.

1. Copie e cole o seguinte script na Cloud Shell.

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter a resource group name (i.e. ExampleGrouprg)"
    $emailAddress = Read-Host -Prompt "Enter an email address for a user in your directory"
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    
    $roleAssignmentName = New-Guid
    $principalId = (Get-AzAdUser -Mail $emailAddress).id
    $roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"
    
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
    ```

1. Introduza um nome de grupo de recursos como ExemploGrouprg.

1. Insira um endereço de e-mail para si ou para outro utilizador no seu diretório.

1. Introduza uma localização para o grupo de recursos como central.

1. Se necessário, prima Enter para executar o comando New-AzResourceGroupDeployment.

    O comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cria um novo grupo de recursos e o comando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) implementa o modelo para adicionar a atribuição de funções.

    Deverá ver um resultado semelhante ao seguinte:

    ```azurepowershell
    PS> New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
    
    DeploymentName          : azuredeploy
    ResourceGroupName       : ExampleGrouprg
    ProvisioningState       : Succeeded
    Timestamp               : 5/22/2020 9:01:30 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name                  Type                       Value
                              ====================  =========================  ==========
                              roleAssignmentName    String                     {roleAssignmentName}
                              roleDefinitionID      String                     9980e02c-c2be-4d73-94e8-173b1dc7cf3c
                              principalId           String                     {principalId}
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. No portal Azure, abra o grupo de recursos que criou.

1. No menu esquerdo, clique no **controlo de acesso (IAM)**.

1. Clique no separador **Atribuições de funções**.

1. Verifique se a função **contribuidor da máquina virtual** é atribuída ao utilizador especificado.

   ![Nova atribuição de função](./media/quickstart-role-assignments-template/role-assignment-portal.png)

## <a name="clean-up-resources"></a>Limpar recursos

Para remover a atribuição de funções e o grupo de recursos que criou, siga estes passos.

1. Copie e cole o seguinte script na Cloud Shell.

    ```azurepowershell
    $emailAddress = Read-Host -Prompt "Enter the email address of the user with the role assignment to remove"
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name to remove (i.e. ExampleGrouprg)"
    
    $principalId = (Get-AzAdUser -Mail $emailAddress).id
    
    Remove-AzRoleAssignment -ObjectId $principalId -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName $resourceGroupName
    Remove-AzResourceGroup -Name $resourceGroupName
    ```
    
1. Insira o endereço de e-mail do utilizador com a atribuição de funções a remover.

1. Introduza o nome do grupo de recursos para remover tal como o ExampleGrouprg.

1. Se necessário, prima Enter para executar o comando Remove-AzResourceGroup.

1. Insira **Y** para confirmar que deseja remover o grupo de recursos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Conceder acesso a um utilizador aos recursos da Azure utilizando a Azure PowerShell](tutorial-role-assignments-user-powershell.md)