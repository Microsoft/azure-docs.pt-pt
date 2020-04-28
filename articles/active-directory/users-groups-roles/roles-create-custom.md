---
title: Criar papéis personalizados no controlo de acesso baseado em funções da Azure AD [ Microsoft Docs
description: Crie e atribua funções personalizadas de AD Azure com âmbito de recursos sobre recursos do Diretório Ativo do Azure.
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
ms.openlocfilehash: c2cb19c82f8c19bf87eeef755adb5756b2452512
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74025277"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Criar e atribuir um papel personalizado no Diretório Ativo Azure

Este artigo descreve como criar novos papéis personalizados no Azure Ative Directory (Azure AD). Para obter o básico dos papéis personalizados, consulte a visão geral dos [papéis personalizados.](roles-custom-overview.md) A função pode ser atribuída quer no âmbito do diretório, quer apenas no âmbito do recurso de registo de aplicações.

As funções personalizadas podem ser criadas no separador [Funções e administradores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) na página de visão geral do Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Criar um papel no portal Azure

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Criar uma nova função personalizada para garantir o acesso à gestão de registos de aplicações

1. Inscreva-se no centro de [administração da Azure AD](https://aad.portal.azure.com)com o administrador privilegiado ou permissões de administrador global na organização Azure AD.
1. Selecione > **Funções de** >  **Diretório Ativo Azure**e administradores**Nova função personalizada**.

   ![Criar ou editar funções a partir da página Funções e administradores](./media/roles-create-custom/new-custom-role.png)

1. No separador **Basics,** forneça um nome e descrição para o papel e, em seguida, clique em **Seguinte**.

   ![fornecer um nome e descrição para um papel personalizado no separador Basics](./media/roles-create-custom/basics-tab.png)

1. No separador **Permissões,** selecione as permissões necessárias para gerir propriedades básicas e propriedades credenciais de registos de aplicações. Para obter uma descrição detalhada de cada permissão, consulte subtipos e permissões de registo de [aplicação no Diretório Ativo azure](./roles-custom-available-permissions.md).
   1. Primeiro, introduza "credenciais" na `microsoft.directory/applications/credentials/update` barra de pesquisa e selecione a permissão.

      ![Selecione as permissões para um papel personalizado no separador Permissões](./media/roles-create-custom/permissions-tab.png)

   1. Em seguida, introduza "basic" na `microsoft.directory/applications/basic/update` barra de pesquisa, selecione a permissão e, em seguida, clique **em Seguinte**.
1. No **separador Review + criar,** reveja as permissões e selecione **Criar**.

O seu papel personalizado aparecerá na lista de funções disponíveis para atribuir.

## <a name="create-a-role-using-powershell"></a>Criar uma função usando powerShell

### <a name="prepare-powershell"></a>Preparar powerShell

Primeiro, tem de descarregar o módulo PowerShell de [pré-visualização AD Azure](https://www.powershellgallery.com/packages/AzureADPreview).

Para instalar o módulo PowerShell Azure AD, utilize os seguintes comandos:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Para verificar se o módulo está pronto a ser utilizado, utilize o seguinte comando:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Criar o papel personalizado

Criar uma nova função utilizando o seguinte script PowerShell:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Atribuir a função personalizada usando a Azure AD PowerShell

Atribuir a função utilizando o script abaixo powerShell:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-graph-api"></a>Criar um papel com a API do gráfico

1. Criar a definição de papel.

    HTTP solicita para criar uma definição de função personalizada.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Corpo

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
   }
    ```

1. Criar a atribuição do papel.

    HTTP solicita para criar uma definição de função personalizada.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Corpo

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Atribuir uma função personalizada ao seu recurso

Tal como as funções incorporadas, as funções personalizadas são atribuídas por padrão no âmbito padrão da organização para conceder permissões de acesso em todas as inscrições de aplicações na sua organização. Mas, ao contrário de papéis incorporados, as funções personalizadas também podem ser atribuídas no âmbito de um único recurso Azure AD. Isto permite-lhe dar ao utilizador a permissão para atualizar credenciais e propriedades básicas de uma única aplicação sem ter de criar uma segunda função personalizada.

1. Inscreva-se no centro de [administração da Azure AD](https://aad.portal.azure.com) com permissões de programador de aplicações na organização Azure AD.
1. Selecione **Registos das aplicações**.
1. Selecione o registo da aplicação a que está a conceder acesso à gestão. Poderá ter de selecionar **Todas as aplicações** para ver a lista completa de registos de aplicações na sua organização Azure AD.

    ![Selecione o registo da aplicação como uma margem de recurso para uma atribuição de funções](./media/roles-create-custom/appreg-all-apps.png)

1. No registo da aplicação, selecione **Funções e administradores.** Se ainda não criou uma, as instruções estão no [procedimento anterior](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Selecione o papel para abrir a página **de Atribuiçãos.**
1. Selecione **Adicionar a tarefa** para adicionar um utilizador. O utilizador receberá permissões apenas sobre o registo da aplicação selecionada.

## <a name="next-steps"></a>Passos seguintes

- Sinta-se livre para partilhar connosco no fórum de [funções administrativas da Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Para mais informações sobre funções e atribuição de funções de administrador, consulte [as funções de administrador de atribuição](directory-assign-admin-roles.md).
- Para obter permissões de utilizador predefinidas, consulte uma [comparação das permissões padrão dos hóspedes e dos utilizadores dos membros](../fundamentals/users-default-permissions.md).
