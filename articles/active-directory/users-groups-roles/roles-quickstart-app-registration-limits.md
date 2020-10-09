---
title: Remover limites na criação de registos de apps - Azure AD ! Microsoft Docs
description: Atribuir um papel personalizado para conceder registos de aplicações sem restrições no Diretório Ativo AZURE AD
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 08/07/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb53f9a6121ec9329eaec5d8cb554e7f125e058f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856658"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Quickstart: Conceder permissão para criar registos ilimitados de aplicações

Neste guia de arranque rápido, irá criar uma função personalizada com permissão para criar um número ilimitado de registos de aplicações e, em seguida, atribuir essa função a um utilizador. O utilizador designado pode então utilizar o portal AD AD Azure, O Azure AD PowerShell ou a Microsoft Graph API para criar registos de aplicações. Ao contrário da função de Desenvolvedor de Aplicações incorporada, esta função personalizada garante a capacidade de criar um número ilimitado de registos de aplicações. A função de Desenvolvedor de Aplicações concede a capacidade, mas o número total de objetos criados está limitado a 250 para evitar atingir [a quota de objetos em todo o diretório](directory-service-limits-restrictions.md). O papel menos privilegiado necessário para criar e atribuir funções personalizadas a Azure É o administrador privilegiado.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-custom-role-using-the-azure-ad-portal"></a>Crie um papel personalizado usando o portal AD Azure

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com)   com o administrador de função privilegiada ou permissões de administrador global na organização Azure AD.
1. Selecione **Azure Ative Directory**, selecione **Funções e administradores**, e, em seguida, selecione **Nova função personalizada**.

    ![Criar ou editar funções a partir da página Funções e administradores](./media/roles-create-custom/new-custom-role.png)

1. No separador **Básicos,** forneça "Criador de Registo de Aplicação" para o nome da função e "Pode criar um número ilimitado de registos de candidaturas" para a descrição da função e, em seguida, selecione **Seguinte**.

    ![fornecer um nome e descrição para um papel personalizado no separador Básicos](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. No separador **Permissões,** introduza "microsoft.directy/applications/create" na caixa de pesquisa e, em seguida, selecione as caixas de verificação ao lado das permissões desejadas e, em seguida, selecione **Seguinte**.

    ![Selecione as permissões para uma função personalizada no separador Permissões](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. No **separador 'Rever +' criar,** rever as permissões e selecionar **Criar.**

### <a name="assign-the-role-in-the-azure-ad-portal"></a>Atribuir o papel no portal AD Azure

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com)   com o administrador privilegiado ou permissões de administrador global na sua organização Azure AD.
1. Selecione **O Diretório Ativo Azure** e, em seguida, selecione **Funções e administradores**.
1. Selecione a função de Criador de Registo de Inscrição de Aplicação e **selecione a atribuição de adicionar.**
1. Selecione o utilizador pretendido e clique **em Selecionar** para adicionar o utilizador à função.

E já está! Neste quickstart, criou com sucesso um papel personalizado com permissão para criar um número ilimitado de registos de aplicações e, em seguida, atribuir esse papel a um utilizador.

> [!TIP]
> Para atribuir a função a uma aplicação utilizando o portal Azure AD, insira o nome da aplicação na caixa de pesquisa da página de atribuição. As aplicações não são apresentadas na lista por padrão, mas são devolvidas em resultados de pesquisa.

### <a name="app-registration-permissions"></a>Permissões do registo da aplicação

Existem duas permissões disponíveis para a concessão da capacidade de criar registos de candidaturas, cada uma com comportamentos diferentes.

- microsoft.diretório/aplicações/createAsOwner: Atribuir esta permissão resulta em que o criador seja adicionado como o primeiro proprietário do registo de aplicações criado, e o registo de aplicações criado contará com a quota de objetos criado pelo criador.
- microsoft.diretório/aplicações/criar: Atribuir esta permissão resulta em que o criador não seja adicionado como o primeiro proprietário do registo de aplicações criado, e o registo de aplicações criado não contará com a quota de objetos criado pelo criador. Use esta permissão cuidadosamente, porque não há nada que impeça o cessionário de criar registos de aplicações até que a quota de nível de diretório seja atingida. Se ambas as permissões forem atribuídas, esta permissão tem precedência.

## <a name="create-a-custom-role-in-azure-ad-powershell"></a>Crie um papel personalizado no Azure AD PowerShell

### <a name="prepare-powershell"></a>Preparar PowerShell

Em primeiro lugar, instale o módulo Azure AD PowerShell da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Em seguida, importe o módulo de pré-visualização Azure AD PowerShell, utilizando o seguinte comando:

```powershell
import-module azureadpreview
```

Para verificar se o módulo está pronto a ser utilizado, combine a versão devolvida pelo seguinte comando ao indicado aqui:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role-in-azure-ad-powershell"></a>Crie o papel personalizado no Azure AD PowerShell

Crie um novo papel utilizando o seguinte script PowerShell:

```powershell

# Basic role information
$displayName = "Application Registration Creator"
$description = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
    "microsoft.directory/applications/createAsOwner"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customRole = New-AzureAdMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-role-in-azure-ad-powershell"></a>Atribuir o papel no Azure AD PowerShell

Atribua a função utilizando o seguinte script PowerShell:

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'Adam@contoso.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Registration Creator'"

# Get resource scope for assignment
$resourceScope = '/'

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-custom-role-in-the-microsoft-graph-api"></a>Crie um papel personalizado na Microsoft Graph API

HTTP solicitação para criar o papel personalizado.

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
                    "microsoft.directory/applications/createAsOwner"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-role-in-the-microsoft-graph-api"></a>Atribuir o papel na Microsoft Graph API

A atribuição de funções combina um ID principal de segurança (que pode ser um utilizador ou principal de serviço), uma definição de função (função) ID, e um âmbito de recurso AD Azure.

HTTP solicita para atribuir uma função personalizada.

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

- Sinta-se livre para compartilhar conosco no fórum de [funções administrativas Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Para obter mais informações sobre as atribuições de funções de Azure AD, consulte [as funções de administrador de atribuição](directory-assign-admin-roles.md).
- Para obter mais informações sobre permissões de utilizador predefinidos, consulte [a comparação das permissões de utilizador por defeito e do utilizador do membro.](../fundamentals/users-default-permissions.md)
