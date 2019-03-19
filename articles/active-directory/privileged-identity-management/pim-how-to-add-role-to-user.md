---
title: Atribuir funções de administrador do Azure AD - Privileged Identity Management | Documentos da Microsoft
description: Saiba como atribuir funções de administrador do Azure Active Directory no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/30/2018
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3c8a7fb7a94dbca7b0e63ddaf756a536fbd0600
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000625"
---
# <a name="assign-azure-ad-administrator-roles-in-pim"></a>Atribuir funções de administrador do Azure AD no PIM

Com o Azure Active Directory (Azure AD), pode tornar um Administrador Global **permanente** atribuições de funções de administrador do Azure AD. Estas atribuições de função podem ser criadas com o [portal do Azure](../users-groups-roles/directory-assign-admin-roles.md) ou a utilizar [comandos do PowerShell](/powershell/module/azuread#directory_roles).

O serviço do Azure AD Privileged Identity Management (PIM) também permite que os administradores de função com privilégios tornar administrador permanente atribuições de funções. Além disso, os administradores de função com privilégios podem tornar os usuários **elegíveis** para funções de administrador do Azure AD. Um administrador elegível pode ativar a função quando surge a necessidade e, em seguida, as respetivas permissões expirarem assim que estiverem concluídos.

## <a name="make-a-user-eligible-for-a-role"></a>Tornar um utilizador elegível para uma função

Siga estes passos para tornar um utilizador elegível para uma função de administrador do Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com um utilizador que seja um membro a [administrador com função privilegiada](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) função.

    Para obter informações sobre como conceder outro acesso de administrador para gerir o PIM, consulte [conceder acesso a outros administradores para gerir o PIM](pim-how-to-give-access-to-pim.md).

1. Open **do Azure AD Privileged Identity Management**.

    Se ainda não começou PIM no portal do Azure, aceda a [começar a utilizar o PIM](pim-getting-started.md).

1. Clique em **funções de diretório do Azure AD**.

1. Clique em **funções** ou **membros**.

    ![Funções de diretório do Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Clique em **Add member** abrir Adicionar geridos membros.

1. Clique em **selecionar uma função**, clique numa função que pretende gerir e, em seguida, clique em **selecione**.

    ![Selecionar uma função](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Clique em **selecionar membros**, selecione os utilizadores que pretende atribuir à função e, em seguida, clique em **selecione**.

    ![Selecionar uma função](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Em Adicionar membros geridos, clique em **OK** para adicionar o utilizador à função.

1. Na lista de funções, clique na função atribuída apenas para ver a lista de membros.

     Quando a função é atribuída, o utilizador selecionou irá aparecer na lista de membros como **elegíveis** para a função.

    ![Utilizador elegível para uma função](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Agora que o utilizador é elegível para a função, informá-los de que eles podem fazê-lo, de acordo com as instruções em [ativar as minhas funções de diretório do Azure AD no PIM](pim-how-to-activate-role.md).

    Os administradores elegíveis são-lhe pedidos para se registrar para o Azure multi-factor Authentication (MFA) durante a ativação. Se um utilizador não é possível registar para MFA ou está a utilizar uma conta Microsoft (normalmente @outlook.com), precisa para torná-los permanente em todas as suas funções.

## <a name="make-a-role-assignment-permanent"></a>Tornar uma atribuição de função permanente

Por predefinição, os novos utilizadores só são elegíveis para uma função de administrador do Azure AD. Siga estes passos, se pretender disponibilizar uma atribuição de função permanente.

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **funções de diretório do Azure AD**.

1. Clique em **membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Clique num **elegíveis** função que pretende tornar permanente.

1. Clique em **mais** e, em seguida, clique em **permanente de tornar**.

    ![Tornar permanente a atribuição de função](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    A função é agora listada como **permanente**.

    ![Lista de membros com alteração permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Remover um utilizador de uma função

Pode remover os utilizadores de atribuições de funções, mas certifique-se sempre existe pelo menos um utilizador que seja um Administrador Global permanente. Se não tem a certeza de que os utilizadores ainda precisam de suas atribuições de funções, pode [iniciar uma revisão de acesso para a função](pim-how-to-start-security-review.md).

Siga estes passos para remover um utilizador específico de uma função de administrador do Azure AD.

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **funções de diretório do Azure AD**.

1. Clique em **membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Clique numa atribuição de função que pretende remover.

1. Clique em **mais** e, em seguida, clique em **remover**.

    ![Remover uma função](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Na mensagem que lhe pede para confirmar, clique em **Sim**.

    ![Remover uma função](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A atribuição de função é removida.

## <a name="authorization-error-when-assigning-roles"></a>Erro de autorização ao atribuir funções

Se ativou recentemente o PIM para obter uma subscrição e obtém um erro de autorização quando tentar fazer com que um utilizador elegível para uma função de administrador do Azure AD, poderá ser porque o principal de serviço MS-PIM ainda não tem as permissões adequadas. O principal de serviço MS-PIM tem de ter o [administrador de acesso de utilizador](../../role-based-access-control/built-in-roles.md#user-access-administrator) função para atribuir funções a outras pessoas. Em vez de aguardar até o MS PIM é atribuída a função de administrador de acesso de utilizador, pode atribuí-la manualmente.

Siga estes passos para atribuir a função de administrador de acesso de utilizador para o MS-PIM principal de serviço para uma subscrição.

1. Inicie sessão no portal do Azure como Administrador Global.

1. Escolher **todos os serviços** e, em seguida **subscrições**.

1. Escolha a sua subscrição.

1. Escolha **Controlo de acesso (IAM)**.

1. Escolher **atribuições de funções** para ver a lista atual de atribuições de funções no âmbito da subscrição.

   ![Painel de controlo (IAM) de acesso para uma subscrição](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Verifique se o **MS-PIM** principal de serviço é atribuído a **administrador de acesso de utilizador** função.

1. Se não, escolha **adicionar atribuição de função** para abrir o **adicionar atribuição de função** painel.

1. Na **função** na lista pendente, selecione a **administrador de acesso de utilizador** função.

1. Na **selecionar** lista, localize e selecione o **MS-PIM** principal de serviço.

   ![Adicionar permissões para MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Escolher **guardar** para atribuir a função.

   Após alguns instantes, o principal de serviço MS-PIM é atribuída a função de administrador de acesso de utilizador no âmbito da subscrição.

   ![Função de administrador de acesso de utilizador para MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>Passos Seguintes

- [Configurar definições de função de administrador do Azure AD no PIM](pim-how-to-change-default-settings.md)
- [Atribuir funções de recursos do Azure no PIM](pim-resource-roles-assign-roles.md)
