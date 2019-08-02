---
title: Criar uma definição de função personalizada no controle de acesso baseado em função do Azure AD – Azure Active Directory | Microsoft Docs
description: Crie funções personalizadas do Azure AD com o escopo de recursos em Azure Active Directory recursos.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae773bc1a4e1831dbe462149bb827c26b7e74b96
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722246"
---
# <a name="create-a-custom-role-and-assign-a-resource-scope-in-azure-active-directory"></a>Criar uma função personalizada e atribuir um escopo de recurso em Azure Active Directory

Este artigo descreve como criar novas funções personalizadas no Azure Active Directory (AD do Azure). As funções personalizadas podem ser criadas na guia **funções e administradores** na página Visão geral do Azure ad ou [na página de registro do aplicativo](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview). A função pode ser atribuída no escopo no nível do diretório ou no escopo dos registros do aplicativo.

Para obter mais informações, consulte a [visão geral das funções personalizadas](roles-custom-overview.md) para obter noções básicas de funções personalizadas.

## <a name="using-the-azure-ad-portal"></a>Usando o portal do AD do Azure

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Criar uma nova função personalizada para conceder acesso para gerenciar registros de aplicativo

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com)com permissões de administrador de função privilegiada ou de administrador global na organização do Azure AD.
1. Selecione ****  > **funções de Azure Active Directory e administradores** > **nova função personalizada**.

   ![Criar ou editar funções da página funções e administradores](./media/roles-create-custom/new-custom-role.png)

1. Na guia **noções básicas** , forneça um nome e uma descrição para a função.

   ![forneça um nome e uma descrição para uma função personalizada na guia noções básicas](./media/roles-create-custom/basics-tab.png)

1. Para selecionar as permissões necessárias para gerenciar as credenciais de registro do aplicativo e as propriedades básicas, como nome:
   1. Insira "credenciais" na barra de pesquisa e selecione a `microsoft.directory/applications/credentials/update` permissão.

      ![Selecione as permissões para uma função personalizada na guia permissões](./media/roles-create-custom/permissions-tab.png)

   1. Digite "básico" na barra de pesquisa, selecione a `microsoft.directory/applications/basic/update` permissão e clique em **Avançar**.
1. Na guia **revisar + criar** , examine as permissões e selecione **criar**.

Sua função personalizada aparecerá na lista de funções disponíveis a serem atribuídas.

## <a name="assign-a-role-scoped-to-a-resource"></a>Atribuir uma função com escopo a um recurso

Como funções internas, as funções personalizadas podem ser atribuídas no escopo da organização padrão para conceder acesso em todos os registros do aplicativo. Mas as funções personalizadas também podem ser atribuídas em escopo de objeto. Isso permite que você conceda ao destinatário a permissão para atualizar as credenciais e as propriedades básicas de um único aplicativo sem precisar criar uma segunda função personalizada.

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com permissões de desenvolvedor de aplicativo na organização do Azure AD.
1. Selecione **Registos das aplicações**.
1. Selecione o registro de aplicativo ao qual você está concedendo acesso para gerenciar. Talvez seja necessário selecionar **todos os aplicativos** para ver a lista completa de registros de aplicativo na sua organização do Azure AD.

    ![Selecionar o registro do aplicativo como um escopo de recurso para uma atribuição de função](./media/roles-create-custom/appreg-all-apps.png)

1. No registro do aplicativo, selecione **funções e administradores**. Se você ainda não criou uma, as instruções estão no [procedimento anterior](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

    Se você atribuir essa função do contexto de um registro de aplicativo aberto, o destinatário terá permissões nesse registro de aplicativo único. A função que você está atribuindo aparece na lista em cada registro de aplicativo. Esse modelo de acesso, em que um proprietário pode receber permissão para recursos específicos do AD do Azure por funções, é semelhante ao modelo usado no [Azure RBAC](../../role-based-access-control/overview.md) para controle de acesso de recursos do Azure.

1. Selecione a função para abrir a página atribuições.
1. Selecione **Adicionar atribuição** para adicionar um usuário. O usuário não receberá nenhuma permissão sobre qualquer registro do aplicativo que não seja o selecionado.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Criar uma função personalizada usando o PowerShell do Azure AD

### <a name="prepare-powershell"></a>Preparar o PowerShell

Primeiro, você deve [baixar o módulo do PowerShell do Azure ad](https://www.powershellgallery.com/packages/AzureAD/).

Para instalar o módulo do PowerShell do Azure AD, use os seguintes comandos:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Para verificar se o módulo está pronto para uso, use o seguinte comando:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Criar a função personalizada

Crie uma nova função usando o seguinte script do PowerShell:

``` PowerShell
# Basic role information
$description = "Application Support Administrator"
$displayName = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/allProperties/read",
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="create-a-custom-role-using-microsoft-graph-api"></a>Criar uma função personalizada usando Microsoft Graph API

1. Crie a definição de função.

    Solicitação HTTP para criar uma definição de função personalizada.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Body

    ``` HTTP
    {
    "description":"Can manage basic aspects of application registrations.",
    "displayName":"Application Support Administrator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "allowedResourceActions": 
            [
                "microsoft.directory/applications/basic/read",
                "microsoft.directory/applications/credentials/update"
            ]
        }
    ]
    }
    ```

1. Crie a atribuição de função.

    Solicitação HTTP para criar uma definição de função personalizada.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions/roleAssignments
    ```

    Body

    ``` HTTP
    {
    "principalId":"<GUID OF USER>",
    "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
    "resourceScope":["/<GUID OF APPLICATION REGISTRATION>"]
    }
    ```

## <a name="next-steps"></a>Passos Seguintes

- Fique à vontade para compartilhar conosco no [Fórum de funções administrativas do Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Para obter mais informações sobre funções e atribuição de função de administrador, consulte [atribuir funções de administrador](directory-assign-admin-roles.md).
- Para permissões de usuário padrão, consulte uma [comparação das permissões padrão de usuário convidado e membro](../fundamentals/users-default-permissions.md).
