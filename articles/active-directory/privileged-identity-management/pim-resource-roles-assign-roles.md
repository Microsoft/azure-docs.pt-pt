---
title: Atribuir funções de recursos do Azure no PIM | Documentos da Microsoft
description: Saiba como atribuir funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee09526c2bf1ebf9821e1f84dc99bfc8635b9ee2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086195"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Atribuir funções de recursos do Azure no PIM

PIM do Azure AD pode gerir as funções de recursos do Azure incorporadas, bem como funções personalizadas, incluindo (mas não limitado a):

- Proprietário
- Administrador de Acesso de Utilizador
- Contribuinte
- Administrador de Segurança
- Gestor de segurança e muito mais

> [!NOTE]
> Os utilizadores ou membros de um grupo atribuídas às funções de proprietário ou administrador de acesso de utilizadores e os administradores globais que permitem a gestão de subscrições no Azure AD são administradores de recursos. Estes administradores podem atribuir funções, configure definições de função e rever o acesso a utilizar o PIM para recursos do Azure. Ver a lista de [funções incorporadas para recursos do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Atribuir uma função

Siga estes passos para tornar um utilizador elegível para uma função de recursos do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com um utilizador que seja um membro a [administrador com função privilegiada](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) função.

    Para obter informações sobre como conceder outro acesso de administrador para gerir o PIM, consulte [conceder acesso a outros administradores para gerir o PIM](pim-how-to-give-access-to-pim.md).

1. Open **do Azure AD Privileged Identity Management**.

    Se ainda não começou PIM no portal do Azure, aceda a [começar a utilizar o PIM](pim-getting-started.md).

1. Clique em **recursos do Azure**.

1. Utilize o **filtro de recursos** para filtrar a lista de recursos geridos.

    ![Lista de recursos do Azure para gerir](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Clique em recursos que pretende gerir, tal como um grupo de gestão ou de subscrição.

1. Em gerir, clique em **funções** para ver a lista de funções para recursos do Azure.

    ![Funções de recursos do Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Clique em **Add member** para abrir o painel de atribuição de novo.

1. Clique em **selecionar uma função** para abrir um painel de função de Select.

    ![Novo painel de atribuição](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Clique numa função que pretende atribuir e, em seguida, clique em **selecione**.

    Selecione um membro ou grupo no painel abre-se.

1. Clique num membro ou um grupo que pretende atribuir à função e, em seguida, clique em **selecione**.

    ![Selecione um painel de membro ou grupo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    É aberto o painel de definições de associação.

1. Na **tipo de atribuição** lista, selecione **elegível** ou **Active**.

    ![Painel de definições de associações](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    PIM para recursos do Azure fornece dois tipos de atribuição distintos:

    - **Elegível** atribuições requerem o membro da função de executar uma ação para utilizar a função. Ações podem incluir a efetuar uma verificação de autenticação multifator (MFA), fornecer uma justificação de negócio ou o pedido de aprovação de aprovadores designados.

    - **Active Directory** atribuições não exigem o membro efetuar qualquer ação para utilizar a função. Os membros atribuídos como ativo têm os privilégios atribuídos à função tempo todo.

1. Se a atribuição permanente (permanentemente elegível ou atribuídos de forma permanente), selecione o **permanentemente** caixa de verificação.

    Consoante as definições de função, a caixa de verificação podem não aparecer, ou pode ser unmodifiable.

1. Especifique uma duração de atribuição específica, desmarque a caixa de verificação e modificar as caixas data e hora de início e/ou final.

    ![Definições de associações - data e hora](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Quando terminar, clique em **Concluído**.

    ![Nova atribuição - adicionar](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Para criar a nova atribuição de função, clique em **adicionar**. É apresentada uma notificação do Estado.

    ![Nova atribuição - notificação](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existentes

Siga estes passos para atualizar ou remover uma atribuição de função existente.

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Clique em recursos que pretende gerir, tal como um grupo de gestão ou de subscrição.

1. Em gerir, clique em **funções** para ver a lista de funções para recursos do Azure.

    ![Funções de recursos do Azure - selecionar função](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Clique na função que pretende atualizar ou remover.

1. Encontrar a atribuição de função sobre o **funções elegíveis** ou **funções ativas** separadores.

    ![Atualizar ou remover a atribuição de função](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Clique em **atualizar** ou **remover** para atualizar ou remover a atribuição de função.

    Para obter informações sobre a expansão de uma atribuição de função, veja [expandir ou renovar funções de recursos do Azure no PIM](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Passos Seguintes

- [Expandir ou renovar funções de recursos do Azure no PIM](pim-resource-roles-renew-extend.md)
- [Configurar as definições de função de recursos do Azure no PIM](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções de diretório do Azure AD no PIM](pim-how-to-add-role-to-user.md)
