---
title: Atribuições de funções de AD lista Azure
description: Agora pode ver e gerir membros de um papel de administrador do Azure Ative Directory no centro de administração do Azure Ative Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: de546ef091b1a8e996f286b0c9af45e93488b5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467662"
---
# <a name="list-azure-ad-role-assignments"></a>Atribuições de funções de AD lista Azure

Este artigo descreve como listar as funções que atribuiu no Azure Ative Directory (Azure AD). No Azure Ative Directory (Azure AD), as funções podem ser atribuídas num âmbito de organização ou com um âmbito de aplicação único.

- As atribuições de funções no âmbito da organização são adicionadas e podem ser vistas na lista de atribuições de funções de candidatura única.
- As atribuições de funções no âmbito de aplicação única não são adicionadas e não podem ser vistas na lista de atribuições de âmbito de organização.

## <a name="list-role-assignments-in-the-azure-portal"></a>Atribuições de funções de lista no portal Azure

Este procedimento descreve como listar atribuições de funções com âmbito de organização.

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com o administrador privilegiado ou permissões de administrador global na organização Azure AD.
1. Selecione **Azure Ative Directory**, selecione **Roles e administradores**, e, em seguida, selecione uma função para abri-lo e ver as suas propriedades.
1. Selecione **Atribuições** para listar as atribuições de funções.

    ![Listar atribuições e permissões de funções quando abrir um papel da lista](./media/view-assignments/role-assignments.png)

## <a name="list-my-role-assignments"></a>Listar as minhas atribuições de papéis

É fácil listar as suas próprias permissões também. Selecione **a sua função** na página **de funções e administradores** para ver as funções que lhe são atribuídas.

## <a name="download-role-assignments"></a>Descarregue atribuições de funções

Para descarregar todas as atribuições para uma função específica, na página **Roles e administradores,** selecione uma função e, em seguida, selecione **descarregamento de atribuições de funções**. Um ficheiro CSV que lista atribuições em todos os âmbitos para essa função é descarregado.

![baixar todas as atribuições para um papel](./media/view-assignments/download-role-assignments.png)

## <a name="list-role-assignments-using-azure-ad-powershell"></a>Atribuições de funções de lista usando Azure AD PowerShell

Esta secção descreve atribuições de visualização de um papel com âmbito de organização. Este artigo utiliza o módulo [Azure Ative Directory PowerShell Version 2.](/powershell/module/azuread/#directory_roles) Para visualizar as atribuições de âmbito de aplicação única utilizando o PowerShell, pode utilizar os cmdlets em [Atribuir funções personalizadas com PowerShell](custom-assign-powershell.md).

### <a name="prepare-powershell"></a>Preparar PowerShell

Em primeiro lugar, tem de [descarregar o módulo PowerShell de pré-visualização Azure AD](https://www.powershellgallery.com/packages/AzureAD/).

Para instalar o módulo Azure AD PowerShell, utilize os seguintes comandos:

``` PowerShell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Para verificar se o módulo está pronto a ser utilizado, utilize o seguinte comando:

``` PowerShell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="list-role-assignments"></a>Listar atribuições de função

Exemplo de listagem das atribuições de funções.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="list-role-assignments-using-microsoft-graph-api"></a>Atribuições de funções de lista usando Microsoft Graph API

Esta secção descreve como listar atribuições de funções com âmbito de organização.  Para listar atribuições de funções de âmbito de aplicação única utilizando a API do gráfico, pode utilizar as operações em [Atribuir funções personalizadas com API de gráficos.](custom-assign-graph.md)

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

## <a name="list-role-assignments-with-single-application-scope"></a>Atribuições de funções de lista com âmbito de aplicação única

Esta secção descreve como listar atribuições de funções com âmbito de aplicação única. Esta funcionalidade encontra-se atualmente em visualização pública.

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com o administrador privilegiado ou permissões de administrador global na organização Azure AD.
1. Selecione **registos de Aplicações** e, em seguida, selecione o registo da aplicação para ver as suas propriedades. Poderá ter de selecionar **todas as aplicações** para ver a lista completa de registos de aplicações na sua organização AZure AD.

    ![Criar ou editar registos de aplicações a partir da página de registos da App](./media/view-assignments/app-reg-all-apps.png)

1. No registo da aplicação, selecione **Roles e administradores**, e, em seguida, selecione uma função para ver as suas propriedades.

    ![Listar atribuições de funções de inscrição de aplicativos a partir da página de registos da App](./media/view-assignments/app-reg-assignments.png)

1. Selecione **Atribuições** para listar as atribuições de funções. A abertura da página de atribuições a partir do registo da aplicação mostra-lhe as atribuições de funções que são traçadas para este recurso AD Azure.

    ![Listar atribuições de funções de inscrição de aplicativos a partir das propriedades de um registo de aplicações](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Passos seguintes

* Sinta-se livre para compartilhar conosco no fórum de [funções administrativas Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
* Para obter mais informações sobre funções e atribuição de funções de Administrador, consulte [as funções de administrador de atribuição](permissions-reference.md).
* Para obter permissões de utilizador predefinidos, consulte uma [comparação entre as permissões de utilizador e de hóspedes predefinidos](../fundamentals/users-default-permissions.md).
