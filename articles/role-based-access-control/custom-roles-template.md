---
title: Criar ou atualizar funções personalizadas Azure usando um modelo de Gestor de Recursos Azure - Azure RBAC
description: Aprenda a criar ou atualizar funções personalizadas Azure usando um modelo de Gestor de Recursos Azure (modelo ARM) e controlo de acesso baseado em funções Azure (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: 0626a9e36d05ac9cb51f62652dbe6f3133bbc6d7
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095910"
---
# <a name="create-or-update-azure-custom-roles-using-an-arm-template"></a>Criar ou atualizar funções personalizadas Azure usando um modelo ARM

Se os [papéis incorporados do Azure](built-in-roles.md) não corresponderem às necessidades específicas da sua organização, pode criar os seus [próprios papéis personalizados.](custom-roles.md) Este artigo descreve como criar ou atualizar uma função personalizada usando um modelo de Gestor de Recursos Azure (modelo ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Para criar um papel personalizado, especifica um nome de função, permissões e onde a função pode ser usada. Neste artigo, cria uma função chamada _Custom Role - RG Reader_ com permissões de recursos que podem ser atribuídas num âmbito de subscrição ou inferior.

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsubscription-deployments%2Fcreate-role-def%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Para criar um papel personalizado, você deve ter:

- Permissões para criar funções personalizadas, tais como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso ao Utilizador.](built-in-roles.md#user-access-administrator)

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste artigo é de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/create-role-def). O modelo tem quatro parâmetros e uma secção de recursos. Os quatro parâmetros são:

- Conjunto de ações com um valor padrão de `["Microsoft.Resources/subscriptions/resourceGroups/read"]` .
- Matriz de `notActions` com um valor padrão vazio.
- Nome de função com um valor padrão de `Custom Role - RG Reader` .
- Descrição da função com um valor padrão de `Subscription Level Deployment of a Role Definition` .

O âmbito onde esta função personalizada pode ser atribuída é definido para a subscrição atual.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

O recurso definido no modelo é:

- [Microsoft.Autorização/funDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

## <a name="deploy-the-template"></a>Implementar o modelo

Siga estes passos para implementar o modelo anterior.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Abra a casca de nuvem Azure para powerShell.

1. Copie e cole o seguinte script na Cloud Shell.

    ```azurepowershell-interactive
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"
    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Introduza um local para a implantação como `centralus` .

1. Introduza uma lista de ações para o papel personalizado como uma lista separada por vírgula, tais como `Microsoft.Resources/resources/read,Microsoft.Resources/subscriptions/resourceGroups/read` .

1. Se necessário, prima Enter para executar o `New-AzDeployment` comando.

    O comando [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) implementa o modelo para criar o papel personalizado.

    Deverá ver um resultado semelhante ao seguinte:

    ```azurepowershell-interactive
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

    ```azurepowershell-interactive
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    Deverá ver um resultado semelhante ao seguinte:

    ```azurepowershell-interactive
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

1. No menu esquerdo, selecione **Access control (IAM)**.

1. Selecione o **separador Funções.**

1. Deslote a lista **de tipo** para **CustomRole**.

1. Verifique se a **função De Função Personalizada - Leitor RG** está listada.

   ![Novo papel personalizado no portal Azure](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Semelhante à criação de um papel personalizado, pode atualizar um papel personalizado existente usando um modelo. Para atualizar uma função personalizada, tem de especificar a função que pretende atualizar.

Aqui estão as alterações que você precisaria de fazer para o modelo quickstart anterior para atualizar o papel personalizado.

- Inclua o iD de função como parâmetro.
    ```json
        ...
        "roleDefName": {
          "type": "string",
          "metadata": {
            "description": "ID of the role definition"
          }
        ...
    ```

- Incluir o parâmetro de ID de função na definição de função.

    ```json
      ...
      "resources": [
        {
          "type": "Microsoft.Authorization/roleDefinitions",
          "apiVersion": "2018-07-01",
          "name": "[parameters('roleDefName')]",
          "properties": {
            ...
    ```

Aqui está um exemplo de como implementar o modelo.

```azurepowershell
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
[string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
$actions = $actions.Split(',')
$roleDefName = Read-Host -Prompt "Enter the role ID to update"
$templateFile = "rg-reader-update.json"
New-AzDeployment -Location $location -TemplateFile $templateFile -actions $actions -roleDefName $roleDefName
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover o papel personalizado, siga estes passos.

1. Executar o seguinte comando para remover o papel personalizado.

    ```azurepowershell-interactive
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Insira **Y** para confirmar que deseja remover a função personalizada.

## <a name="next-steps"></a>Passos seguintes

- [Compreender definições de função Azure](role-definitions.md)
- [Quickstart: Atribua um papel Azure usando um modelo de Gestor de Recursos Azure](quickstart-role-assignments-template.md)
- [Documentação do modelo do ARM](../azure-resource-manager/templates/index.yml)
