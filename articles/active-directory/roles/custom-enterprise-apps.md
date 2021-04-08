---
title: Criar funções personalizadas para gerir aplicações empresariais no Azure Ative Directory
description: Criar e atribuir funções Azure AD personalizadas para o acesso de apps empresariais no Azure Ative Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a76e2d37e9dcdd285a8608fdbfd715bfb834eb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467753"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Criar funções personalizadas para gerir aplicações empresariais no Azure Ative Directory

Este artigo explica como criar uma função personalizada com permissões para gerir atribuições de aplicações empresariais para utilizadores e grupos em Azure Ative Directory (Azure AD). Para os elementos das atribuições de funções e o significado de termos como subtipo, permissão e conjunto de propriedades, consulte a visão geral das [funções personalizadas.](custom-overview.md)

## <a name="enterprise-app-role-permissions"></a>Permissões de funções de aplicativo da empresa

Há duas permissões de aplicações empresariais discutidas neste artigo. Todos os exemplos utilizam a permissão de atualização.

* Para ler as atribuições de utilizador e grupo no âmbito, conceda a `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` permissão
* Para gerir as atribuições de utilizador e grupo no âmbito, conceda a `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` permissão

A concessão da permissão de atualização resulta em que o destinatário seja capaz de gerir atribuições de utilizadores e grupos a aplicações empresariais. O âmbito das atribuições de utilizador e/ou grupo pode ser concedido para uma única aplicação ou concedido para todas as aplicações. Se concedido a nível de organização, o cessionário pode gerir atribuições para todas as aplicações. Se for escamado a nível de aplicação, o cessionário pode gerir atribuições apenas para a aplicação especificada.

A concessão da permissão de atualização é feita em dois passos:

1. Criar um papel personalizado com permissão `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Conceda aos utilizadores ou grupos permissões para gerir as atribuições de utilizadores e grupos a aplicações empresariais. É aqui que pode definir o âmbito para o nível da organização ou para uma única aplicação.

## <a name="use-the-azure-ad-admin-center"></a>Use o centro de administração Azure AD

### <a name="create-a-new-custom-role"></a>Criar um novo papel personalizado

>[!NOTE]
> As funções personalizadas são criadas e geridas a nível da organização e estão disponíveis apenas a partir da página geral da organização.

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com permissões de Administrador de Função Privilegiada ou Administrador Global na sua organização.
1. Selecione **Azure Ative Directory**, selecione **Funções e administradores**, e, em seguida, selecione **Nova função personalizada**.

    ![Adicione um novo papel personalizado da lista de papéis em Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. No separador **Basics,** forneça "Gerir as atribuições de utilizador e grupo" para o nome da função e "Conceder permissões para gerir as atribuições de utilizador e grupo" para a descrição da função e, em seguida, selecione **Seguinte**.

    ![Fornecer um nome e descrição para o papel personalizado](./media/custom-enterprise-apps/role-name-and-description.png)

1. No separador **Permissões, insira** "microsoft.directiony/servicePrincipals/appRoleAssignedTo/update" na caixa de pesquisa e, em seguida, selecione as caixas de verificação ao lado das permissões desejadas e, em seguida, selecione **Seguinte**.

    ![Adicione as permissões ao papel personalizado](./media/custom-enterprise-apps/role-custom-permissions.png)

1. No **separador 'Rever +' criar,** rever as permissões e selecionar **Criar.**

    ![Agora pode criar o papel personalizado](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Atribuir a função a um utilizador utilizando o portal AD AZure

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com permissões de função de administrador de função privilegiada.
1. Selecione **O Diretório Ativo Azure** e, em seguida, selecione **Funções e administradores**.
1. Selecione as **permissões Grant para gerir a função de atribuição de utilizadores e grupos.**

    ![Funções abertas e administradores e pesquisa rumo ao papel personalizado](./media/custom-enterprise-apps/select-custom-role.png)

1. **Selecione Adicionar a atribuição**, selecione o utilizador pretendido e, em seguida, clique em **Selecionar** para adicionar a atribuição de funções ao utilizador.

    ![Adicione uma atribuição para o papel personalizado ao utilizador](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Dicas de atribuição

* Para conceder permissões aos atribuir aos utilizadores e ao acesso ao grupo para todas as aplicações empresariais em toda a organização, comece pela lista de **Funções e Administradores** em toda a organização na página AZure AD **Overview** para a sua organização.
* Para conceder permissões aos atribuir aos utilizadores e ao acesso em grupo a uma aplicação empresarial específica, vá a essa aplicação em Azure AD e abra na lista **de Funções e Administradores** para essa aplicação. Selecione a nova função personalizada e complete a atribuição do utilizador ou do grupo. Os assignes podem gerir os utilizadores e o acesso ao grupo apenas para a aplicação específica.
* Para testar a sua atribuição de funções personalizada, inscreva-se como o destinatário e abra a página de **grupos de utilizadores e grupos** de uma aplicação para verificar se a opção **de utilizador Add** está ativada.

    ![Verifique as permissões do utilizador](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Use Azure AD PowerShell

Para obter mais detalhes, consulte [Criar e atribuir uma função personalizada](custom-create.md) e atribuir [funções personalizadas com âmbito de recursos utilizando o PowerShell.](custom-assign-powershell.md)

Em primeiro lugar, instale o módulo Azure AD PowerShell [da PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Em seguida, importe o módulo de pré-visualização Azure AD PowerShell, utilizando o seguinte comando:

```powershell
Import-Module -Name AzureADPreview
```

Para verificar se o módulo está pronto a ser utilizado, combine a versão devolvida pelo seguinte comando ao indicado aqui:

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>Criar uma função personalizada

Crie um novo papel utilizando o seguinte script PowerShell:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =@( "microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Atribuir o papel personalizado

Atribua a função utilizando este script PowerShell.

```powershell
PowerShell
# Basic role information

$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="use-the-microsoft-graph-api"></a>Utilizar a Microsoft Graph API

Crie uma função personalizada utilizando o exemplo fornecido na Microsoft Graph API. Para obter mais detalhes, consulte [Criar e atribuir uma função personalizada](custom-create.md) e atribuir [funções de administração personalizadas utilizando a API do Gráfico microsoft](custom-assign-graph.md).

HTTP solicitação para criar o papel personalizado.

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Atribua a função personalizada usando a Microsoft Graph API

A atribuição de funções combina um ID principal de segurança (que pode ser um utilizador ou principal de serviço), um ID de definição de função e um âmbito de recurso AD Azure. Para obter mais informações sobre os elementos de uma atribuição de funções, consulte a visão geral das [funções personalizadas](custom-overview.md)

HTTP solicita para atribuir uma função personalizada.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Passos seguintes

* [Explore as permissões de funções personalizadas disponíveis para aplicações empresariais](custom-enterprise-app-permissions.md)
