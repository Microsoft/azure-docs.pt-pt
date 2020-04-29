---
title: Remover limites na criação de registos de aplicações - Azure AD / Microsoft Docs
description: Atribuir um papel personalizado para conceder registos de aplicações sem restrições no Diretório Ativo da AD Azure
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
ms.openlocfilehash: 7acd76ff45f783f614b2a1d3f0d5c10d800a1ea9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77559050"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Quickstart: Conceder permissão para criar registos ilimitados de aplicações

Neste arranque rápido, irá criar uma função personalizada com permissão para criar um número ilimitado de registos de aplicações e, em seguida, atribuir essa função a um utilizador. O utilizador designado pode então utilizar o portal Azure AD, O PowerShell Azure AD ou a Microsoft Graph API para criar registos de aplicações. Ao contrário do papel de Desenvolvedor de Aplicações incorporado, este papel personalizado confere a capacidade de criar um número ilimitado de inscrições de aplicações. O papel de Desenvolvedor de Aplicações concede a capacidade, mas o número total de objetos criados está limitado a 250 para evitar atingir a quota de objetos em [toda a direção.](directory-service-limits-restrictions.md)

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisite"></a>Pré-requisito

O papel menos privilegiado necessário para criar e atribuir funções personalizadas da Azure AD é o administrador de Funções Privilegiadas.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Criar uma nova função personalizada usando o portal Azure AD

1. Inscreva-se no centro de [administração da Azure AD](https://aad.portal.azure.com)com administrador de funções privilegiadas ou permissões de administrador global na organização Azure AD.
1. Selecione **Diretório Ativo Azure,** selecione **Funções e administradores,** e, em seguida, selecione **Nova função personalizada**.

    ![Criar ou editar funções a partir da página Funções e administradores](./media/roles-create-custom/new-custom-role.png)

1. No separador **Basics,** forneça "Application Registration Creator" para o nome da função e "Pode criar um número ilimitado de inscrições de candidatura" para a descrição do papel e, em seguida, selecionar **Next**.

    ![fornecer um nome e descrição para um papel personalizado no separador Basics](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. No separador **Permissões,** introduza "microsoft.directy/applications/create" na caixa de pesquisa e, em seguida, selecione as caixas de verificação ao lado das permissões desejadas e, em seguida, selecione **Next**.

    ![Selecione as permissões para um papel personalizado no separador Permissões](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. No **separador Review + criar,** reveja as permissões e selecione **Criar**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Atribuir a função a um utilizador que utilize o portal Azure AD

1. Inscreva-se no centro de [administração da Azure AD](https://aad.portal.azure.com)com o administrador privilegiado ou permissões de administrador global na sua organização Azure AD.
1. Selecione **Diretório Ativo Azure** e, em seguida, selecione **Funções e administradores**.
1. Selecione a função criador de registo de inscrição de aplicação e **selecione Adicionar a tarefa**.
1. Selecione o utilizador pretendido e clique **em Selecionar** para adicionar o utilizador à função.

E já está! Neste arranque rápido, criou com sucesso um papel personalizado com permissão para criar um número ilimitado de registos de aplicações e, em seguida, atribuir esse papel a um utilizador.

> [!TIP]
> Para atribuir a função a uma aplicação utilizando o portal Azure AD, insira o nome da aplicação na caixa de pesquisa da página de atribuição. As aplicações não são apresentadas na lista por padrão, mas são devolvidas nos resultados da pesquisa.

### <a name="app-registration-permissions"></a>Permissões de registo de aplicações

Existem duas permissões disponíveis para a concessão da capacidade de criar registos de candidaturas, cada uma com comportamentos diferentes.

- microsoft.directy/applications/createAsOwner: Atribuir esta permissão resulta na adição do criador como o primeiro proprietário do registo de aplicações criado, e o registo de aplicações criado contará com a quota de 250 objetos criados pelo criador.
- microsoft.directy/applicationPolicies/create: Atribuir esta permissão resulta em não ser adicionado o criador como o primeiro proprietário do registo de aplicações criado, e o registo de aplicações criado não contará com a quota de 250 objetos criados pelo criador. Utilize esta permissão cuidadosamente, pois não há nada que impeça o designado de criar registos de aplicações até que a quota de nível de diretório seja atingida. Se ambas as permissões forem atribuídas, esta permissão tem precedência.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Criar uma função personalizada usando o Azure AD PowerShell

Criar uma nova função utilizando o seguinte script PowerShell:

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

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Atribuir a função personalizada usando a Azure AD PowerShell

#### <a name="prepare-powershell"></a>Preparar powerShell

Em primeiro lugar, instale o módulo PowerShell Azure AD a partir da [Galeria PowerShell.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17) Em seguida, importe o módulo de pré-visualização Azure AD PowerShell, utilizando o seguinte comando:

```powershell
import-module azureadpreview
```

Para verificar se o módulo está pronto a ser utilizado, corresponda à versão devolvida pelo seguinte comando ao indicado aqui:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Atribuir o papel personalizado

Atribuir a função utilizando o script abaixo powerShell:

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

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Criar uma função personalizada usando a Microsoft Graph API

HTTP solicita para criar o papel personalizado.

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

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Atribuir a função personalizada usando a Microsoft Graph API

A atribuição de funções combina um ID principal de segurança (que pode ser um utilizador ou um principal de serviço), um ID de definição de função (função) e um âmbito de recurso Azure AD.

PEDIDO HTTP para atribuir uma função personalizada.

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

- Sinta-se livre para partilhar connosco no fórum de [funções administrativas da Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Para mais informações sobre funções e atribuição de funções de administrador, consulte [as funções de administrador de atribuição](directory-assign-admin-roles.md).
- Para obter permissões de utilizador predefinidas, consulte uma [comparação das permissões padrão dos hóspedes e dos utilizadores dos membros](../fundamentals/users-default-permissions.md).
