---
title: Conceder acesso a outros administradores para gerenciar o PIM-Azure Active Directory | Microsoft Docs
description: Saiba como conceder acesso a outras administrações para gerenciar Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3a0173108b6c884994ca25fd0495e9cb8d45186
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804359"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Conceder acesso a outros administradores para gerenciar o PIM

O administrador global que habilita o Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para uma organização obtém automaticamente atribuições de função e acesso ao PIM. No entanto, ninguém mais tem acesso de gravação por padrão, incluindo outros administradores globais. Outros administradores globais, administradores de segurança e leitores de segurança têm acesso somente leitura ao PIM. Para conceder acesso ao PIM, o primeiro usuário pode atribuir outras pessoas à função de **administrador de função com privilégios** .

> [!NOTE]
> O gerenciamento do PIM requer o Azure MFA. Como as contas da Microsoft não podem se registrar no Azure MFA, um usuário que se conecta com um conta Microsoft não pode acessar o PIM.

Verifique se há sempre pelo menos dois usuários em uma função de administrador de função com privilégios, caso um usuário esteja bloqueado ou sua conta seja excluída.

## <a name="grant-access-to-manage-pim"></a>Conceder acesso para gerenciar o PIM

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **funções do Azure ad**.

1. Clique em **funções**.

    ![Funções do Azure AD do PIM – funções](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Clique na função **administrador de função com privilégios** para abrir a página Membros.

    ![Administrador de função com privilégios-Membros](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Clique em **Adicionar membro** para abrir o painel Adicionar Membros gerenciados.

1. Clique em **selecionar Membros** para abrir o painel Selecionar Membros.

    ![Administrador de função com privilégios-selecionar membros](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selecione um membro e clique em **selecionar**.

1. Clique em **OK** para tornar o membro qualificado para a função de **administrador de função com privilégios** .

    Quando você atribui uma nova função a alguém no PIM, elas são configuradas automaticamente como **qualificadas** para ativar a função.

1. Para tornar o membro permanente, clique no usuário na lista de membros de administrador da função com privilégios.

1. Clique em **mais** e em **tornar permanente** para tornar a atribuição permanente.

    ![Administrador de função com privilégios-tornar permanente](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Envie ao usuário um link para [começar a usar o PIM](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Remover o acesso para gerenciar o PIM

Antes de remover alguém da função de administrador de função com privilégios, sempre verifique se ainda haverá pelo menos dois usuários atribuídos a ele.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **funções do Azure ad**.

1. Clique em **funções**.

1. Clique na função **administrador de função com privilégios** para abrir a página Membros.

1. Adicione uma marca de seleção ao lado do usuário que você deseja remover e, em seguida, clique em **Remover membro**.

    ![Administrador de função com privilégios-remover membro](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Na mensagem que aparece perguntando se você deseja remover o membro da função, clique em **Sim**.

## <a name="next-steps"></a>Passos Seguintes

- [Comece a utilizar o PIM](pim-getting-started.md)
