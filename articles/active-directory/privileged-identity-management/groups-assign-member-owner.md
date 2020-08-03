---
title: Atribuir proprietários e membros elegíveis para grupos de acesso privilegiados - Azure Ative Directory
description: Saiba como atribuir proprietários elegíveis ou membros de um grupo atribuível a funções na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1dcc98366e37455f462fe1a0740aa161201912f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506228"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Atribuir elegibilidade para um grupo privilegiado de acesso (pré-visualização) em Gestão de Identidade Privilegiada

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) pode ajudá-lo a gerir a elegibilidade e ativação de atribuições a grupos de acesso privilegiados em Azure AD. Pode atribuir elegibilidade aos membros ou proprietários do grupo.

>[!NOTE]
>Todos os utilizadores elegíveis para adesão ou propriedade de um grupo de acesso privilegiado devem ter uma licença Azure AD Premium P2. Para mais informações, consulte [os requisitos da Licença para utilizar a Gestão de Identidade Privilegiada.](subscription-requirements.md)

## <a name="assign-an-owner-or-member-of-a-group"></a>Atribuir um proprietário ou membro de um grupo

Siga estes passos para tornar um utilizador elegível para ser membro ou proprietário de um grupo de acesso privilegiado.

1. Inscreva-se na [Gestão de Identidade Privilegiada](https://portal.azure.com/) no portal Azure com permissões de função de administrador de [funções privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Para obter informações sobre como conceder a outro administrador acesso à gestão da Gestão de Identidade Privilegiada, consulte [o Acesso de Concessão a outros administradores para gerir a Gestão de Identidade Privilegiada.](pim-how-to-give-access-to-pim.md)

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **acesso privilegiado (Pré-visualização)**.

1. Pode procurar um nome de grupo e utilizar o **tipo de Grupo** para filtrar a lista para selecionar o grupo que pretende gerir.

    ![Lista de grupos privilegiados de acesso para gerir](./media/groups-assign-member-owner/privileged-access-list.png)

1. Em **Gestão**, selecione **Atribuições**.

1. Selecione **Adicionar atribuições**.

    ![Novo painel de atribuição](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Selecione os membros ou proprietários que pretende tornar elegível para o grupo de acesso privilegiado.

    ![Selecione um membro ou painel de grupo](./media/groups-assign-member-owner/add-assignments.png)

1. Selecione **Seguinte** para definir a duração da adesão ou da propriedade.

    ![Selecione um membro ou painel de grupo](./media/groups-assign-member-owner/assignment-duration.png)

1. Na lista de **tipos de atribuição,** selecione **Elegível** ou **Ativo**. Os grupos de acesso privilegiados fornecem dois tipos distintos de atribuição:

    - As atribuições **elegíveis** requerem que o membro do papel execute uma ação para usar o papel. As ações podem incluir a realização de uma verificação de autenticação multi-factor (MFA), a justificação do negócio ou a solicitação de aprovação dos aprovadores designados.

    - As atribuições **ativas** não requerem que o membro execute qualquer ação para usar o papel. Os membros designados como ativos têm sempre os privilégios atribuídos ao papel.

1. Se a atribuição deve ser permanente (permanentemente elegível ou permanentemente atribuída), selecione a caixa de verificação **Permanente.** Dependendo das definições da sua organização, a caixa de verificação pode não aparecer ou não ser editável.

1. Quando terminar, **selecione Atribuir**.

1. Para criar a nova atribuição de funções, **selecione Add**. É apresentada uma notificação do estado.

    ![Nova atribuição - Notificação](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estes passos para atualizar ou remover uma atribuição de função existente.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **acesso privilegiado (Pré-visualização)**.

1. Pode procurar um nome de grupo e utilizar o **tipo de Grupo** para filtrar a lista para selecionar o grupo que pretende gerir.

    ![Lista de grupos privilegiados de acesso para gerir](./media/groups-assign-member-owner/privileged-access-list.png)

1. Em **Gestão**, selecione **Atribuições**.

1. Selecione a função que pretende atualizar ou remover.

1. Encontre a atribuição de funções nos **separadores de funções elegíveis** ou **de funções ativas.**

    ![Atualizar ou remover a atribuição de funções](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Selecione **Update** ou **Remove** para atualizar ou remover a atribuição de funções.

    Para obter informações sobre o alargamento de uma atribuição de funções, consulte [alargar ou renovar funções de recurso Azure na Gestão de Identidade Privilegiada.](pim-resource-roles-renew-extend.md)

## <a name="next-steps"></a>Passos seguintes

- [Alargar ou renovar funções de recursos do Azure na Gestão de Identidade Privilegiada](pim-resource-roles-renew-extend.md)
- [Configurar configurações de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções de Azure AD em Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
