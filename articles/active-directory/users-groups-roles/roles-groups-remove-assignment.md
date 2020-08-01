---
title: Remover atribuições de funções de um grupo no Azure Ative Directory Microsoft Docs
description: Pré-visualizar funções Azure AD personalizadas para delegar a gestão de identidade. Gerir as funções Azure no portal Azure, PowerShell ou Graph API.
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
ms.openlocfilehash: de09fddf6b02cfd57504994b4b8d3f7dc4254870
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476217"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Remover atribuições de funções de um grupo no Azure Ative Directory

Este artigo descreve como um administrador de TI pode remover as funções AD AZure atribuídas a grupos. No portal Azure, pode agora remover atribuições de funções diretas e indiretas a um utilizador. Se um utilizador for atribuído a uma função por uma associação de grupo, remova o utilizador do grupo para remover a atribuição de funções.

## <a name="using-azure-admin-center"></a>Usando o centro de administração Azure

1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com o administrador privilegiado ou permissões de administrador global na organização Azure AD.

1. Selecione **Funções e nome de**  >  ***função***de administradores .

1. Selecione o grupo a partir do qual pretende remover a atribuição de funções e selecione **Remover a atribuição**.

   ![Remova uma tarefa de função de um grupo selecionado.](./media/roles-groups-remove-assignment/remove-assignment.png)

1. Quando lhe pedirem para confirmar a sua ação, selecione **Sim**.

## <a name="using-powershell"></a>Com o PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Criar um grupo que possa ser atribuído ao papel

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>Obtenha a definição de papel que deseja atribuir ao grupo

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>Criar uma atribuição de funções

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>Remover a atribuição de funções

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Utilização da Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Criar um grupo que possa ser atribuído a um papel AD AZure

```powershell
POST https://graph.microsoft.com/beta/groups

{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
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

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Criar a atribuição de funções

```powershell
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<Id of role definition>",
"resourceScope":"/"
}
```

### <a name="delete-role-assignment"></a>Eliminar atribuição de funções

```powershell
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/<Id of role assignment>
```

## <a name="next-steps"></a>Passos seguintes

- [Use grupos de nuvem para gerir atribuições de funções](roles-groups-concept.md)
- [Funções de resolução de problemas atribuídas a grupos de nuvem](roles-groups-faq-troubleshooting.md)
