---
title: Atribuir papel personalizado Azure AD - Gestão de Identidade Privilegiada (PIM)
description: Como atribuir um papel personalizado Azure AD para a atribuição de Gestão de Identidade Privilegiada (PIM)
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
ms.openlocfilehash: 75e004980ada23a616b57e3c7eb1afb241e09598
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371652"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Atribuir um papel personalizado a AZure AD na Gestão de Identidade Privilegiada

Este artigo diz-lhe como utilizar a Gestão de Identidade Privilegiada (PIM) para criar uma atribuição just-in-time e tempo-bound a funções personalizadas criadas para gerir aplicações na experiência administrativa Azure Ative (Azure AD).

- Para obter mais informações sobre a criação de funções personalizadas para delegar a gestão de aplicações em Azure AD, consulte [as funções de administrador personalizado no Azure Ative Directory (pré-visualização)](../roles/custom-overview.md).
- Se ainda não utilizou a Gestão de Identidade Privilegiada, obtenha mais informações no [Início da Utilização de Gestão de Identidade Privilegiada.](pim-getting-started.md)
- Para obter informações sobre como conceder a outro administrador acesso à gestão da Gestão de Identidade Privilegiada, consulte [o Acesso de Concessão a outros administradores para gerir a Gestão de Identidade Privilegiada.](pim-how-to-give-access-to-pim.md)

> [!NOTE]
> As funções personalizadas AZURE AD não são integradas com as funções de diretório incorporado durante a pré-visualização. Uma vez que a capacidade esteja geralmente disponível, a gestão de funções terá lugar na experiência de funções incorporadas. Se vir o seguinte banner, estas funções devem ser geridas [na experiência de funções incorporadas](pim-how-to-activate-role.md) e este artigo não se aplica:
>
> [![Selecione Azure AD > Gestão de Identidade Privilegiada.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Atribuir um papel

A Gestão de Identidade Privilegiada pode gerir funções personalizadas que pode criar na gestão de aplicações Azure Ative (Azure AD).  Os passos seguintes fazem uma atribuição elegível para um papel de diretório personalizado.

1. Inscreva-se na [Gestão de Identidade Privilegiada](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) no portal Azure com uma conta de utilizador que é atribuída à função de administrador de função Privilegiada.
1. Selecione **funções personalizadas Azure AD (Preview)**.

    ![Selecione Azure AD funções personalizadas pré-visualização para ver atribuições de funções elegíveis](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selecione **Roles** para ver uma lista de funções personalizadas para aplicações AD Azure.

    ![Selecione Funções ver a lista de atribuições de funções elegíveis](./media/azure-ad-custom-roles-assign/view-roles.png)

1. **Selecione Adicionar membro** para abrir a página de atribuição.
1. Para restringir o âmbito da atribuição de funções a uma única aplicação, selecione **Scope** para especificar um âmbito de aplicação.

    ![restringir o âmbito das atribuições de funções elegíveis no Azure AD](./media/azure-ad-custom-roles-assign/set-scope.png)

1. **Selecione selecionar uma função** para abrir a lista **de funções.**

    ![selecionar a função elegível para atribuir a um utilizador](./media/azure-ad-custom-roles-assign/select-role.png)

1. Selecione uma função que pretende atribuir e, em seguida, clique em **Selecionar**. A lista **de membros Select abre.**

    ![selecione o utilizador a quem está a atribuir a função](./media/azure-ad-custom-roles-assign/select-member.png)

1. Selecione um utilizador que pretende atribuir à função e, em seguida, clique em **Select**. A lista **de definições de membros** abre.

    ![Desagrafe o tipo de atribuição de funções para elegível ou ativo](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. Na página **de definições de adesão,** selecione **Elegível** ou **ativo**:

    - As atribuições **elegíveis** requerem que o utilizador designado para o papel execute uma ação antes de poder utilizar a função. As ações podem incluir a aprovação de uma verificação de autenticação multi-factor, a justificação do negócio ou a solicitação de aprovação dos aprovadores designados.
    - As atribuições **ativas** não requerem que o utilizador designado execute qualquer ação para utilizar o papel. Os utilizadores ativos têm sempre os privilégios atribuídos ao papel.

1. Se a caixa de verificação **Permanente** estiver presente e disponível (dependendo das definições de funções), pode especificar se a atribuição é permanente. Selecione a caixa de verificação para tornar a atribuição permanentemente elegível ou permanentemente atribuída. Limpe a caixa de verificação para especificar uma duração de atribuição.
1. Para criar a nova atribuição de funções, clique em **Guardar** e, em seguida, **Adicione**. É apresentada uma notificação do estado do processo de atribuição.

Para verificar a atribuição de funções, numa função aberta, selecione **Atribuições**  >  **Atribuir** e verificar se a sua atribuição de funções está devidamente identificada como elegível ou ativa.

 ![Verifique se a atribuição de funções é visível como elegível ou ativa](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Passos seguintes

- [Ativar um papel personalizado AZure AD](azure-ad-custom-roles-assign.md)
- [Remova ou atualize uma atribuição de função personalizada Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configure uma atribuição de função personalizada Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função em Azure AD](../roles/permissions-reference.md)
