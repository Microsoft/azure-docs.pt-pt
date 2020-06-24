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
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5acfa98636f54f87facf9771beb7d94dbd2b324
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84731737"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Quickstart: Conceder permissão para criar registos ilimitados de aplicações

Neste arranque rápido, irá criar uma função personalizada com permissão para criar um número ilimitado de registos de aplicações e, em seguida, atribuir essa função a um utilizador. O utilizador designado pode então utilizar o portal AD AD Azure, O Azure AD PowerShell ou a Microsoft Graph API para criar registos de aplicações. Ao contrário da função de Desenvolvedor de Aplicações incorporada, esta função personalizada garante a capacidade de criar um número ilimitado de registos de aplicações. A função de Desenvolvedor de Aplicações concede a capacidade, mas o número total de objetos criados está limitado a 250 para evitar atingir [a quota de objetos em todo o diretório](directory-service-limits-restrictions.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisite"></a>Pré-requisito

O papel menos privilegiado necessário para criar e atribuir funções personalizadas a Azure É o administrador privilegiado.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Crie um novo papel personalizado usando o portal AD AZure

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com)   com o administrador de função privilegiada ou permissões de administrador global na organização Azure AD.
1. Selecione **Azure Ative Directory**, selecione **Funções e administradores**, e, em seguida, selecione **Nova função personalizada**.

    ![Criar ou editar funções a partir da página Funções e administradores](./media/roles-create-custom/new-custom-role.png)

1. No separador **Básicos,** forneça "Criador de Registo de Aplicação" para o nome da função e "Pode criar um número ilimitado de registos de candidaturas" para a descrição da função e, em seguida, selecione **Seguinte**.

    ![fornecer um nome e descrição para um papel personalizado no separador Básicos](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. No separador **Permissões,** introduza "microsoft.directy/applications/create" na caixa de pesquisa e, em seguida, selecione as caixas de verificação ao lado das permissões desejadas e, em seguida, selecione **Seguinte**.

    ![Selecione as permissões para uma função personalizada no separador Permissões](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. No **separador 'Rever +' criar,** rever as permissões e selecionar **Criar.**

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Atribuir a função a um utilizador utilizando o portal AD AZure

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com)   com o administrador privilegiado ou permissões de administrador global na sua organização Azure AD.
1. Selecione **O Diretório Ativo Azure** e, em seguida, selecione **Funções e administradores**.
1. Selecione a função de Criador de Registo de Inscrição de Aplicação e **selecione a atribuição de adicionar.**
1. Selecione o utilizador pretendido e clique **em Selecionar** para adicionar o utilizador à função.

E já está! Neste quickstart, criou com sucesso um papel personalizado com permissão para criar um número ilimitado de registos de aplicações e, em seguida, atribuir esse papel a um utilizador.

> [!TIP]
> Para atribuir a função a uma aplicação utilizando o portal Azure AD, insira o nome da aplicação na caixa de pesquisa da página de atribuição. As aplicações não são apresentadas na lista por padrão, mas são devolvidas em resultados de pesquisa.

### <a name="app-registration-permissions"></a>Permissões de registo de aplicativos

Existem duas permissões disponíveis para a concessão da capacidade de criar registos de candidaturas, cada uma com comportamentos diferentes.

- microsoft.diretório/aplicações/createAsOwner: Atribuir esta permissão resulta em que o criador seja adicionado como o primeiro proprietário do registo de aplicações criado, e o registo de aplicações criado contará com a quota de objetos criado pelo criador.
- microsoft.diretório/aplicaçãoPolicies/create: Atribuir esta permissão resulta em que o criador não seja adicionado como o primeiro proprietário do registo de aplicações criado, e o registo de aplicações criado não contará com a quota de objetos criado pelo criador. Use esta permissão cuidadosamente, porque não há nada que impeça o cessionário de criar registos de aplicações até que a quota de nível de diretório seja atingida. Se ambas as permissões forem atribuídas, esta permissão tem precedência.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Crie um papel personalizado usando Azure AD PowerShell

Crie um novo papel utilizando o seguinte script PowerShell:

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Atribua o papel personalizado usando Azure AD PowerShell

#### <a name="prepare-powershell"></a>Preparar PowerShell

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

#### <a name="assign-the-custom-role"></a>Atribuir o papel personalizado

Atribua a função utilizando o script abaixo do PowerShell:

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

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Crie um papel personalizado usando a Microsoft Graph API

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
- Para obter mais informações sobre funções e atribuição de funções de Administrador, consulte [as funções de administrador de atribuição](directory-assign-admin-roles.md).
- Para obter permissões de utilizador predefinidos, consulte uma [comparação entre as permissões de utilizador e de hóspedes predefinidos](../fundamentals/users-default-permissions.md).
