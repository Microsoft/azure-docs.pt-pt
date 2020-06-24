---
title: Crie funções personalizadas no controlo de acesso baseado em funções Azure AD / Microsoft Docs
description: Crie e atribua funções Azure AD personalizadas com âmbito de recursos nos recursos do Azure Ative Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e172a023cc9156f435b4f40b2262ee44128c138e
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84732009"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Criar e atribuir um papel personalizado no Azure Ative Directory

Este artigo descreve como criar novas funções personalizadas no Azure Ative Directory (Azure AD). Para o básico das funções personalizadas, consulte a visão geral das [funções personalizadas.](roles-custom-overview.md) A função pode ser atribuída no âmbito do diretório ou apenas no âmbito do recurso de registo de aplicações.

As funções personalizadas podem ser criadas no [separador Funções e administradores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) na página geral do AD Azure.

## <a name="create-a-role-in-the-azure-portal"></a>Criar um papel no portal Azure

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Criar uma nova função personalizada para garantir o acesso à gestão de registos de aplicações

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com)   com o administrador privilegiado ou permissões de administrador global na organização Azure AD.
1. Selecione **Azure Ative Directory**  >  **Roles and administrators**  >  **New custom role**.

   ![Criar ou editar funções a partir da página Funções e administradores](./media/roles-create-custom/new-custom-role.png)

1. No **separador Básicos,** forneça um nome e descrição para a função e, em seguida, clique em **Seguinte**.

   ![fornecer um nome e descrição para um papel personalizado no separador Básicos](./media/roles-create-custom/basics-tab.png)

1. No separador **Permissões,** selecione as permissões necessárias para gerir propriedades básicas e propriedades credenciais de registos de aplicações. Para obter uma descrição detalhada de cada permissão, consulte [os subtipos e permissões de registo de aplicações no Diretório Ativo Azure](./roles-custom-available-permissions.md).
   1. Primeiro, introduza "credenciais" na barra de pesquisa e selecione a `microsoft.directory/applications/credentials/update` permissão.

      ![Selecione as permissões para uma função personalizada no separador Permissões](./media/roles-create-custom/permissions-tab.png)

   1. Em seguida, introduza "básico" na barra de pesquisa, selecione a `microsoft.directory/applications/basic/update` permissão e, em seguida, clique em **Seguinte**.
1. No **separador 'Rever +' criar,** rever as permissões e selecionar **Criar.**

O seu papel personalizado aparecerá na lista de funções disponíveis para atribuir.

## <a name="create-a-role-using-powershell"></a>Criar um papel usando PowerShell

### <a name="prepare-powershell"></a>Preparar PowerShell

Em primeiro lugar, tem de [descarregar o módulo PowerShell de pré-visualização AZure AD](https://www.powershellgallery.com/packages/AzureADPreview).

Para instalar o módulo Azure AD PowerShell, utilize os seguintes comandos:

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

Crie um novo papel utilizando o seguinte script PowerShell:

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Atribua o papel personalizado usando Azure AD PowerShell

Atribua a função utilizando o script abaixo do PowerShell:

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

## <a name="create-a-role-with-graph-api"></a>Criar um papel com a API do Gráfico

1. Crie a definição de papel.

    HTTP solicitação para criar uma definição de função personalizada.

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

  > [!Note]
  > O "templateId": "GUID" é um parâmetro opcional enviado no corpo dependendo da exigência. Se você tem um requisito para criar vários papéis personalizados diferentes com parâmetros comuns, é melhor criar um modelo e definir um modeloId . Pode gerar um modeloId previamente utilizando o cmdlet powershell (New-Guid). Guia. 

1. Crie a tarefa de função.

    HTTP solicitação para criar uma definição de função personalizada.

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


## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Atribuir uma função personalizada a um recurso

Tal como as funções incorporadas, as funções personalizadas são atribuídas por padrão no âmbito padrão da organização para conceder permissões de acesso em todos os registos de aplicações da sua organização. Mas, ao contrário das funções incorporadas, as funções personalizadas também podem ser atribuídas no âmbito de um único recurso AD Azure. Isto permite-lhe dar ao utilizador a permissão para atualizar credenciais e propriedades básicas de uma única aplicação sem ter de criar uma segunda função personalizada.

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com permissões de desenvolvedores de aplicações na organização Azure AD.
1. Selecione **Registos das aplicações**.
1. Selecione o registo da aplicação a que está a conceder acesso à gestão. Poderá ter de selecionar **todas as aplicações** para ver a lista completa de registos de aplicações na sua organização AZure AD.

    ![Selecione o registo da aplicação como uma margem de recurso para uma atribuição de funções](./media/roles-create-custom/appreg-all-apps.png)

1. No registo da aplicação, selecione **Roles e administradores.** Se ainda não criou uma, as instruções estão no [procedimento anterior.](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)

1. Selecione a função para abrir a página **Atribuições.**
1. **Selecione Adicionar a atribuição** para adicionar um utilizador. O utilizador receberá quaisquer permissões apenas sobre o registo de aplicações selecionados.

## <a name="next-steps"></a>Passos seguintes

- Sinta-se livre para compartilhar conosco no fórum de [funções administrativas Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Para obter mais informações sobre funções e atribuição de funções de Administrador, consulte [as funções de administrador de atribuição](directory-assign-admin-roles.md).
- Para obter permissões de utilizador predefinidos, consulte uma [comparação entre as permissões de utilizador e de hóspedes predefinidos](../fundamentals/users-default-permissions.md).
