---
title: Conceder acesso à gestão da PIM - Diretório Ativo Azure [ Microsoft Docs
description: Saiba como conceder acesso a outras administrações para gerir a Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7424e92f8520d13137b6ac8787523095058a005f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74022117"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Conceder acesso a outros administradores para gerir a Gestão de Identidade Privilegiada

O administrador global que permite a Gestão de Identidade Privilegiada (PIM) para uma organização obtém automaticamente atribuições de funções e acesso à Gestão de Identidade Privilegiada. No entanto, mais ninguém na sua organização Azure Ative Directory (Azure AD) obtém acesso por escrito por defeito, incluindo outros administradores globais. Outros administradores globais, administradores de segurança e leitores de segurança têm acesso apenas a Gestão de Identidade Privilegiada. Para conceder acesso à Gestão de Identidade Privilegiada, o primeiro utilizador pode atribuir outros ao papel de Administrador de **Funções Privilegiadas.**

> [!NOTE]
> Gerir a Gestão de Identidade Privilegiada requer autenticação multi-factor Azure. Uma vez que as contas da Microsoft não podem registar-se para a Autenticação Multi-Factor Do Azure, um utilizador que se inscreva numa conta microsoft não pode aceder à Gestão de Identidade Privilegiada.

Certifique-se de que existem sempre pelo menos dois utilizadores numa função de Administrador de Funções Privilegiadas, caso um utilizador esteja bloqueado ou a sua conta seja eliminada.

## <a name="grant-access-to-manage-pim"></a>Conceder acesso à gestão da PIM

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Em Azure AD, abra **a Gestão de Identidade Privilegiada.**

1. Selecione **funções Azure AD**.

1. Selecione **Funções**.

    ![Funções privilegiadas de Gestão de Identidade Azure AD - Funções](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Selecione a função **de Administrador de Funções Privilegiadas** para abrir a página dos membros.

    ![Administrador de Funções Privilegiadas - Membros](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. **Selecione Adicionar membro** para abrir o painel de membros geridos Add.

1. Selecione **selecione os membros** para abrir o painel de membros Select.

    ![Administrador de Funções Privilegiadas - Selecione membros](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selecione um membro e, em seguida, clique em **Selecionar**.

1. Selecione **OK** para tornar o membro elegível para o papel de **Administrador de Funções Privilegiadas.**

    Quando atribui um novo papel a alguém na Gestão de Identidade Privilegiada, são automaticamente configurados como **Elegíveis** para ativar o papel.

1. Para tornar o membro permanente, selecione o utilizador na lista de membros do Administrador de Funções Privilegiadas.

1. Selecione **Mais** e, em seguida, Tornar permanente para tornar a atribuição **permanente.**

    ![Administrador de Funções Privilegiadas - Tornar permanente](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Envie ao utilizador um link para [começar a utilizar a Gestão de Identidade Privilegiada.](pim-getting-started.md)

## <a name="remove-access-to-manage-pim"></a>Remover acesso para gerir pim

Antes de remover alguém do papel de Administrador de Funções Privilegiadas, certifique-se sempre de que ainda haverá pelo menos dois utilizadores atribuídos ao mesmo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

1. Selecione **Funções**.

1. Selecione a função **de Administrador de Funções Privilegiadas** para abrir a página dos membros.

1. Selecione a caixa de verificação ao lado do utilizador que pretende remover e, em seguida, **selecione Remover membro**.

    ![Administrador de Funções Privilegiada - Remover membro](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Quando lhe for solicitado que confirme que pretende retirar o membro do papel, selecione **Sim**.

## <a name="next-steps"></a>Passos seguintes

- [Começar a utilizar o Privileged Identity Management](pim-getting-started.md)
