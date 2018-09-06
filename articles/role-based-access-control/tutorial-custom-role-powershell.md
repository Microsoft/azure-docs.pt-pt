---
title: Tutorial - Criar uma função personalizada com o Azure PowerShell| Microsoft Docs
description: Começar a criar uma função personalizada com o Azure PowerShell.
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
ms.date: 06/12/2018
ms.author: rolyon
ms.openlocfilehash: 1e3043a6053625f8363e9035e59b53275714a827
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300614"
---
# <a name="tutorial-create-a-custom-role-using-azure-powershell"></a>Tutorial: Criar uma função personalizada com o Azure PowerShell

Se as [funções incorporadas](built-in-roles.md) não suprirem as necessidades específicas da sua organização, pode criar as suas próprias funções personalizadas. Neste tutorial vai criar uma função personalizada com o nome Pedidos de Suporte do Leitor com o Azure PowerShell. A função personalizada permite ao utilizador ver tudo na subscrição e abrir pedidos de suporte.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma função personalizada
> * Listar funções personalizadas
> * Atualizar uma função personalizada
> * Eliminar uma função personalizada

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, irá precisar de:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Utilizador](built-in-roles.md#user-access-administrator)
- [Azure PowerShell](/powershell/azure/install-azurerm-ps) instalado localmente

## <a name="sign-in-to-azure-powershell"></a>Iniciar sessão no Azure PowerShell

Iniciar sessão no [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Criar uma função personalizada

A forma mais fácil de criar uma função personalizada é começar com uma função incorporada, editá-la e, em seguida, criar uma nova função.

1. No PowerShell, utilize o comando [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) para obter a lista de operações para o fornecedor de recursos Microsoft.Support. É útil conhecer as operações disponíveis para criar as suas permissões. Também pode ver uma lista de todas as operações em [operações do fornecedor de recursos do Azure Resource Manager](resource-provider-operations.md#microsoftsupport).

    ```azurepowershell
    Get-AzureRMProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Utilize o comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para devolver a função [Leitor](built-in-roles.md#reader) no formato JSON.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Abra o ficheiro **ReaderSupportRole.json** num editor.

    O seguinte mostra o resultado do JSON. Para obter informações sobre as diferentes propriedades, veja [Funções personalizadas](custom-roles.md).

    ```json
    {
        "Name":  "Reader",
        "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "IsCustom":  false,
        "Description":  "Lets you view everything, but not make any changes.",
        "Actions":  [
                        "*/read"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/"
                             ]
    }
    ```
    
1. Edite o ficheiro JSON para adicionar a operação `"Microsoft.Support/*"` à propriedade `Actions`. Não se esqueça de incluir uma vírgula após a operação de leitura. Esta ação irá permitir ao utilizador criar pedidos de suporte.

1. Obtenha o ID da sua subscrição com o comando [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

    ```azurepowershell
    Get-AzureRmSubscription
    ```

1. Em `AssignableScopes`, adicione o ID de subscrição com o seguinte formato: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Tem de adicionar os IDs de subscrição explícitos, caso contrário, não poderá importar a função para a sua subscrição.

1. Elimine a linha de propriedade `Id` e altere a propriedade `IsCustom` para `true`.

1. Altere as propriedades `Name` e `Description` para "Pedidos de Suporte do Leitor" e "Ver tudo na subscrição e abrir pedidos de suporte."

    O seu ficheiro JSON deverá ser semelhante ao seguinte:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
    
1. Para criar a nova função personalizada, utilize o comando [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) e especifique o ficheiro de definição de função JSON.

    ```azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
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

- Para listar todas as funções personalizadas, utilize o comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

    ```azurepowershell
    Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
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

1. Para atualizar o ficheiro JSON, utilize o comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para devolver a função personalizada no formato JSON.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Abra o ficheiro num editor.

1. Em `Actions`, adicione a operação para criar e gerir implementações do grupo de recursos `"Microsoft.Resources/deployments/*"`.

    O seu ficheiro JSON atualizado deverá ser semelhante ao seguinte:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "Id":  "22222222-2222-2222-2222-222222222222",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*",
                        "Microsoft.Resources/deployments/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
        
1. Para atualizar a função personalizada, utilize o comando [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) e especifique o ficheiro JSON atualizado.

    ```azurepowershell
    Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
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

1. Para utilizar o objeto `PSRoleDefintion` para atualizar a sua função personalizada, primeiro utilize o comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para obter a função.

    ```azurepowershell
    $role = Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```
    
1. Chame o método `Add` para adicionar a operação para ler as definições de diagnóstico.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Utilize o [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) para atualizar a função.

    ```azurepowershell
    Set-AzureRmRoleDefinition -Role $role
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

1. Utilize o comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para obter o ID da função personalizada.

    ```azurepowershell
    Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```

1. Utilize o comando [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) e especifique o ID da função para eliminar a função personalizada.

    ```azurepowershell
    Remove-AzureRmRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Quando lhe for pedido para confirmar, escreva **Y**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar funções personalizadas com o PowerShell](custom-roles-powershell.md)
