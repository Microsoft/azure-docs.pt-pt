---
title: Atualizar ou remover o papel personalizado Azure AD - Gestão de Identidade Privilegiada (PIM)
description: Como atualizar ou remover uma tarefa personalizada Azure AD Gestão de Identidade Privilegiada (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a35442dd8af1cd4acf22de453c8d10460e1e39f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371533"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Atualizar ou remover um papel personalizado Azure AD atribuído na Gestão de Identidade Privilegiada

Este artigo diz-lhe como utilizar a Gestão de Identidade Privilegiada (PIM) para atualizar ou remover a atribuição just-in-time e time-bound para funções personalizadas criadas para gestão de aplicações na experiência administrativa Azure Ative (Azure AD). 

- Para obter mais informações sobre a criação de funções personalizadas para delegar a gestão de aplicações em Azure AD, consulte [as funções de administrador personalizado no Azure Ative Directory (pré-visualização)](../roles/custom-overview.md). 
- Se ainda não utilizou a Gestão de Identidade Privilegiada, obtenha mais informações no [Início da Utilização de Gestão de Identidade Privilegiada.](pim-getting-started.md)

> [!NOTE]
> As funções personalizadas AZURE AD não são integradas com as funções de diretório incorporado durante a pré-visualização. Uma vez que a capacidade esteja geralmente disponível, a gestão de funções terá lugar na experiência de funções incorporadas. Se vir o seguinte banner, estas funções devem ser geridas [na experiência de funções incorporadas](pim-how-to-add-role-to-user.md) e este artigo não se aplica:
>
> [![Selecione Azure AD > Gestão de Identidade Privilegiada.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Atualizar ou remover uma atribuição

Siga estes passos para atualizar ou remover uma atribuição de funções personalizadas existente.

1. Inscreva-se na [Gestão de Identidade Privilegiada](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) no portal Azure com uma conta de utilizador que é atribuída à função de administrador de função Privilegiada.
1. Selecione **funções personalizadas Azure AD (Preview)**.

    ![Selecione Azure AD funções personalizadas pré-visualização para ver atribuições de funções elegíveis](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selecione **Funções** para ver a lista de atribuições de funções **personalizadas** para aplicações AD Azure.

    ![Selecione Funções ver a lista de atribuições de funções elegíveis](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Selecione a função que pretende atualizar ou remover.
1. Encontre a atribuição de funções nos **separadores de funções elegíveis** ou **de funções ativas.**
1. Selecione **Update** ou **Remove** para atualizar ou remover a atribuição de funções.

    ![Selecione remover ou atualizar na atribuição de função elegível](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Passos seguintes

- [Ativar um papel personalizado AZure AD](azure-ad-custom-roles-assign.md)
- [Atribuir um papel personalizado AZure AD](azure-ad-custom-roles-assign.md)
- [Configure uma atribuição de função personalizada Azure AD](azure-ad-custom-roles-configure.md)
