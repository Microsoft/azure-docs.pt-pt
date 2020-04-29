---
title: Ver atribuições de papéis personalizados no portal Azure AD [ Microsoft Docs
description: Agora pode ver e gerir membros de um papel de administrador da AD Azure no centro de administração da Azure AD.
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
ms.openlocfilehash: f04bd7788a9cc9657e14aedfb153182d6e53eb95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79259711"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Ver atribuições de papéis personalizados no Diretório Ativo Azure

Este artigo descreve como visualizar papéis personalizados que atribuiu no Azure Ative Directory (Azure AD). No Azure Ative Directory (Azure AD), as funções podem ser atribuídas num âmbito de aplicação única ou com um âmbito de aplicação única.

- As atribuições de funções no âmbito da organização são adicionadas e podem ser vistas na lista de atribuições de papéis de aplicação única.
- As atribuições de funções no âmbito de aplicação única não são adicionadas e não podem ser vistas na lista de atribuições de âmbito de aplicação em toda a organização.

## <a name="view-role-assignments-in-the-azure-portal"></a>Ver atribuições de papéis no portal Azure

Este procedimento descreve as atribuições de visualização de um papel com âmbito de organização.

1. Inscreva-se no centro de [administração da Azure AD](https://aad.portal.azure.com)com o administrador privilegiado ou permissões de administrador global na organização Azure AD.
1. Selecione **Azure Ative Directory**, selecione **Funções e administradores,** e, em seguida, selecione uma função para abri-lo e ver as suas propriedades.
1. Selecione **Atribuições** para visualizar as atribuições para o papel.

    ![Ver atribuições e permissões de papéis quando abrir um papel a partir da lista](./media/roles-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Ver atribuições de papéis usando a Azure AD PowerShell

Esta secção descreve tarefas de visualização de um papel com âmbito de organização. Este artigo utiliza o módulo PowerShell Version 2 do [Diretório Ativo Azure.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) Para visualizar as atribuições de âmbito de aplicação única utilizando o PowerShell, pode utilizar os cmdlets em [atribuir funções personalizadas com powerShell](roles-assign-powershell.md).

### <a name="prepare-powershell"></a>Preparar powerShell

Primeiro, tem de descarregar o módulo powerShell de [pré-visualização da AD Azure](https://www.powershellgallery.com/packages/AzureAD/).

Para instalar o módulo PowerShell Azure AD, utilize os seguintes comandos:

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

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Ver atribuições de papéis usando a Microsoft Graph API

Esta secção descreve tarefas de visualização de um papel com âmbito de organização.  Para visualizar as atribuições de âmbito de aplicação única utilizando a API do Gráfico, pode utilizar as operações em [atribuir funções personalizadas com a API do Gráfico](roles-assign-graph.md).

HTTP solicita obter uma atribuição de funções para uma determinada definição de papel.

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

Esta secção descreve atribuições de visualização de um papel com âmbito de aplicação única. Esta funcionalidade encontra-se atualmente em pré-visualização pública.

1. Inscreva-se no centro de [administração da Azure AD](https://aad.portal.azure.com)com o administrador privilegiado ou permissões de administrador global na organização Azure AD.
1. Selecione **registos**de Apps e, em seguida, selecione o registo da aplicação para ver as suas propriedades. Poderá ter de selecionar **Todas as aplicações** para ver a lista completa de registos de aplicações na sua organização Azure AD.

    ![Criar ou editar registos de aplicações a partir da página de registos da App](./media/roles-create-custom/appreg-all-apps.png)

1. No registo da aplicação, selecione **Funções e administradores,** e, em seguida, selecione uma função para visualizar as suas propriedades.

    ![Ver atribuições de funções de inscrição de aplicativos a partir da página de registos da App](./media/roles-view-assignments/appreg-assignments.png)

1. Selecione **Atribuições** para visualizar as atribuições para o papel. A abertura da vista de atribuição a partir do registo da aplicação mostra-lhe as atribuições que são vias para este recurso Azure AD.

    ![Ver atribuições de funções de inscrição de aplicativos a partir das propriedades de um registo de aplicações](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Passos seguintes

* Sinta-se livre para partilhar connosco no fórum de [funções administrativas da Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
* Para mais informações sobre funções e atribuição de funções de administrador, consulte [as funções de administrador de atribuição](directory-assign-admin-roles.md).
* Para obter permissões de utilizador predefinidas, consulte uma [comparação das permissões padrão dos hóspedes e dos utilizadores dos membros](../fundamentals/users-default-permissions.md).
