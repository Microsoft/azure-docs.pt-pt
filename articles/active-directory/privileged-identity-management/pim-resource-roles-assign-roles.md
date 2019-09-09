---
title: Atribuir funções de recurso do Azure em PIM-Azure Active Directory | Microsoft Docs
description: Saiba como atribuir funções de recurso do Azure no Azure AD Privileged Identity Management (PIM).
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
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2850f17c96ef031f9e1b8e11558ab369e4175b0
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804279"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Atribuir funções de recurso do Azure no PIM

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pode gerenciar as funções de recurso internas do Azure, bem como as funções personalizadas, incluindo (mas não se limitando a):

- Owner
- Administrador de Acesso dos Utilizadores
- Contribuinte
- Administrador de Segurança
- Gerenciador de segurança e muito mais

> [!NOTE]
> Os usuários ou membros de um grupo atribuído ao proprietário ou às funções de administrador de acesso do usuário e aos administradores globais que habilitam o gerenciamento de assinaturas no Azure AD são administradores de recursos. Esses administradores podem atribuir funções, definir configurações de função e examinar o acesso usando o PIM para recursos do Azure. Ou seja, a conta não terá os direitos de gerenciar o PIM para recursos se o usuário não tiver uma função de administrador de recursos. Exiba a lista de [funções internas para recursos do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Atribuir uma função

Siga estas etapas para tornar um usuário qualificado para uma função de recurso do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Para obter informações sobre como conceder a outro administrador acesso para gerenciar o PIM, consulte [conceder acesso a outros administradores para gerenciar o PIM](pim-how-to-give-access-to-pim.md).

1. Abra **Azure ad Privileged Identity Management**.

    Se você ainda não tiver iniciado o PIM no portal do Azure, vá para [começar a usar o PIM](pim-getting-started.md).

1. Clique em **recursos do Azure**.

1. Use o **filtro de recursos** para filtrar a lista de recursos gerenciados.

    ![Lista de recursos do Azure a serem gerenciados](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Clique no recurso que você deseja gerenciar, como uma assinatura ou grupo de gerenciamento.

1. Em gerenciar, clique em **funções** para ver a lista de funções dos recursos do Azure.

    ![Funções de recursos do Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Clique em **Adicionar membro** para abrir o painel nova atribuição.

1. Clique em **selecionar uma função** para abrir o painel Selecionar uma função.

    ![Painel de nova atribuição](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Clique em uma função que você deseja atribuir e clique em **selecionar**.

    O painel Selecionar um membro ou grupo é aberto.

1. Clique em um membro ou grupo que você deseja atribuir à função e, em seguida, clique em **selecionar**.

    ![Selecionar um membro ou um painel de grupo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    O painel configurações de associação é aberto.

1. Na lista **tipo de atribuição** , selecione **qualificado** ou **ativo**.

    ![Painel de configurações de associações](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    O PIM para recursos do Azure fornece dois tipos distintos de atribuição:

    - Atribuições **qualificadas** exigem que o membro da função execute uma ação para usar a função. As ações podem incluir a execução de uma verificação de MFA (autenticação multifator), o fornecimento de uma justificativa de negócios ou a solicitação de aprovação de aprovadores designados.

    - As atribuições **ativas** não exigem que o membro execute qualquer ação para usar a função. Os membros atribuídos como ativos têm os privilégios atribuídos à função em todos os momentos.

1. Se a atribuição deve ser permanente (permanentemente qualificada ou permanentemente atribuída), marque a caixa de seleção **permanentemente** .

    Dependendo das configurações de função, a caixa de seleção pode não aparecer ou pode não ser modificável.

1. Para especificar uma duração de atribuição específica, desmarque a caixa de seleção e modifique as caixas de data e hora de início e/ou término.

    ![Configurações de associações-data e hora](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Quando terminar, clique em **Concluído**.

    ![Nova atribuição-adicionar](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Para criar a nova atribuição de função, clique em **Adicionar**. Uma notificação do status é exibida.

    ![Nova atribuição-notificação](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estas etapas para atualizar ou remover uma atribuição de função existente.

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Clique no recurso que você deseja gerenciar, como uma assinatura ou grupo de gerenciamento.

1. Em gerenciar, clique em **funções** para ver a lista de funções dos recursos do Azure.

    ![Funções de recurso do Azure – selecionar função](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Clique na função que você deseja atualizar ou remover.

1. Localize a atribuição de função nas guias **funções qualificadas** ou **funções ativas** .

    ![Atualizar ou remover atribuição de função](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Clique em **Atualizar** ou **remover** para atualizar ou remover a atribuição de função.

    Para obter informações sobre como estender uma atribuição de função, consulte [estender ou renovar funções de recurso do Azure no PIM](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Passos seguintes

- [Estender ou renovar funções de recurso do Azure no PIM](pim-resource-roles-renew-extend.md)
- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções do Azure AD no PIM](pim-how-to-add-role-to-user.md)
