---
title: Ver funções atribuídas a um grupo no Azure Ative Directory | Microsoft Docs
description: Saiba como as funções atribuídas a um grupo podem ser vistas usando o centro de administração Azure AD. Os grupos de visualização e as funções atribuídas são permissões de utilizador predefinidas.
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
ms.openlocfilehash: 1a1939be42126606fdae261e60c890c71374c894
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741830"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Ver funções atribuídas a um grupo no Azure Ative Directory

Esta secção descreve como as funções atribuídas a um grupo podem ser visualizadas usando o centro de administração Admin Azure. Os grupos de visualização e as funções atribuídas são permissões de utilizador predefinidas.

1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com quaisquer credenciais não administradas ou administradas.

1. Selecione o grupo em que está interessado.

1. Selecione **funções atribuídas**. Agora pode ver todas as funções AD AZure atribuídas a este grupo.

   ![Ver todas as funções atribuídas a um grupo selecionado](./media/groups-view-assignments/view-assignments.png)

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

## <a name="next-steps"></a>Próximos passos

- [Utilizar os grupos da cloud para gerir atribuições de funções](groups-concept.md)
- [Resolver problemas de funções atribuídas a grupos de cloud](groups-faq-troubleshooting.md)
