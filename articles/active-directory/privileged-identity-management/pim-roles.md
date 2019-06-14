---
title: Funções no PIM - Azure Active Directory não é possível gerir | Documentos da Microsoft
description: Descreve as funções que não pode ser gerido no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa5fb632ee5fd9c18bde7443e81fe2ef6e5335e4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60437278"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Funções não é possível gerir no PIM

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) permite-lhe gerir tudo [funções do Azure AD](../users-groups-roles/directory-assign-admin-roles.md) e todos os [funções de recursos do Azure](../../role-based-access-control/built-in-roles.md). Estas funções também incluem as suas funções personalizadas anexadas a seus grupos de gestão, subscrições, grupos de recursos e recursos. No entanto, existem algumas funções que não pode gerir. Este artigo descreve as funções que não é possível gerir PIM.

## <a name="classic-subscription-administrator-roles"></a>Funções de administrador de subscrição clássica

Não é possível gerir as seguintes funções de administrador de subscrição clássica no PIM:

- Administrador de Conta
- Administrador de Serviços
- Coadministrador

Para obter mais informações sobre as funções de administrador de subscrição clássica, consulte [funções de administrador de subscrição clássico, funções RBAC do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>E funções de administração do Office 365?

Funções no Exchange Online ou SharePoint Online, exceto para o administrador do Exchange e o administrador do SharePoint, não são representadas no Azure AD e, portanto, não podem ser gerenciadas no PIM. Para obter mais informações sobre estes serviços do Office 365, consulte [funções de administração do Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Administrador do SharePoint tem acesso administrativo ao SharePoint Online através do Centro de administração do SharePoint Online e podem executar quase qualquer tarefa no SharePoint Online. Os utilizadores elegíveis poderão ocorrem atrasos com esta função dentro do SharePoint após a ativação no PIM.

## <a name="next-steps"></a>Passos Seguintes

- [Atribuir funções do Azure AD no PIM](pim-how-to-add-role-to-user.md)
- [Atribuir funções de recursos do Azure no PIM](pim-resource-roles-assign-roles.md)
