---
title: Funções que não pode gerir em Gestão de Identidade Privilegiada - Azure Ative Directory | Microsoft Docs
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
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c72587c5486ed61215fd20c215a1dd194f4b7bc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92372417"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Funções que não pode gerir na Gestão de Identidade Privilegiada

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) permite-lhe gerir todas as [funções de AD Azure](../roles/permissions-reference.md) e todas as [funções de Azure](../../role-based-access-control/built-in-roles.md). As funções Azure também podem incluir as suas funções personalizadas anexadas aos seus grupos de gestão, subscrições, grupos de recursos e recursos. No entanto, há poucos papéis que não consegues gerir. Este artigo descreve os papéis que não pode gerir na Gestão de Identidade Privilegiada.

## <a name="classic-subscription-administrator-roles"></a>Funções de administrador de subscrição clássica

Não é possível gerir as seguintes funções clássicas de administrador de subscrição na Gestão de Identidade Privilegiada:

- Administrador de Conta
- Administrador de Serviços
- Coadministrador

Para obter mais informações sobre as funções clássicas de administrador de subscrição, consulte [as funções de administrador de subscrição clássica, funções de administrador da Azure e administradora AD Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-microsoft-365-admin-roles"></a>E os papéis de administração da Microsoft 365?

Apoiamos todas as funções da Microsoft 365 na experiência do portal AD Roles e Administradores Azure, como O Administrador de Câmbio e Administrador sharePoint, mas não suportamos funções específicas dentro do RBAC de Exchange ou Do SharePoint RBAC. Para obter mais informações sobre estes serviços Microsoft 365, consulte [as funções de administração da Microsoft 365](/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Os utilizadores elegíveis para a função de administrador do SharePoint, a função de administrador do dispositivo e quaisquer funções que tentem aceder ao Microsoft Security and Compliance Center podem sofrer atrasos de até algumas horas após a ativação da sua função. Estamos a trabalhar com essas equipas para resolver os problemas.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de Azure AD em Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Atribuir funções de recursos da Azure na Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)