---
title: Atribuir funções de recurso Azure na Gestão privilegiada de Identidade - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: Saiba como atribuir funções de recurso Azure na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79266562"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Atribuir funções de recurso Azure na Gestão de Identidade Privilegiada

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) pode gerir as funções de recurso Azure incorporado, bem como funções personalizadas, incluindo (mas não se limitando a):

- Proprietário
- Administrador de Acesso de Utilizador
- Contribuinte
- Administrador de Segurança
- Gestor de Segurança

> [!NOTE]
> Os utilizadores ou membros de um grupo atribuído às funções de subscrição do Proprietário ou do Administrador de Acesso ao Utilizador, e os administradores da Azure AD Global que permitem a gestão de subscrições em Azure AD têm permissões de administrador de recursos por padrão. Estes administradores podem atribuir funções, configurar definições de funções e rever o acesso utilizando a Gestão de Identidade Privilegiada para os recursos Do Azure. Um utilizador não pode gerir a Gestão de Identidade Privilegiada para Recursos sem permissões de administrador de Recursos. Veja a lista de [papéis incorporados para os recursos azure.](../../role-based-access-control/built-in-roles.md)

## <a name="assign-a-role"></a>Atribuir um papel

Siga estes passos para tornar um utilizador elegível para uma função de recurso Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que seja membro do papel de [administrador privilegiado.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Para obter informações sobre como conceder a outro administrador acesso para gerir a Gestão de Identidade Privilegiada, consulte o [acesso de Grant a outros administradores para gerir a Gestão de Identidade Privilegiada.](pim-how-to-give-access-to-pim.md)

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **recursos Azure**.

1. Utilize o **filtro Derecurso** para filtrar a lista de recursos geridos.

    ![Lista de recursos azure para gerir](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Selecione o recurso que pretende gerir, como uma subscrição ou grupo de gestão.

1. Em Manage, selecione **Roles** para ver a lista de funções para os recursos Azure.

    ![Funções de recursos azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. **Selecione Adicionar membro** para abrir o novo painel de atribuição.

1. **Selecione Selecione uma função** para abrir o painel de funções Select.

    ![Novo painel de atribuição](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Selecione uma função que pretende atribuir e, em seguida, clique em **Selecionar**.

    Abre-se um painel de membro ou grupo.

1. Selecione um membro ou grupo que deseja atribuir à função e, em seguida, clique em **Selecionar**.

    ![Selecione um painel de membro ou grupo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    O painel de definições de Membro abre.

1. Na lista do **tipo de Atribuição,** selecione **Elegível** ou **Ativo**.

    ![Painel de definições de membros](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    A Gestão privilegiada de Identidade dos recursos Azure fornece dois tipos distintos de atribuição:

    - As atribuições **elegíveis** exigem que o membro do papel execute uma ação para utilizar o papel. As ações podem incluir a realização de um controlo de autenticação multifactor (MFA), o fornecimento de uma justificação de negócio, ou o pedido de aprovação dos aprovadores designados.

    - As atribuições **ativas** não requerem que o membro execute qualquer ação para usar o papel. Os membros designados como ativos têm sempre os privilégios atribuídos ao papel.

1. Se a atribuição for permanente (permanentemente elegível ou permanentemente atribuída), selecione a caixa de verificação **Permanente.**

    Dependendo das definições de função, a caixa de verificação pode não aparecer ou pode ser inmodificável.

1. Para especificar uma duração específica da atribuição, limpe a caixa de verificação e modifique as caixas de data e hora de início e/ou fim.

    ![Definições de membros - data e hora](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Quando terminar, selecione **Done**.

    ![Nova atribuição - Adicionar](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Para criar a nova atribuição de funções, selecione **Adicionar**. É apresentada uma notificação do estado.

    ![Nova atribuição - Notificação](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de funções existente

Siga estes passos para atualizar ou remover uma atribuição de funções existente.

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **recursos Azure**.

1. Selecione o recurso que pretende gerir, como uma subscrição ou grupo de gestão.

1. Em Manage, selecione **Roles** para ver a lista de funções para os recursos Azure.

    ![Funções de recurso Azure - Função selecione](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Selecione a função que pretende atualizar ou remover.

1. Encontre a atribuição de funções nas **funções elegíveis** ou nos separadores de **funções ativas.**

    ![Atualizar ou remover a atribuição de funções](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Selecione **Atualizar** ou **Remover** para atualizar ou remover a atribuição de funções.

    Para obter informações sobre o alargamento de uma atribuição de funções, consulte Extend ou renovar funções de [recurso Azure na Gestão de Identidade Privilegiada.](pim-resource-roles-renew-extend.md)

## <a name="next-steps"></a>Passos seguintes

- [Alargar ou renovar funções de recurso Azure na Gestão de Identidade Privilegiada](pim-resource-roles-renew-extend.md)
- [Configure definições de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
