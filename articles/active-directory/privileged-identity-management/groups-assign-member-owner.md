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
ms.date: 08/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48c4473cfafce1215219251c47bce1d5730645fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91534425"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Atribuir elegibilidade para um grupo privilegiado de acesso (pré-visualização) em Gestão de Identidade Privilegiada

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) pode ajudá-lo a gerir a elegibilidade e ativação de atribuições a grupos de acesso privilegiados em Azure AD. Pode atribuir elegibilidade aos membros ou proprietários do grupo.

>[!NOTE]
>Todos os utilizadores elegíveis para adesão ou propriedade de um grupo de acesso privilegiado devem ter uma licença Azure AD Premium P2. Para mais informações, consulte [os requisitos da Licença para utilizar a Gestão de Identidade Privilegiada.](subscription-requirements.md)

## <a name="assign-an-owner-or-member-of-a-group"></a>Atribuir um proprietário ou membro de um grupo

Siga estes passos para tornar um utilizador elegível para ser membro ou proprietário de um grupo de acesso privilegiado.

1. [Inscreva-se no Azure AD](https://aad.portal.azure.com) com permissões do Administrador Global ou do Grupo Proprietário.
1. Selecione **Grupos** e, em seguida, selecione o grupo atribuível a funções que pretende gerir. Pode pesquisar ou filtrar a lista.

    ![encontrar um grupo atribuível a funções para gerir em PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Abra o grupo e selecione **acesso privilegiado (Pré-visualização)**.

    ![Abra a experiência de Gestão de Identidade Privilegiada](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Selecione **Adicionar atribuições**.

    ![Novo painel de atribuição](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Selecione os membros ou proprietários que pretende tornar elegível para o grupo de acesso privilegiado.

    ![Screenshot que mostra a página "Adicionar atribuições" com o painel "Selecione um membro ou grupo" aberto e o botão "Selecione" realçado.](./media/groups-assign-member-owner/add-assignments.png)

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

1. [Inscreva-se no Azure AD](https://aad.portal.azure.com) com permissões do Administrador Global ou do Grupo Proprietário.
1. Selecione **Grupos** e, em seguida, selecione o grupo atribuível a funções que pretende gerir. Pode pesquisar ou filtrar a lista.

    ![encontrar um grupo atribuível a funções para gerir em PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Abra o grupo e selecione **acesso privilegiado (Pré-visualização)**.

    ![Abra a experiência de Gestão de Identidade Privilegiada](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Selecione a função que pretende atualizar ou remover.

1. Encontre a atribuição de funções nos **separadores de funções elegíveis** ou **de funções ativas.**

    ![Atualizar ou remover a atribuição de funções](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. Selecione **Update** ou **Remove** para atualizar ou remover a atribuição de funções.

    Para obter informações sobre o alargamento de uma atribuição de funções, consulte [alargar ou renovar funções de recurso Azure na Gestão de Identidade Privilegiada.](pim-resource-roles-renew-extend.md)

## <a name="next-steps"></a>Passos seguintes

- [Alargar ou renovar funções de recursos do Azure na Gestão de Identidade Privilegiada](pim-resource-roles-renew-extend.md)
- [Configurar configurações de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções de Azure AD em Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
