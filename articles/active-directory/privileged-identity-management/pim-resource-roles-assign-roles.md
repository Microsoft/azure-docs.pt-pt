---
title: Atribuir funções de recursos Azure em Gestão de Identidade Privilegiada - Azure Ative Directory | Microsoft Docs
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
ms.date: 10/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 011b50449f0a02b128bc000535f93e1809fd9777
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673880"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Atribuir funções de recursos da Azure na Gestão de Identidade Privilegiada

O Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) pode gerir as funções de recursos Azure incorporados, bem como funções personalizadas, incluindo (mas não se limitando a):

- Proprietário
- Administrador de Acesso dos Utilizadores
- Contribuinte
- Administrador de Segurança
- Gestor de Segurança

> [!NOTE]
> Os utilizadores ou membros de um grupo atribuído às funções de subscrição do Administrador de Acesso ao Utilizador ou ao Utilizador, e os administradores AD AD Global que permitem a gestão da subscrição em Azure AD têm permissões de administrador de recursos por padrão. Estes administradores podem atribuir funções, configurar definições de funções e rever o acesso usando a Gestão de Identidade Privilegiada para recursos Azure. Um utilizador não pode gerir a Gestão de Identidade Privilegiada para Recursos sem permissões de administrador de recursos. Veja a lista de [funções incorporadas do Azure.](../../role-based-access-control/built-in-roles.md)

## <a name="assign-a-role"></a>Atribuir um papel

Siga estes passos para tornar um utilizador elegível para um papel de recurso Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com permissões de função do Proprietário ou do Administrador de Acesso ao Utilizador.

    Para obter informações sobre como conceder a outro administrador acesso à gestão da Gestão de Identidade Privilegiada, consulte [o Acesso de Concessão a outros administradores para gerir a Gestão de Identidade Privilegiada.](pim-how-to-give-access-to-pim.md)

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **recursos Azure**.

1. Use o filtro de recursos para encontrar os recursos geridos que procura.

    ![Lista de recursos da Azure para gerir](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Selecione o recurso que pretende conseguir abrir a página de visão geral do recurso.

1. Em **Gestão**, **selecione Roles** para ver a lista de funções para recursos Azure.

    ![Funções de recursos Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. **Selecione Adicionar atribuições** para abrir o painel **de atribuições Adicionar.**

1. **Selecione selecionar uma função** para abrir a página **De função.**

    ![Novo painel de atribuição](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Selecione uma função que pretende atribuir e, em seguida, clique em **Selecionar**.

    **O painel Select um membro ou painel** de grupo abre.

1. Selecione um membro ou grupo que pretende atribuir à função e, em seguida, clique em **Select**.

    ![Selecione um membro ou painel de grupo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. No **separador Definições,** na lista de **tipos de atribuição,** selecione **Elegível** ou **Ativo**.

    ![Painel de configurações de membros](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    A Gestão privilegiada de Identidade para recursos Azure fornece dois tipos distintos de atribuição:

    - As atribuições **elegíveis** requerem que o membro do papel execute uma ação para usar o papel. As ações podem incluir a realização de uma verificação de autenticação multi-factor (MFA), a justificação do negócio ou a solicitação de aprovação dos aprovadores designados.

    - As atribuições **ativas** não requerem que o membro execute qualquer ação para usar o papel. Os membros designados como ativos têm sempre os privilégios atribuídos ao papel.

1. Para especificar uma duração específica da atribuição, altere as datas e horários de início e fim.

1. Quando terminar, **selecione Atribuir**.

1. Após a criação da nova atribuição de funções, é apresentada uma notificação de estado.

    ![Nova atribuição - Notificação](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estes passos para atualizar ou remover uma atribuição de função existente.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **recursos Azure**.

1. Selecione o recurso que pretende conseguir abrir a sua página geral.

1. Em **Gestão**, **selecione Roles** para ver a lista de funções para recursos Azure.

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
