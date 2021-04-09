---
title: Conceder acesso à gestão da PIM - Azure Ative Directory | Microsoft Docs
description: Saiba como conceder acesso a outras administrações para gerir a Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fba46ee5632f7411c433e4bba29201c59c552f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835208"
---
# <a name="delegate-access-to-privileged-identity-management"></a>Delegado de acesso à Gestão de Identidade Privilegiada

Para delegar o acesso à Gestão de Identidade Privilegiada (PIM), um Administrador Global pode atribuir outros utilizadores à função de Administrador de Função Privilegiada. Por padrão, os administradores de segurança e os leitores de Segurança têm acesso apenas de leitura à Gestão de Identidade Privilegiada. Para conceder acesso à Gestão de Identidade Privilegiada, o primeiro utilizador pode atribuir outros à **função de Administrador de Função Privilegiada.** O papel de Administrador de Função Privilegiada é necessário apenas para gerir funções Azure AD. As permissões privilegiadas de administrador de funções não são necessárias para gerir as definições para os recursos Azure.

> [!NOTE]
> Gerir a Gestão de Identidade Privilegiada requer autenticação multi-factor Azure AD. Como as contas da Microsoft não podem registar-se para autenticação multi-factor Azure AD, um utilizador que se inscreva numa conta da Microsoft não pode aceder à Gestão de Identidade Privilegiada.

Certifique-se de que há sempre pelo menos dois utilizadores numa função de Administrador de Função Privilegiada, caso um utilizador esteja bloqueado ou a sua conta seja eliminada.

## <a name="delegate-access-to-manage-pim"></a>Delegado de acesso para gerir PIM

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Em Azure AD, open **Privileged Identity Management**.

1. Selecione **funções AD Azure**.

1. Selecione **Funções**.

    ![Funções de Gestão de Identidade Privilegiada Azure - Funções](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Selecione a **função de Administrador de Função Privilegiada** para abrir a página dos membros.

    ![Administrador privilegiado - Membros](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. **Selecione Adicionar membro** para abrir o painel de **membros geridos Add.**

1. **Selecione Selecione os membros** para abrir o painel **de membros Select.**

    ![Administrador privilegiado de funções - Selecione membros](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selecione um membro e, em seguida, clique **em Selecionar**.

1. Selecione **OK** para tornar o membro elegível para o papel **de Administrador de Função Privilegiada.**

    Quando atribui um novo papel a alguém na Gestão de Identidade Privilegiada, eles são automaticamente configurados como **Elegíveis** para ativar o papel.

1. Para tornar o membro permanente, selecione o utilizador na lista de membros do Administrador de Função Privilegiada.

1. Selecione **Mais** e, em seguida, **Torne-se permanente** para tornar a atribuição permanente.

    ![Administrador privilegiado - Tornar permanente](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Envie ao utilizador um link para [começar a utilizar a Gestão de Identidade Privilegiada.](pim-getting-started.md)

## <a name="remove-access-to-manage-pim"></a>Remover o acesso à gestão do PIM

Antes de remover alguém da função de Administrador de Função Privilegiada, certifique-se sempre de que ainda haverá pelo menos dois utilizadores atribuídos ao mesmo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **funções AD Azure**.

1. Selecione **Funções**.

1. Selecione a **função de Administrador de Função Privilegiada** para abrir a página dos membros.

1. Selecione a caixa de verificação ao lado do utilizador que pretende remover e, em seguida, selecione **Remover o membro**.

    ![Administrador privilegiado de funções - Remova o membro](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Quando lhe for solicitado que confirme que pretende retirar o membro da função, selecione **Sim**.

## <a name="next-steps"></a>Passos seguintes

- [Começar a utilizar o Privileged Identity Management](pim-getting-started.md)
