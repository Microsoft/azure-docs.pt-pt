---
title: Documentos de função de administrador entre Microsoft 365 serviços – Azure AD | Microsoft Docs
description: Encontre referências de conteúdo e API para funções de administrador para serviços de Microsoft 365 no Azure Active Directory
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024462"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Funções de administrador para serviços Microsoft 365s

Todos os produtos no Microsoft 365 podem ser gerenciados com funções administrativas no Azure AD. Alguns produtos também fornecem funções adicionais que são específicas para esse produto. Para obter informações sobre as funções com suporte em cada produto, consulte a tabela abaixo. Discussões gerais sobre problemas de delegação podem ser encontradas no [planejamento de delegação de função no Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Onde encontrar conteúdo

Serviço de Microsoft 365 | Conteúdo da função | Conteúdo da API
---------------------- | ------------------ | -----------------
Funções de administrador no Office 365 e Microsoft 365 planos de negócios | [Funções de administrador do Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Não disponível
Azure Active Directory (Azure AD) e Azure AD Identity Protection| [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Controle de acesso baseado em função do Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell para Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Buscar atribuições de função](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Funções de administrador do Azure AD](directory-assign-admin-roles.md)<br>Também [sobre a função de administrador do SharePoint no Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Equipes/Skype for Business | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Centro de Conformidade e Segurança (proteção avançada contra ameaças do Office 365, proteção do Exchange Online, proteção de informações) | [Funções de administrador do Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [PowerShell do Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Buscar atribuições de função](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Pontuação segura | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Gerente de conformidade | [Funções do Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Não disponível
Azure Information Protection | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Controlo de acesso baseado em funções](https://docs.microsoft.com/cloud-app-security/manage-admins) | [Referência da API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Proteção Avançada Contra Ameaças do Azure | [Grupos de funções do Azure ATP](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Não disponível
Proteção Avançada Contra Ameaças do Windows Defender | [Controle de acesso baseado em função do Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Não disponível
Privileged Identity Management | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Controle de acesso baseado em função do Intune](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Área de trabalho gerenciada | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Passos seguintes

* [Como atribuir ou remover funções de administrador do Azure AD](directory-manage-roles-portal.md)
* [Referência de funções de administrador do Azure AD](directory-assign-admin-roles.md)
