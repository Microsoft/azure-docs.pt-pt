---
title: Conteúdo de função de administrador para cargas de trabalho do Microsoft 365 - Azure AD | Documentos da Microsoft
description: Localizar conteúdo e referências de API para funções de administrador para cargas de trabalho do Microsoft 365 no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7460c3a7d957634d14186d313994c65d89fa4a32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60468186"
---
# <a name="administrator-roles-for-microsoft-365-workloads"></a>Funções de administrador para cargas de trabalho do Microsoft 365

Todos os produtos no Microsoft 365 podem ser geridos com funções administrativas no Azure AD. Alguns produtos também fornecem funções adicionais que são específicas para esse produto. Para obter informações sobre as funções suportadas por cada produto, consulte a tabela abaixo. Discussão geral dos problemas de delegação pode ser encontrado na [delegação de funções no Azure Active Directory de planeamento](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Onde encontrar o conteúdo

Carga de trabalho do Microsoft 365 | Conteúdo de função | Conteúdo da API
---------------------- | ------------------ | -----------------
Funções de administrador nos planos de negócios do Office 365 e o Microsoft 365 | [Funções de administrador do Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Não disponível
Azure Active Directory (Azure AD) e o Azure AD Identity Protection| [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Obter atribuições de funções](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Controlo de acesso baseado em funções do Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell para o Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Obter atribuições de funções](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Funções de administrador do Azure AD](directory-assign-admin-roles.md)<br>Também [sobre o SharePoint a função de administrador no Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Obter atribuições de funções](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
As equipes/Skype para empresas | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Obter atribuições de funções](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
(Office 365 avançada contra ameaças proteção, o Exchange Online Protection, proteção de informações) do Centro de conformidade e segurança | [Funções de administrador do Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Obter atribuições de funções](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Pontuação segura | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Obter atribuições de funções](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Gestor de conformidade | [Funções de Gestor de conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Não disponível
Azure Information Protection | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Obter atribuições de funções](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Controlo de acesso baseado em funções](https://docs.microsoft.com/cloud-app-security/manage-admins) | [Referência da API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Proteção Avançada Contra Ameaças do Azure | [Grupos de funções do Azure ATP](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Não disponível
Proteção Avançada Contra Ameaças do Windows Defender | [Controlo de acesso baseado em funções do Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Não disponível
Privileged Identity Management | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Obter atribuições de funções](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Controlo de acesso baseado em funções do Intune](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Obter atribuições de funções](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Área de trabalho gerenciada | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Obter atribuições de funções](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Passos Seguintes

* [Como atribuir ou remover funções de administrador do Azure AD](directory-manage-roles-portal.md)
* [Referenciam de funções de administrador do Azure AD](directory-assign-admin-roles.md)
