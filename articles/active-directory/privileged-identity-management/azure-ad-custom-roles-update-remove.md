---
title: Atualizar ou remover a função personalizada da Azure AD - Gestão de Identidade Privilegiada (PIM)
description: Como atualizar ou remover uma atribuição de funções personalizadas da AD Azure (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad5747be47e250cf9c623cc40d21d12c91ee16f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77499109"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Atualizar ou remover uma função personalizada da AD Azure atribuída na Gestão de Identidade Privilegiada

Este artigo diz-lhe como usar a Gestão de Identidade Privilegiada (PIM) para atualizar ou remover a atribuição just-in-time e time-bound para funções personalizadas criadas para gestão de aplicações na experiência administrativa do Azure Ative Directory (Azure AD). 

- Para obter mais informações sobre a criação de papéis personalizados para delegar a gestão de aplicações em Azure AD, consulte as funções de [administrador personalizado no Azure Ative Directory (pré-visualização)](../users-groups-roles/roles-custom-overview.md). 
- Se ainda não utilizou a Gestão de Identidade Privilegiada, obtenha mais informações no [Start use A Gestão](pim-getting-started.md)de Identidade Privilegiada.

> [!NOTE]
> As funções personalizadas da Azure AD não estão integradas com as funções de diretório incorporada durante a pré-visualização. Uma vez que a capacidade esteja geralmente disponível, a gestão de papéis terá lugar na experiência de papéis incorporados. Se vir o seguinte banner, estas funções devem ser geridas na experiência de [papéis incorporados](pim-how-to-add-role-to-user.md) e este artigo não se aplica:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Atualizar ou remover uma atribuição

Siga estes passos para atualizar ou remover uma atribuição de funções personalizada existente.

1. Inscreva-se na [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) no portal Azure com uma conta de utilizador que é atribuída ao papel de administrador de funções privilegiada.
1. Selecione **funções personalizadas Azure AD (Pré-visualização)**.

    ![Selecione visualização de funções personalizadas da Azure AD para ver atribuições de papéis elegíveis](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selecione **Funções** para ver a lista de tarefas de funções **personalizadas** para aplicações AD Azure.

    ![Selecione Roles ver a lista de atribuições de funções elegíveis](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Selecione a função que pretende atualizar ou remover.
1. Encontre a atribuição de funções nas **funções elegíveis** ou nos separadores de **funções ativas.**
1. Selecione **Atualizar** ou **Remover** para atualizar ou remover a atribuição de funções.

    ![Selecione remover ou atualizar na atribuição de funções elegíveis](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Passos seguintes

- [Ativar uma função personalizada da AD Azure](azure-ad-custom-roles-assign.md)
- [Atribuir uma função personalizada azure AD](azure-ad-custom-roles-assign.md)
- [Configure uma atribuição de funções personalizadas da Azure AD](azure-ad-custom-roles-configure.md)
