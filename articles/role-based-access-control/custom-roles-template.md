---
title: Crie um papel personalizado Azure usando um modelo de Gestor de Recursos Azure - Azure RBAC
description: Saiba como criar um papel personalizado Azure usando modelos de Gestor de Recursos Azure e controlo de acesso baseado em funções Azure (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: bcf1966ffc326291448cb611d99390fe0d652151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85392979"
---
# <a name="create-an-azure-custom-role-using-an-azure-resource-manager-template"></a>Crie um papel personalizado Azure usando um modelo de Gestor de Recursos Azure

Se os [papéis incorporados do Azure](built-in-roles.md) não corresponderem às necessidades específicas da sua organização, pode criar os seus [próprios papéis personalizados.](custom-roles.md) Este artigo descreve como criar um papel personalizado usando um modelo de Gestor de Recursos Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para criar um papel personalizado, você deve ter:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Utilizador](built-in-roles.md#user-access-administrator)

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar um papel personalizado, especifica um nome de função, permissões e onde a função pode ser usada. Neste artigo, cria uma função chamada "Custom Role - RG Reader" com permissões de recursos que podem ser atribuídas num âmbito de subscrição ou inferior.

### <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste artigo é de [Azure Quickstart Templates](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-role-def). O modelo tem quatro parâmetros e uma secção de recursos. Os quatro parâmetros são:

- Conjunto de ações com um valor predefinido de ["Microsoft.Resources/subscrições/resourceGroups/read"]
- Matriz de nãoActions com um valor padrão vazio
- Nome de função com um valor padrão de "Função Personalizada - Leitor de RG"
- Descrição da função com um valor predefinido de "Implementação do Nível de Subscrição de uma Definição de Função"

O recurso definido no modelo é:

- [Microsoft.Autorização/funDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

O âmbito onde esta função personalizada pode ser atribuída é definido para a subscrição atual.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

### <a name="deploy-the-template"></a>Implementar o modelo

Siga estes passos para implementar o modelo anterior.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Abra a casca de nuvem Azure para powerShell.

1. Copie e cole o seguinte script na Cloud Shell.

    ```azurepowershell
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Introduza um local para a implantação como *central*.

1. Introduza uma lista de ações para o papel personalizado como uma lista separada por vírgula, como *Microsoft.Resources/resources/read,Microsoft.Resources/subscriptions/resourceGroups/read*.

1. Se necessário, prima Enter para executar o comando New-AzDeployment.

    O comando [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) implementa o modelo para criar o papel personalizado.

    Deverá ver um resultado semelhante ao seguinte:

    ```azurepowershell
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    
    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Siga estes passos para verificar se o papel personalizado foi criado.

1. Executar o comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) para listar o papel personalizado.

    ```azurepowershell
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    Deverá ver um resultado semelhante ao seguinte:

    ```azurepowershell
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. No portal Azure, abra a sua subscrição.

1. No menu esquerdo, clique no **controlo de acesso (IAM)**.

1. Clique no **separador Papéis.**

1. Deslote a lista **de tipo** para **CustomRole**.

1. Verifique se a **função De Função Personalizada - Leitor RG** está listada.

   ![Novo papel personalizado no portal Azure](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover o papel personalizado, siga estes passos.

1. Executar o seguinte comando para remover o papel personalizado.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Insira **Y** para confirmar que deseja remover a função personalizada.

## <a name="next-steps"></a>Passos seguintes

- [Compreender definições de função Azure](role-definitions.md)
- [Quickstart: Adicione uma atribuição de função Azure usando um modelo de Gestor de Recursos Azure](quickstart-role-assignments-template.md)
- [Documentação do modelo do ARM](../azure-resource-manager/templates/index.yml)
