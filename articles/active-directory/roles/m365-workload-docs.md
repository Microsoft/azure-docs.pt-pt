---
title: Docs de função de administração em todos os serviços da Microsoft 365 - Azure AD ! Microsoft Docs
description: Encontre conteúdo e referências de API para funções de administrador para serviços microsoft 365 em Azure Ative Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8178f0753cd6a93caac2f9dece62f21e1c1b311d
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378895"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Funções de administrador para serviços Microsoft 365

Todos os produtos da Microsoft 365 podem ser geridos com funções administrativas no Azure AD. Alguns produtos também fornecem funções adicionais específicas a esse produto. Para obter informações sobre as funções suportadas por cada produto, consulte a tabela abaixo. As discussões gerais sobre questões de delegação podem ser encontradas no [planeamento da delegação de funções no Diretório Ativo do Azure.](concept-delegation.md)

## <a name="where-to-find-content"></a>Onde encontrar conteúdo

Serviço Microsoft 365 | Conteúdo de função | Conteúdo da API
---------------------- | ------------------ | -----------------
Funções de administração no Office 365 e Microsoft 365 planos de negócios | [Microsoft 365 funções de administração](/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Não disponível
Diretório Ativo Azure (Azure AD) e Proteção de Identidade AZURE AD| [Funções de administração da AD Azure](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Ir buscar atribuições de papéis](/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Controlo de acesso baseado em funções de troca](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell para troca](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Ir buscar atribuições de papéis](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Funções de administração da AD Azure](permissions-reference.md)<br>Também [sobre o papel de administrador do SharePoint na Microsoft 365](/sharepoint/sharepoint-admin-role) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Ir buscar atribuições de papéis](/graph/api/directoryrole-list?view=graph-rest-1.0)
Equipas/Skype para Negócios | [Funções de administração da AD Azure](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Ir buscar atribuições de papéis](/graph/api/directoryrole-list?view=graph-rest-1.0)
Centro de Conformidade & de Segurança (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Funções de administrador do Office 365](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Bolsa powershell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Ir buscar atribuições de papéis](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Pontuação Segura | [Funções de administração da AD Azure](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Ir buscar atribuições de papéis](/graph/api/directoryrole-list?view=graph-rest-1.0)
Gestor de Conformidade | [Funções de Gestor de Conformidade](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Não disponível
Azure Information Protection | [Funções de administração da AD Azure](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Ir buscar atribuições de papéis](/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Controlo de acesso baseado em funções](/cloud-app-security/manage-admins) | [Referência da API](/cloud-app-security/api-tokens) 
Proteção Avançada Contra Ameaças do Azure | [Grupos de funções da ATP do Azure](/azure-advanced-threat-protection/atp-role-groups) | Não disponível
Proteção Avançada Contra Ameaças do Windows Defender | [Controlo de acesso baseado em funções do Windows Defender ATP](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Não disponível
Privileged Identity Management | [Funções de administração da AD Azure](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Ir buscar atribuições de papéis](/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Controlo de acesso baseado em funções intune](/intune/role-based-access-control) | [Graph API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Ir buscar atribuições de papéis](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Desktop gerido | [Funções de administração da AD Azure](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Ir buscar atribuições de papéis](/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Passos seguintes

* [Como atribuir ou remover funções de administrador AD da Azure](manage-roles-portal.md)
* [Referência de funções de administrador da AD Azure](permissions-reference.md)