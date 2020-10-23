---
title: Ver funções atribuídas a um grupo no Azure Ative Directory Microsoft Docs
description: Saiba como as funções atribuídas a um grupo podem ser vistas usando o centro de administração Azure AD. Os grupos de visualização e as funções atribuídas são permissões de utilizador predefinidas.
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
ms.openlocfilehash: e578c90a05085df33c2d547402256cfc38229aa3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377535"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Ver funções atribuídas a um grupo no Azure Ative Directory

Esta secção descreve como as funções atribuídas a um grupo podem ser visualizadas usando o centro de administração Admin Azure. Os grupos de visualização e as funções atribuídas são permissões de utilizador predefinidas.

1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com quaisquer credenciais não administradas ou administradas.

1. Selecione o grupo em que está interessado.

1. Selecione **funções atribuídas**. Agora pode ver todas as funções AD AZure atribuídas a este grupo.

   ![Ver todas as funções atribuídas a um grupo selecionado](./media/groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>Utilizar o PowerShell

### <a name="get-object-id-of-the-group"></a>Obtenha o objeto ID do grupo

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>Ver atribuição de funções a um grupo

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Utilização da Microsoft Graph API

### <a name="get-object-id-of-the-group"></a>Obtenha o objeto ID do grupo

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>Obtenha atribuições de papéis para um grupo

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Passos seguintes

- [Use grupos de nuvem para gerir atribuições de funções](groups-concept.md)
- [Resolver problemas de funções atribuídas a grupos de cloud](groups-faq-troubleshooting.md)
