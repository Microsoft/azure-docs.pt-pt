---
title: Atribuir funções de recursos da Azure em Gestão de Identidade Privilegiada - Diretório Ativo Azure Microsoft Docs
description: Saiba como atribuir funções de recursos Azure na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: dceb7dc6ce6f2b97bbaad0b2db129611833eb30f
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84743835"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Atribuir funções de recursos da Azure na Gestão de Identidade Privilegiada

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) pode gerir as funções de recursos Azure incorporados, bem como funções personalizadas, incluindo (mas não se limitando a):

- Proprietário
- Administrador de Acesso de Utilizador
- Contribuinte
- Administrador de Segurança
- Gestor de Segurança

> [!NOTE]
> Os utilizadores ou membros de um grupo atribuído às funções de subscrição do Administrador de Acesso ao Utilizador ou ao Utilizador, e os administradores AD AD Global que permitem a gestão da subscrição em Azure AD têm permissões de administrador de recursos por padrão. Estes administradores podem atribuir funções, configurar definições de funções e rever o acesso usando a Gestão de Identidade Privilegiada para recursos Azure. Um utilizador não pode gerir a Gestão de Identidade Privilegiada para Recursos sem permissões de administrador de recursos. Veja a lista de [funções incorporadas para recursos Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Atribuir um papel

Siga estes passos para tornar um utilizador elegível para um papel de recurso Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que é membro da função de administrador de [função Privileged.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Para obter informações sobre como conceder a outro administrador acesso à gestão da Gestão de Identidade Privilegiada, consulte [o Acesso de Concessão a outros administradores para gerir a Gestão de Identidade Privilegiada.](pim-how-to-give-access-to-pim.md)

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **recursos Azure**.

1. Utilize o **filtro De recurso** para filtrar a lista de recursos geridos.

    ![Lista de recursos da Azure para gerir](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Selecione o recurso que pretende gerir, como um grupo de subscrição ou gestão.

1. Em Gestão, selecione **Roles** para ver a lista de papéis para recursos Azure.

    ![Funções de recursos Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. **Selecione Adicionar membro** para abrir o painel de atribuição Nova.

1. **Selecione selecionar uma função** para abrir o painel de funções Selecione.

    ![Novo painel de atribuição](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Selecione uma função que pretende atribuir e, em seguida, clique em **Selecionar**.

    O painel Select um membro ou painel de grupo abre.

1. Selecione um membro ou grupo que pretende atribuir à função e, em seguida, clique em **Select**.

    ![Selecione um membro ou painel de grupo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    O painel de definições de membro abre.-

1. Na lista de **tipos de atribuição,** selecione **Elegível** ou **Ativo**.

    ![Painel de configurações de membros](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    A Gestão privilegiada de Identidade para recursos Azure fornece dois tipos distintos de atribuição:

    - As atribuições **elegíveis** requerem que o membro do papel execute uma ação para usar o papel. As ações podem incluir a realização de uma verificação de autenticação multi-factor (MFA), a justificação do negócio ou a solicitação de aprovação dos aprovadores designados.

    - As atribuições **ativas** não requerem que o membro execute qualquer ação para usar o papel. Os membros designados como ativos têm sempre os privilégios atribuídos ao papel.

1. Se a atribuição deve ser permanente (permanentemente elegível ou permanentemente atribuída), selecione a caixa de verificação **Permanente.**

    Dependendo das definições de função, a caixa de verificação pode não aparecer ou pode ser indificável.

1. Para especificar uma duração específica da atribuição, limpe a caixa de verificação e modifique as caixas de início e/ou de fim e hora.

    ![Definições de membros - data e hora](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Quando terminar, selecione **'Fazer' ( 'Fazer' ( "Fazer")**

    ![Nova atribuição - Adicionar](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Para criar a nova atribuição de funções, **selecione Add**. É apresentada uma notificação do estado.

    ![Nova atribuição - Notificação](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estes passos para atualizar ou remover uma atribuição de função existente.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **recursos Azure**.

1. Selecione o recurso que pretende gerir, como um grupo de subscrição ou gestão.

1. Em Gestão, selecione **Roles** para ver a lista de papéis para recursos Azure.

    ![Funções de recursos Azure - Selecione função](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Selecione a função que pretende atualizar ou remover.

1. Encontre a atribuição de funções nos **separadores de funções elegíveis** ou **de funções ativas.**

    ![Atualizar ou remover a atribuição de funções](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Selecione **Update** ou **Remove** para atualizar ou remover a atribuição de funções.

    Para obter informações sobre o alargamento de uma atribuição de funções, consulte [alargar ou renovar funções de recurso Azure na Gestão de Identidade Privilegiada.](pim-resource-roles-renew-extend.md)

## <a name="next-steps"></a>Passos seguintes

- [Alargar ou renovar funções de recursos do Azure na Gestão de Identidade Privilegiada](pim-resource-roles-renew-extend.md)
- [Configurar configurações de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções de Azure AD em Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
