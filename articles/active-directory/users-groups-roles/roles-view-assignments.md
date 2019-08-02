---
title: Exibir permissões de função de administrador no centro de administração-Azure Active Directory | Microsoft Docs
description: Agora você pode ver e gerenciar membros de uma função de administrador do Azure AD no centro de administração do Azure AD.
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
ms.openlocfilehash: bf45d45481712e30d40bacec9a3c4d80d1ed56b6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707555"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Exibir atribuições de função personalizadas no Azure Active Directory

Este artigo descreve como exibir funções personalizadas que você atribuiu no Azure Active Directory (Azure AD). No Azure Active Directory (AD do Azure), as funções podem ser atribuídas no nível do diretório ou com um escopo de um único aplicativo. As atribuições de função no escopo de diretório são adicionadas à lista de atribuições de função de aplicativo único, mas as atribuições de função no escopo de aplicativo único não são adicionadas à lista de atribuições de nível de diretório.

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-the-azure-ad-portal"></a>Exibir as atribuições de uma função com escopo de diretório usando o portal do AD do Azure

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com)com permissões de administrador de função privilegiada ou de administrador global na organização do Azure AD.
1. Selecione **Azure Active Directory**, selecione **funções e administradores**e, em seguida, selecione uma função para abri-la e exibir suas propriedades.
1. Selecione **atribuições** para exibir as atribuições da função.

    ![Exibir atribuições de função e permissões ao abrir uma função da lista](./media/roles-view-assignments/role-assignments.png)

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-azure-ad-powershell"></a>Exibir as atribuições de uma função com escopo de diretório usando o PowerShell do Azure AD

Você pode automatizar a forma como atribui funções de administrador do Azure AD a usuários usando Azure PowerShell. Este artigo usa o módulo [Azure Active Directory PowerShell versão 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) .

### <a name="prepare-powershell"></a>Preparar o PowerShell

Primeiro, você deve [baixar o módulo PowerShell de visualização do Azure ad](https://www.powershellgallery.com/packages/AzureAD/).

Para instalar o módulo do PowerShell do Azure AD, use os seguintes comandos:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Para verificar se o módulo está pronto para uso, use o seguinte comando:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Exibir as atribuições de uma função

Exemplo de exibição das atribuições de uma função.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-microsoft-graph-api"></a>Exibir as atribuições de uma função com escopo de diretório usando Microsoft Graph API

Solicitação HTTP para obter uma atribuição de função para uma determinada definição de função.

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
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

## <a name="view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview"></a>Exibir as atribuições de uma função com escopo de aplicativo único usando o portal do AD do Azure (versão prévia)

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com)com permissões de administrador de função privilegiada ou de administrador global na organização do Azure AD.
1. Selecione Azure Active Directory, selecione **registros de aplicativo**e, em seguida, selecione o registro do aplicativo para exibir suas propriedades. Talvez seja necessário selecionar **todos os aplicativos** para ver a lista completa de registros de aplicativo na sua organização do Azure AD.

    ![Criar ou editar registros de aplicativo na página Registros de aplicativo](./media/roles-create-custom/appreg-all-apps.png)

1. Selecione **funções e administradores**e, em seguida, selecione uma função para exibir suas propriedades.

    ![Exibir atribuições de função de registro de aplicativo na página Registros de aplicativo](./media/roles-view-assignments/appreg-assignments.png)

1. Selecione **atribuições** para exibir as atribuições da função.

    ![Exibir atribuições de função de registro de aplicativo das propriedades de um registro de aplicativo](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Passos Seguintes

* Fique à vontade para compartilhar conosco no [Fórum de funções administrativas do Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Para obter mais informações sobre funções e atribuição de função de administrador, consulte [atribuir funções de administrador](directory-assign-admin-roles.md).
* Para permissões de usuário padrão, consulte uma [comparação das permissões padrão de usuário convidado e membro](../fundamentals/users-default-permissions.md).
