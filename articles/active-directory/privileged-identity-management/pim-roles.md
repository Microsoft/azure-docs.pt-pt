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
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80607528"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Funções que não pode gerir na Gestão de Identidade Privilegiada

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) permite-lhe gerir todas as [funções da Azure AD](../users-groups-roles/directory-assign-admin-roles.md) e todas as [funções Azure.](../../role-based-access-control/built-in-roles.md) As funções Azure também podem incluir as suas funções personalizadas anexadas aos seus grupos de gestão, subscrições, grupos de recursos e recursos. No entanto, há poucos papéis que não consegues gerir. Este artigo descreve os papéis que não consegue gerir na Gestão de Identidade Privilegiada.

## <a name="classic-subscription-administrator-roles"></a>Funções de administrador de subscrição clássica

Não é possível gerir as seguintes funções clássicas de administrador de subscrição na Gestão de Identidade Privilegiada:

- Administrador de Conta
- Administrador de Serviços
- Coadministrador

Para obter mais informações sobre as funções clássicas de administrador de subscrição, consulte funções de administrador de [subscrição Classic, funções de Azure RBAC e funções](../../role-based-access-control/rbac-and-directory-admin-roles.md)de administrador da AD Azure.

## <a name="what-about-office-365-admin-roles"></a>E os papéis de administrador do Office 365?

Apoiamos todas as funções do Office365 na experiência do portal DeFunções e Administradores do Azure, como o Administrador de Intercâmbio e administrador de SharePoint, mas não apoiamos funções específicas dentro do Exchange RBAC ou do SharePoint RBAC. Para obter mais informações sobre estes serviços do Office 365, consulte as [funções de administrador do Office 365.](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)

> [!NOTE]
> Os utilizadores elegíveis para o papel de Administrador SharePoint, bem como quaisquer funções que tentem aceder ao Microsoft Security and Compliance Center podem sofrer atrasos de até algumas horas após a ativação do seu papel. Estamos a trabalhar com essas equipas para resolver os problemas.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Atribuir funções de recurso Azure na Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
