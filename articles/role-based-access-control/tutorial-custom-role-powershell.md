---
title: Tutorial - Create a custom role for Azure resources using Azure PowerShell
description: Get started creating a custom role for Azure resources using Azure PowerShell in this tutorial.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: 176e465163d92156308eda64c4187467cc10ee15
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419741"
---
# <a name="tutorial-create-a-custom-role-for-azure-resources-using-azure-powershell"></a>Tutorial: Create a custom role for Azure resources using Azure PowerShell

If the [built-in roles for Azure resources](built-in-roles.md) don't meet the specific needs of your organization, you can create your own custom roles. Neste tutorial vai criar uma função personalizada com o nome Pedidos de Suporte do Leitor com o Azure PowerShell. The custom role allows the user to view everything in the management plane of a subscription and also open support tickets.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma função personalizada
> * Listar funções personalizadas
> * Atualizar uma função personalizada
> * Eliminar uma função personalizada

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, irá precisar de:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Utilizador](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) or [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Iniciar sessão no Azure PowerShell

Iniciar sessão no [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Criar uma função personalizada

A forma mais fácil de criar uma função personalizada é começar com uma função incorporada, editá-la e, em seguida, criar uma nova função.

1. In PowerShell, use the [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) command to get the list of operations for the Microsoft.Support resource provider. É útil conhecer as operações disponíveis para criar as suas permissões. Também pode ver uma lista de todas as operações em [operações do fornecedor de recursos do Azure Resource Manager](resource-provider-operations.md#microsoftsupport).

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command to output the [Reader](built-in-roles.md#reader) role in JSON format.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Abra o ficheiro **ReaderSupportRole.json** num editor.

    O seguinte mostra o resultado do JSON. Para obter informações sobre as diferentes propriedades, veja [Funções personalizadas](custom-roles.md).

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. Edite o ficheiro JSON para adicionar a operação `"Microsoft.Support/*"` à propriedade `Actions`. Não se esqueça de incluir uma vírgula após a operação de leitura. Esta ação irá permitir ao utilizador criar pedidos de suporte.

1. Get the ID of your subscription using the [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) command.

    ```azurepowershell
    Get-AzSubscription
    ```

1. Em `AssignableScopes`, adicione o ID de subscrição com o seguinte formato: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Tem de adicionar os IDs de subscrição explícitos, caso contrário, não poderá importar a função para a sua subscrição.

1. Elimine a linha de propriedade `Id` e altere a propriedade `IsCustom` para `true`.

1. Altere as propriedades `Name` e `Description` para "Pedidos de Suporte do Leitor" e "Ver tudo na subscrição e abrir pedidos de suporte."

    O seu ficheiro JSON deverá ser semelhante ao seguinte:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. To create the new custom role, use the [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) command and specify the JSON role definition file.

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    A nova função personalizada está agora disponível no portal do Azure e pode ser atribuída a utilizadores, grupos ou principais de serviço, tal como as funções incorporadas.

## <a name="list-custom-roles"></a>Listar funções personalizadas

- To list all your custom roles, use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command.

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Também pode ver a função personalizada no portal do Azure.

    ![captura de ecrã da função personalizada importada no portal do Azure](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar a função personalizada, pode atualizar o ficheiro JSON ou utilizar o objeto `PSRoleDefinition`.

1. To update the JSON file, use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command to output the custom role in JSON format.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Abra o ficheiro num editor.

1. Em `Actions`, adicione a operação para criar e gerir implementações do grupo de recursos `"Microsoft.Resources/deployments/*"`.

    O seu ficheiro JSON atualizado deverá ser semelhante ao seguinte:

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. To update the custom role, use the [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) command and specify the updated JSON file.

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. To use the `PSRoleDefintion` object to update your custom role, first use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command to get the role.

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. Chame o método `Add` para adicionar a operação para ler as definições de diagnóstico.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Use the [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) to update the role.

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

1. Use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command to get the ID of the custom role.

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. Use the [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) command and specify the role ID to delete the custom role.

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Quando lhe for pedido para confirmar, escreva **Y**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Create custom roles for Azure resources using Azure PowerShell](custom-roles-powershell.md) (Utilizar o Azure PowerShell para criar funções personalizadas para recursos do Azure)
