---
title: Ver atribuições de funções personalizadas no portal Azure Ative Directory ! Microsoft Docs
description: Agora pode ver e gerir membros de um papel de administrador do Azure Ative Directory no centro de administração do Azure Ative Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9f7c6ec0917adbca5b44feee1ec285f4337ac4c
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579033"
---
# <a name="view-custom-role-assignments-using-azure-active-directory"></a>Ver atribuições de funções personalizadas usando O Diretório Ativo Azure

Este artigo descreve como visualizar funções personalizadas que atribuiu no Azure Ative Directory (Azure AD). No Azure Ative Directory (Azure AD), as funções podem ser atribuídas num âmbito de organização ou com um âmbito de aplicação único.

- As atribuições de funções no âmbito da organização são adicionadas e podem ser vistas na lista de atribuições de funções de candidatura única.
- As atribuições de funções no âmbito de aplicação única não são adicionadas e não podem ser vistas na lista de atribuições de âmbito de organização.

## <a name="view-role-assignments-in-the-azure-portal"></a>Ver atribuições de funções no portal Azure

Este procedimento descreve atribuições de visualização de um papel com âmbito de organização.

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com o administrador privilegiado ou permissões de administrador global na organização Azure AD.
1. Selecione **Azure Ative Directory** , selecione **Roles e administradores** , e, em seguida, selecione uma função para abri-lo e ver as suas propriedades.
1. Selecione **Atribuições** para visualizar as atribuições para o papel.

    ![Ver atribuições de funções e permissões quando abrir um papel da lista](./media/view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Ver atribuições de funções usando Azure AD PowerShell

Esta secção descreve atribuições de visualização de um papel com âmbito de organização. Este artigo utiliza o módulo [Azure Ative Directory PowerShell Version 2.](/powershell/module/azuread/?view=azureadps-2.0#directory_roles) Para visualizar as atribuições de âmbito de aplicação única utilizando o PowerShell, pode utilizar os cmdlets em [Atribuir funções personalizadas com PowerShell](custom-assign-powershell.md).

### <a name="prepare-powershell"></a>Preparar PowerShell

Em primeiro lugar, tem de [descarregar o módulo PowerShell de pré-visualização Azure AD](https://www.powershellgallery.com/packages/AzureAD/).

Para instalar o módulo Azure AD PowerShell, utilize os seguintes comandos:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Para verificar se o módulo está pronto a ser utilizado, utilize o seguinte comando:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Ver as atribuições de um papel

Exemplo de ver as atribuições de um papel.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Ver atribuições de funções utilizando a Microsoft Graph API

Esta secção descreve atribuições de visualização de um papel com âmbito de organização.  Para visualizar as atribuições de âmbito de aplicação única utilizando a API do gráfico, pode utilizar as operações em [Atribuir funções personalizadas com API de gráficos.](custom-assign-graph.md)

HTTP solicita para obter uma atribuição de papel para uma determinada definição de função.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Resposta

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>Ver atribuições de âmbito de aplicação única

Esta secção descreve atribuições de visualização de uma função com âmbito de aplicação única. Esta funcionalidade encontra-se atualmente em visualização pública.

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com o administrador privilegiado ou permissões de administrador global na organização Azure AD.
1. Selecione **registos de Aplicações** e, em seguida, selecione o registo da aplicação para ver as suas propriedades. Poderá ter de selecionar **todas as aplicações** para ver a lista completa de registos de aplicações na sua organização AZure AD.

    ![Criar ou editar registos de aplicações a partir da página de registos da App](./media/view-assignments/app-reg-all-apps.png)

1. No registo da aplicação, selecione **Roles e administradores** , e, em seguida, selecione uma função para ver as suas propriedades.

    ![Ver atribuições de funções de registo de aplicativos a partir da página de registos da App](./media/view-assignments/app-reg-assignments.png)

1. Selecione **Atribuições** para visualizar as atribuições para o papel. A abertura da vista de atribuições a partir do registo da aplicação mostra-lhe as atribuições que são procuradas neste recurso AD AZure.

    ![Ver atribuições de funções de registo de aplicativos a partir das propriedades de um registo de aplicações](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Passos seguintes

* Sinta-se livre para compartilhar conosco no fórum de [funções administrativas Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
* Para obter mais informações sobre funções e atribuição de funções de Administrador, consulte [as funções de administrador de atribuição](permissions-reference.md).
* Para obter permissões de utilizador predefinidos, consulte uma [comparação entre as permissões de utilizador e de hóspedes predefinidos](../fundamentals/users-default-permissions.md).