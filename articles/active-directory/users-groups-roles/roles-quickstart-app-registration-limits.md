---
title: Remover limites na criação de registros de aplicativo-Azure AD | Microsoft Docs
description: Atribuir uma função personalizada para conceder registros de aplicativo irrestrito no Azure AD Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12803e2f65e17155e8bbcaf4842789adc101b0dd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024398"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Início rápido: conceder permissão para criar registros de aplicativo ilimitados

Neste guia de início rápido, você criará uma função personalizada com permissão para criar um número ilimitado de registros de aplicativo e, em seguida, atribuir essa função a um usuário. O usuário atribuído pode usar o portal do Azure AD, o Azure AD PowerShell, o Azure AD API do Graph ou a API Microsoft Graph para criar registros de aplicativos. Diferentemente da função de desenvolvedor de aplicativo interna, essa função personalizada concede a capacidade de criar um número ilimitado de registros de aplicativo. A função de desenvolvedor de aplicativos concede a capacidade, mas o número total de objetos criados é limitado a 250 para evitar atingir [a cota de objetos de todo o diretório](directory-service-limits-restrictions.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisite"></a>Pré-requisito

A função menos privilegiada necessária para criar e atribuir funções personalizadas do Azure AD é o administrador de função com privilégios.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Criar uma nova função personalizada usando o portal do AD do Azure

1. Entre no centro de [Administração do Azure AD](https://aad.portal.azure.com) com permissões de administrador de função com privilégios ou de administrador global na organização do Azure AD.
1. Selecione **Azure Active Directory**, selecione **funções e administradores**e, em seguida, selecione **nova função personalizada**.

    ![Criar ou editar funções da página funções e administradores](./media/roles-create-custom/new-custom-role.png)

1. Na guia **noções básicas** , forneça "criador de registro do aplicativo" para o nome da função e "pode criar um número ilimitado de registros do aplicativo" para a descrição da função e, em seguida, selecione **Avançar**.

    ![forneça um nome e uma descrição para uma função personalizada na guia noções básicas](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. Na guia **permissões** , digite "Microsoft. Directory/Applications/Create" na caixa de pesquisa e marque as caixas de seleção ao lado das permissões desejadas e, em seguida, selecione **Avançar**.

    ![Selecione as permissões para uma função personalizada na guia permissões](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. Na guia **revisar + criar** , examine as permissões e selecione **criar**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Atribuir a função a um usuário usando o portal do Azure AD

1. Entre no centro de [Administração do Azure AD](https://aad.portal.azure.com) com permissões de administrador de função com privilégios ou de administrador global em sua organização do Azure AD.
1. Selecione **Azure Active Directory** e, em seguida, selecione **funções e administradores**.
1. Selecione a função criador de registro de aplicativo e selecione **Adicionar atribuição**.
1. Selecione o usuário desejado e clique em **selecionar** para adicionar o usuário à função.

E já está! Neste guia de início rápido, você criou com êxito uma função personalizada com permissão para criar um número ilimitado de registros de aplicativo e, em seguida, atribuir essa função a um usuário.

> [!TIP]
> Para atribuir a função a um aplicativo usando o portal do AD do Azure, insira o nome do aplicativo na caixa de pesquisa da página de atribuição. Os aplicativos não são mostrados na lista por padrão, mas são retornados nos resultados da pesquisa.

### <a name="app-registration-permissions"></a>Permissões de registro do aplicativo

Há duas permissões disponíveis para conceder a capacidade de criar registros de aplicativo, cada um com um comportamento diferente.

- Microsoft. Directory/Applications/createAsOwner: a atribuição desses resultados de permissão no criador que está sendo adicionado como o primeiro proprietário do registro do aplicativo criado, e o registro do aplicativo criado será contado na cota de objetos criados do 250 do criador.
- Microsoft. Directory/applicationPolicies/Create: a atribuição desses resultados de permissão no criador não será adicionada como o primeiro proprietário do registro do aplicativo criado, e o registro do aplicativo criado não contará na cota de objetos criados do 250 do criador. Use essa permissão com cuidado, pois não há nada impedindo que o destinatário crie registros de aplicativo até que a cota de nível de diretório seja atingida. Se ambas as permissões forem atribuídas, essa permissão terá precedência.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Criar uma função personalizada usando o PowerShell do Azure AD

Crie uma nova função usando o seguinte script do PowerShell:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Atribuir a função personalizada usando o PowerShell do Azure AD

#### <a name="prepare-powershell"></a>Preparar o PowerShell

Primeiro, instale o módulo do PowerShell do Azure AD do [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Em seguida, importe o módulo de visualização do PowerShell do Azure AD usando o seguinte comando:

```powershell
import-module azureadpreview
```

Para verificar se o módulo está pronto para uso, corresponda à versão retornada pelo seguinte comando para aquele listado aqui:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Atribuir a função personalizada

Atribua a função usando o script do PowerShell abaixo:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Criar uma função personalizada usando Microsoft Graph API

Solicitação HTTP para criar a função personalizada.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Corpo

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Atribuir a função personalizada usando Microsoft Graph API

A atribuição de função combina uma ID de entidade de segurança (que pode ser um usuário ou uma entidade de serviço), uma ID de função (função) e um escopo de recursos do Azure AD.

Solicitação HTTP para atribuir uma função personalizada.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Corpo

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Passos seguintes

- Fique à vontade para compartilhar conosco no [Fórum de funções administrativas do Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Para obter mais informações sobre funções e atribuição de função de administrador, consulte [atribuir funções de administrador](directory-assign-admin-roles.md).
- Para permissões de usuário padrão, consulte uma [comparação das permissões padrão de usuário convidado e membro](../fundamentals/users-default-permissions.md).
