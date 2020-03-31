---
title: Administração docs em todos os serviços da Microsoft 365 - Azure AD [ Microsoft Docs
description: Encontre referências de conteúdo e API para funções de administrador para serviços Microsoft 365 no Diretório Ativo Do Azure
services: active-directory
documentationcenter: ''
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
ms.openlocfilehash: 430b65217da33185cd3b5c235fb148f3350e1bfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024462"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Funções de administrador para serviços Microsoft 365

Todos os produtos da Microsoft 365 podem ser geridos com funções administrativas em Azure AD. Alguns produtos também fornecem papéis adicionais específicos a esse produto. Para obter informações sobre as funções suportadas por cada produto, consulte a tabela abaixo. As discussões gerais sobre as questões da delegação podem ser encontradas no planeamento da [delegação de funções no Diretório Ativo do Azure.](roles-concept-delegation.md)

## <a name="where-to-find-content"></a>Onde encontrar conteúdo

Serviço Microsoft 365 | Conteúdo de funções | Conteúdo da API
---------------------- | ------------------ | -----------------
Funções de administrador nos planos de negócios do Office 365 e Microsoft 365 | [Funções de administrador do Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Não disponível
Diretório Ativo Azure (Azure AD) e Azure AD Identity Protection| [Funções de administrador ad-inazure](directory-assign-admin-roles.md) | [Gráfico API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de papéis](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Controlo de acesso baseado em papéis de troca](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell para Troca](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Buscar atribuições de papéis](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Funções de administrador ad-inazure](directory-assign-admin-roles.md)<br>Também [sobre o papel de administrador do SharePoint no Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Gráfico API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de papéis](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Equipas/Skype para Negócios | [Funções de administrador ad-inazure](directory-assign-admin-roles.md) | [Gráfico API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de papéis](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Security & Compliance Center (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Funções de administrador do Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Buscar atribuições de papéis](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Secure Score (Classificação de segurança) | [Funções de administrador ad-inazure](directory-assign-admin-roles.md) | [Gráfico API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de papéis](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Gestor de Conformidade | [Funções do Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Não disponível
Azure Information Protection | [Funções de administrador ad-inazure](directory-assign-admin-roles.md) | [Gráfico API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de papéis](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Controlo de acesso baseado em funções](https://docs.microsoft.com/cloud-app-security/manage-admins) | [Referência da API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Proteção Avançada Contra Ameaças do Azure | [Grupos de funções da ATP do Azure](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Não disponível
Proteção Avançada Contra Ameaças do Windows Defender | [Controlo de acesso baseado em funções do Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Não disponível
Privileged Identity Management | [Funções de administrador ad-inazure](directory-assign-admin-roles.md) | [Gráfico API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de papéis](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Controlo de acesso baseado em funções intonizado](https://docs.microsoft.com/intune/role-based-access-control) | [Gráfico API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Buscar atribuições de papéis](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Ambiente de Trabalho Gerido | [Funções de administrador ad-inazure](directory-assign-admin-roles.md) | [Gráfico API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de papéis](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Passos seguintes

* [Como atribuir ou remover funções de administrador da AD Azure](directory-manage-roles-portal.md)
* [Referência de funções de administrador da AD Azure](directory-assign-admin-roles.md)
