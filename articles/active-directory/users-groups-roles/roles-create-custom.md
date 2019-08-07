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
ms.openlocfilehash: e0e33cf9fa1c4661dd71fc41cb667b0373c9e955
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774818"
---
# <a name="create-a-custom-role-and-assign-at-resource-scope-in-azure-active-directory"></a>Criar uma função personalizada e atribuir no escopo de recurso no Azure Active Directory

Este artigo descreve como criar novas funções personalizadas no Azure Active Directory (AD do Azure). As funções personalizadas podem ser criadas na guia [funções e administradores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) na página Visão geral do Azure AD. A função pode ser atribuída apenas no escopo no nível do diretório ou no escopo do recurso de registro do aplicativo.

Para obter mais informações, consulte a [visão geral das funções personalizadas](roles-custom-overview.md) para obter noções básicas de funções personalizadas.

## <a name="using-the-azure-ad-portal"></a>Usando o portal do AD do Azure

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Criar uma nova função personalizada para conceder acesso para gerenciar registros de aplicativo

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com)com permissões de administrador de função privilegiada ou de administrador global na organização do Azure AD.
1. Selecione ****  > **funções de Azure Active Directory e administradores** > **nova função personalizada**.

   ![Criar ou editar funções da página funções e administradores](./media/roles-create-custom/new-custom-role.png)

1. Na guia **noções básicas** , forneça um nome e uma descrição para a função e clique em **Avançar**.

   ![forneça um nome e uma descrição para uma função personalizada na guia noções básicas](./media/roles-create-custom/basics-tab.png)

1. Na guia **permissões** , selecione as permissões necessárias para gerenciar propriedades básicas e propriedades de credenciais de registros do aplicativo. Para obter uma descrição detalhada de cada permissão, consulte subtipos de [registro de aplicativo e permissões no Azure Active Directory](./roles-custom-available-permissions.md).
   1. Primeiro, insira "credenciais" na barra de pesquisa e selecione a `microsoft.directory/applications/credentials/update` permissão.

      ![Selecione as permissões para uma função personalizada na guia permissões](./media/roles-create-custom/permissions-tab.png)

   1. Em seguida, digite "básico" na barra de pesquisa, selecione `microsoft.directory/applications/basic/update` a permissão e clique em **Avançar**.
1. Na guia **revisar + criar** , examine as permissões e selecione **criar**.

Sua função personalizada aparecerá na lista de funções disponíveis a serem atribuídas.

## <a name="assign-a-role-scoped-to-a-resource"></a>Atribuir uma função com escopo a um recurso

Como funções internas, as funções personalizadas podem ser atribuídas em escopo de toda a organização para conceder acesso a todos os registros de aplicativo. Mas as funções personalizadas também podem ser atribuídas no escopo do recurso. Isso permite que você conceda ao destinatário a permissão para atualizar as credenciais e as propriedades básicas de um único aplicativo sem precisar criar uma segunda função personalizada.

1. Se ainda não estiver, entre no [centro de administração do Azure ad](https://aad.portal.azure.com) com permissões de desenvolvedor de aplicativo na organização do Azure AD.
1. Selecione **Registos das aplicações**.
1. Selecione o registro de aplicativo ao qual você está concedendo acesso para gerenciar. Talvez seja necessário selecionar **todos os aplicativos** para ver a lista completa de registros de aplicativo na sua organização do Azure AD.

    ![Selecionar o registro do aplicativo como um escopo de recurso para uma atribuição de função](./media/roles-create-custom/appreg-all-apps.png)

1. No registro do aplicativo, selecione **funções e administradores**. Se você ainda não criou uma, as instruções estão no [procedimento anterior](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Selecione a função para abrir a página atribuições.
1. Selecione **Adicionar atribuição** para adicionar um usuário. O usuário não receberá nenhuma permissão sobre qualquer registro do aplicativo que não seja o selecionado.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Criar uma função personalizada usando o PowerShell do Azure AD

### <a name="prepare-powershell"></a>Preparar o PowerShell

Primeiro, você deve [baixar o módulo PowerShell de visualização do Azure ad](https://www.powershellgallery.com/packages/AzureADPreview).

Para instalar o módulo do PowerShell do Azure AD, use os seguintes comandos:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Para verificar se o módulo está pronto para uso, use o seguinte comando:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
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
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Atribuir a função personalizada usando o PowerShell do Azure AD

Atribua a função usando o script do PowerShell abaixo:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADRoleDefinition -Filter "displayName eq ' Application Registration Creator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScopes = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADRoleAssignment -ResourceScopes $resourceScopes -RoleDefinitionId $roleDefinition.objectId -PrincipalId $user.objectId
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
        "microsoft.directory/applications/basic/update",
        "microsoft.directory/applications/credentials/update"
    ]
    }
    ```

1. Crie a atribuição de função.

    Solicitação HTTP para criar uma definição de função personalizada.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
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
