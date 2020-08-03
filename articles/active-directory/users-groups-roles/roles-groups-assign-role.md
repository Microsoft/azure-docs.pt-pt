---
title: Atribuir um papel a um grupo em nuvem no Azure Ative Directory Microsoft Docs
description: Atribua um papel AD a um grupo atribuível a funções no portal Azure, PowerShell ou API de gráficos.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f45b5709369dfc2025b55bc3acec69e9328ce403
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513422"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>Atribuir um papel a um grupo de nuvem no Azure Ative Directory

Esta secção descreve como um administrador de TI pode atribuir o papel de Azure Ative Directory (Azure AD) a um grupo AD Azure.

## <a name="using-azure-ad-admin-center"></a>Usando o centro de administração Ad da Adure

Atribuir um grupo a uma função AD Azure é semelhante à atribuição de utilizadores e principais de serviço, exceto que apenas podem ser utilizados grupos que atribuam funções. No portal Azure, apenas são apresentados grupos que sejam atribuíveis a papéis.

1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com o administrador privilegiado ou permissões de administrador global na organização Azure AD.

1. Selecione **Azure Ative Directory**  >  **Roles and administrators**, e selecione a função que pretende atribuir.

1. Na página de nomes de ***funções,*** selecione > **Adicionar a atribuição**.

   ![Adicione a nova atribuição de funções](./media/roles-groups-assign-role/add-assignment.png)

1. Selecione o grupo. Apenas são apresentados os grupos que podem ser atribuídos às funções AD do Azure.

    [![Apenas os grupos que são atribuíveis são mostrados para uma nova atribuição de funções.](media/roles-groups-assign-role/eligible-groups.png "Apenas os grupos que são atribuíveis são mostrados para uma nova atribuição de funções.")](media/roles-groups-assign-role/eligible-groups.png#lightbox)

1. Selecione **Adicionar**.

Para obter mais informações sobre a atribuição de permissões de funções, consulte [as funções de administrador de atribuição e não administrador para os utilizadores](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="using-powershell"></a>Com o PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Criar um grupo que possa ser atribuído ao papel

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>Obtenha a definição de função para o papel que quer atribuir

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>Criar uma atribuição de funções

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>Utilização da Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Criar um grupo que pode ser atribuído a Azure AD

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Obtenha a definição de papel

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Criar a atribuição de funções

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>Passos seguintes

- [Use grupos de nuvem para gerir atribuições de funções](roles-groups-concept.md)
- [Funções de resolução de problemas atribuídas a grupos de nuvem](roles-groups-faq-troubleshooting.md)
