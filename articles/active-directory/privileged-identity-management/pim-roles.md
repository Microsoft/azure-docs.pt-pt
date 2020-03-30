---
title: Funções que não pode gerir na Gestão de Identidade Privilegiada - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: Descreve as funções que não pode gerir na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72895207"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Funções que não pode gerir na Gestão de Identidade Privilegiada

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) permite-lhe gerir todas as [funções de AD Azure](../users-groups-roles/directory-assign-admin-roles.md) e todas as funções de [recursos Azure.](../../role-based-access-control/built-in-roles.md) Estas funções também incluem as suas funções personalizadas anexadas aos seus grupos de gestão, subscrições, grupos de recursos e recursos. No entanto, há poucos papéis que não consegues gerir. Este artigo descreve as funções que não pode gerir na Gestão de Identidade Privilegiada.

## <a name="classic-subscription-administrator-roles"></a>Funções de administrador de subscrição clássica

Não é possível gerir as seguintes funções clássicas de administrador de subscrição na Gestão de Identidade Privilegiada:

- Administrador de Conta
- Administrador de Serviços
- Coadministrador

Para obter mais informações sobre as funções clássicas de administrador de subscrição, consulte funções de administrador de [subscrição Classic, funções de Azure RBAC e funções](../../role-based-access-control/rbac-and-directory-admin-roles.md)de administrador da AD Azure.

## <a name="what-about-office-365-admin-roles"></a>E os papéis de administrador do Office 365?

As funções no Exchange Online ou SharePoint Online, com exceção do Administrador de Câmbio e administrador do SharePoint, não estão representadas no Azure AD e por isso não podem ser geridas na Gestão de Identidade Privilegiada. Para obter mais informações sobre estes serviços do Office 365, consulte as [funções de administrador do Office 365.](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)

> [!NOTE]
> O Administrador SharePoint tem acesso administrativo ao SharePoint Online através do centro de administração SharePoint Online, e pode executar quase qualquer tarefa no SharePoint Online. Os utilizadores elegíveis podem experimentar atrasos na utilização desta função no SharePoint após a ativação na Gestão de Identidade Privilegiada.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Atribuir funções de recurso Azure na Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
