---
title: Designação de ad ida ao Papel personalizado da AD - Gestão privilegiada de identidade (PIM)
description: Como atribuir uma função personalizada da AD Azure para a atribuição de Gestão de Identidade Privilegiada (PIM)
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
ms.openlocfilehash: 0303d37ef5bbbf266feb5456b0bc224ce272ee13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499236"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Atribuir um papel personalizado da AD Azure na Gestão de Identidade Privilegiada

Este artigo diz-lhe como usar a Gestão de Identidade Privilegiada (PIM) para criar uma atribuição justa e limitada ao tempo para funções personalizadas criadas para gerir aplicações na experiência administrativa do Azure Ative Directory (Azure AD).

- Para obter mais informações sobre a criação de papéis personalizados para delegar a gestão de aplicações em Azure AD, consulte as funções de [administrador personalizado no Azure Ative Directory (pré-visualização)](../users-groups-roles/roles-custom-overview.md).
- Se ainda não utilizou a Gestão de Identidade Privilegiada, obtenha mais informações no [Start use A Gestão](pim-getting-started.md)de Identidade Privilegiada.
- Para obter informações sobre como conceder a outro administrador acesso para gerir a Gestão de Identidade Privilegiada, consulte o [acesso de Grant a outros administradores para gerir a Gestão de Identidade Privilegiada.](pim-how-to-give-access-to-pim.md)

> [!NOTE]
> As funções personalizadas da Azure AD não estão integradas com as funções de diretório incorporada durante a pré-visualização. Uma vez que a capacidade esteja geralmente disponível, a gestão de papéis terá lugar na experiência de papéis incorporados. Se vir o seguinte banner, estas funções devem ser geridas na experiência de [papéis incorporados](pim-how-to-activate-role.md) e este artigo não se aplica:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Atribuir um papel

A Gestão de Identidade Privilegiada pode gerir funções personalizadas que pode criar na gestão de aplicações do Azure Ative Directory (Azure AD).  Os seguintes passos fazem uma atribuição elegível para um papel de diretório personalizado.

1. Inscreva-se na [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) no portal Azure com uma conta de utilizador que é atribuída ao papel de administrador de funções privilegiada.
1. Selecione **funções personalizadas Azure AD (Pré-visualização)**.

    ![Selecione visualização de funções personalizadas da Azure AD para ver atribuições de papéis elegíveis](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selecione **Funções** para ver uma lista de funções personalizadas para aplicações Azure AD.

    ![Selecione Roles ver a lista de atribuições de funções elegíveis](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Selecione **Adicionar membro** para abrir a página de atribuição.
1. Para restringir o âmbito da atribuição de funções a uma única aplicação, selecione **Scope** para especificar um âmbito de aplicação.

    ![restringir o âmbito das atribuições de funções elegíveis na AD Azure](./media/azure-ad-custom-roles-assign/set-scope.png)

1. **Selecione Selecione uma função** para abrir a lista de **funções.**

    ![selecionar a função elegível para atribuir a um utilizador](./media/azure-ad-custom-roles-assign/select-role.png)

1. Selecione uma função que pretende atribuir e, em seguida, clique em **Selecionar**. Abre-se a lista **de membros Select.**

    ![selecione o utilizador a quem está atribuindo o papel](./media/azure-ad-custom-roles-assign/select-member.png)

1. Selecione um utilizador que deseja atribuir à função e, em seguida, clique em **Selecionar**. A lista de **definições de Membros** abre.

    ![Definir o tipo de atribuição de funções para elegível ou ativo](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. Na página de **definições** de Membro, selecione **Elegível** ou **Ativo:**

    - As atribuições **elegíveis** requerem que o utilizador designado para a função execute uma ação antes de poder utilizar a função. As ações podem incluir a aprovação de um controlo de autenticação de vários fatores, a justificação do negócio ou o pedido de aprovação dos aprovadores designados.
    - As atribuições **ativas** não requerem que o utilizador designado execute qualquer ação para utilizar a função. Os utilizadores ativos têm sempre os privilégios atribuídos ao papel.

1. Se a caixa de verificação **Permanente** estiver presente e disponível (dependendo das definições de funções), pode especificar se a atribuição é permanente. Selecione a caixa de verificação para tornar a atribuição permanentemente elegível ou permanentemente atribuída. Limpe a caixa de verificação para especificar a duração da atribuição.
1. Para criar a nova atribuição de funções, clique em **Guardar** **e,** em seguida, adicionar . É apresentada uma notificação do estado do processo de atribuição.

Para verificar a atribuição de funções, em funções abertas, selecione Atribuição de **Atribuições** > **e** verifique se a sua atribuição de funções está devidamente identificada como elegível ou ativa.

 ![Verifique se a atribuição de funções é visível como elegível ou ativa](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Passos seguintes

- [Ativar uma função personalizada da AD Azure](azure-ad-custom-roles-assign.md)
- [Remova ou atualize uma atribuição de funções personalizadas da AD Azure](azure-ad-custom-roles-update-remove.md)
- [Configure uma atribuição de funções personalizadas da Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de papéis em Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
