---
title: Funções que você não pode gerenciar em Privileged Identity Management Azure Active Directory | Microsoft Docs
description: Descreve as funções que você não pode gerenciar no Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895207"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Funções que você não pode gerenciar no Privileged Identity Management

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) permite que você gerencie todas as [funções do Azure ad](../users-groups-roles/directory-assign-admin-roles.md) e todas as [funções de recursos do Azure](../../role-based-access-control/built-in-roles.md). Essas funções também incluem suas funções personalizadas anexadas aos grupos de gerenciamento, assinaturas, grupos de recursos e recursos. No entanto, há poucas funções que você não pode gerenciar. Este artigo descreve as funções que você não pode gerenciar no Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Funções de administrador de subscrição clássica

Você não pode gerenciar as seguintes funções de administrador de assinatura clássica no Privileged Identity Management:

- Administrador de Conta
- Administrador de Serviços
- Coadministrador

Para obter mais informações sobre as funções de administrador de assinatura clássica, consulte [funções de administrador de assinatura clássica, funções de RBAC do Azure e funções de administrador do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>E quanto às funções de administrador do Office 365?

As funções no Exchange Online ou no SharePoint Online, exceto para o administrador do Exchange e o administrador do SharePoint, não são representadas no Azure AD e, portanto, não podem ser gerenciadas no Privileged Identity Management. Para obter mais informações sobre esses serviços do Office 365, consulte [funções de administrador do office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> O administrador do SharePoint tem acesso administrativo ao SharePoint Online por meio do centro de administração do SharePoint Online e pode executar quase qualquer tarefa no SharePoint Online. Os usuários qualificados podem experimentar atrasos usando essa função no SharePoint após a ativação no Privileged Identity Management.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Atribuir funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-assign-roles.md)
