---
title: Remova atribuições de funções de um grupo no Azure Ative Directory | Microsoft Docs
description: Pré-visualizar funções Azure AD personalizadas para delegar a gestão de identidade. Gerir as funções Azure no portal Azure, PowerShell ou Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78ed23f563fce9760768a99e5bbf58f68500d665
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012025"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Remover atribuições de funções de um grupo no Azure Ative Directory

Este artigo descreve como um administrador de TI pode remover as funções AD AZure atribuídas a grupos. No portal Azure, pode agora remover atribuições de funções diretas e indiretas a um utilizador. Se um utilizador for atribuído a uma função por uma associação de grupo, remova o utilizador do grupo para remover a atribuição de funções.

## <a name="using-azure-admin-center"></a>Usando o centro de administração Azure

1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com o administrador privilegiado ou permissões de administrador global na organização Azure AD.

1. Selecione **Funções e nome de**  >  **_função_** de administradores .

1. Selecione o grupo a partir do qual pretende remover a atribuição de funções e selecione **Remover a atribuição**.

   ![Remova uma tarefa de função de um grupo selecionado.](./media/groups-remove-assignment/remove-assignment.png)

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

```http
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

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=displayName+eq+'Helpdesk Administrator'
```

### <a name="create-the-role-assignment"></a>Criar a atribuição de funções

```http
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"{object-id-of-group}",
"roleDefinitionId":"{role-definition-id}",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>Eliminar atribuição de funções

```http
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/{role-assignment-id}
```

## <a name="next-steps"></a>Passos seguintes

- [Utilizar os grupos da cloud para gerir atribuições de funções](groups-concept.md)
- [Resolver problemas de funções atribuídas a grupos de cloud](groups-faq-troubleshooting.md)
