---
title: Ver funções atribuídas a um grupo no Azure Ative Directory Microsoft Docs
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
ms.openlocfilehash: 3b7613fb70299a70e4389b97c2647a26cb7c3374
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476212"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Ver funções atribuídas a um grupo no Azure Ative Directory

Esta secção descreve como as funções atribuídas a um grupo podem ser visualizadas usando o centro de administração Admin Azure. Os grupos de visualização e as funções atribuídas são permissões de utilizador predefinidas.

1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com quaisquer credenciais não administradas ou administradas.

1. Selecione o grupo em que está interessado.

1. Selecione **funções atribuídas**. Agora pode ver todas as funções AD AZure atribuídas a este grupo.

   ![Ver todas as funções atribuídas a um grupo selecionado](./media/roles-groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>Com o PowerShell

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

- [Use grupos de nuvem para gerir atribuições de funções](roles-groups-concept.md)
- [Funções de resolução de problemas atribuídas a grupos de nuvem](roles-groups-faq-troubleshooting.md)
