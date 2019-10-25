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
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60c0e1bfd457137ddfb8f3ae81fc9044098c1f8d
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809066"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Conceder acesso a outros administradores para gerenciar Privileged Identity Management

O administrador global que habilita Privileged Identity Management (PIM) para uma organização obtém automaticamente atribuições de função e acesso a Privileged Identity Management. Ninguém mais em sua organização do Azure Active Directory (Azure AD) obtém acesso de gravação por padrão, porém, incluindo outros administradores globais. Outros administradores globais, administradores de segurança e leitores de segurança têm acesso somente leitura ao Privileged Identity Management. Para conceder acesso ao Privileged Identity Management, o primeiro usuário pode atribuir outras pessoas à função de **administrador de função com privilégios** .

> [!NOTE]
> O gerenciamento de Privileged Identity Management requer a autenticação multifator do Azure. Como as contas da Microsoft não podem se registrar para a autenticação multifator do Azure, um usuário que entra com um conta Microsoft não pode acessar Privileged Identity Management.

Verifique se há sempre pelo menos dois usuários em uma função de administrador de função com privilégios, caso um usuário esteja bloqueado ou sua conta seja excluída.

## <a name="grant-access-to-manage-pim"></a>Conceder acesso para gerenciar o PIM

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. No Azure AD, abra **Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **funções**.

    ![Privileged Identity Management funções do Azure AD – funções](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Selecione a função de **administrador de função com privilégios** para abrir a página Membros.

    ![Administrador de função com privilégios-Membros](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Selecione **Adicionar membro** para abrir o painel Adicionar Membros gerenciados.

1. Selecione **selecionar Membros** para abrir o painel Selecionar Membros.

    ![Administrador de função com privilégios-selecionar membros](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selecione um membro e clique em **selecionar**.

1. Selecione **OK** para tornar o membro qualificado para a função de **administrador de função com privilégios** .

    Quando você atribui uma nova função a alguém em Privileged Identity Management, elas são configuradas automaticamente como **qualificadas** para ativar a função.

1. Para tornar o membro permanente, selecione o usuário na lista de membros de administrador da função com privilégios.

1. Selecione **mais** e **torne permanente** para tornar a atribuição permanente.

    ![Administrador de função com privilégios-tornar permanente](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Enviar ao usuário um link para [começar a usar Privileged Identity Management](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Remover o acesso para gerenciar o PIM

Antes de remover alguém da função de administrador de função com privilégios, sempre verifique se ainda haverá pelo menos dois usuários atribuídos a ele.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **funções**.

1. Selecione a função de **administrador de função com privilégios** para abrir a página Membros.

1. Marque a caixa de seleção ao lado do usuário que você deseja remover e, em seguida, selecione **Remover membro**.

    ![Administrador de função com privilégios-remover membro](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Quando for solicitado que você confirme que deseja remover o membro da função, selecione **Sim**.

## <a name="next-steps"></a>Passos seguintes

- [Começar a usar o Privileged Identity Management](pim-getting-started.md)
