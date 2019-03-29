---
title: Conceder acesso a outros administradores para gerir PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como conceder acesso a outros as administrações para gerir o Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74b938335dbace5a827bcfa43172704dc8b09da2
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579619"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Conceder acesso a outros administradores para gerir o PIM

O Administrador Global que ativa para uma organização, a Azure Active Directory (Azure AD) Privileged Identity Management (PIM) automaticamente obter atribuições de funções e acesso ao PIM. Ninguém mais obtém acesso de escrita por predefinição, no entanto, incluindo outros administradores globais. Outros administradores globais, administradores de segurança e leitores de segurança têm acesso só de leitura ao PIM. Para conceder acesso ao PIM, o primeiro utilizador pode atribuir outras pessoas para o **administrador com função privilegiada** função.

> [!NOTE]
> Gerir PIM requer o MFA do Azure. Uma vez que as contas Microsoft não é possível registar para MFA do Azure, um utilizador que inicia sessão com uma conta Microsoft não é possível aceder PIM.

Certificar-se de que existem sempre pelo menos dois utilizadores numa função de administrador com função privilegiada, no caso de um utilizador está bloqueado ou a sua conta foi eliminada.

## <a name="grant-access-to-manage-pim"></a>Conceder acesso para gerir o PIM

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **funções do Azure AD**.

1. Clique em **funções**.

    ![Funções do Azure de PIM AD - funções](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Clique nas **administrador com função privilegiada** função para abrir a página de membros.

    ![Administrador com função privilegiada - membros](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Clique em **Add member** para abrir o painel de membros geridos de adicionar.

1. Clique em **selecionar membros do** para abrir o painel de membros selecione.

    ![Administrador com função privilegiada - selecionados membros](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selecione um membro e, em seguida, clique em **selecione**.

1. Clique em **OK** para tornar o membro elegível para o **administrador com função privilegiada** função.

    Quando atribui uma nova função para alguém no PIM, eles são automaticamente configurados como **elegíveis** para ativar a função.

1. Para tornar o membro permanente, clique o utilizador na lista de membros de administrador com função privilegiada.

1. Clique em **mais** e, em seguida **tornar permanente** para fazer a atribuição permanente.

    ![Administrador com função privilegiada - tornar permanente](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Enviar ao usuário uma ligação para [começar a utilizar o PIM](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Remover o acesso para gerir o PIM

Antes de remover alguém da função de administrador com função privilegiada, sempre Verifique se ainda haverá, pelo menos, dois utilizadores atribuídos à mesma.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **funções do Azure AD**.

1. Clique em **funções**.

1. Clique nas **administrador com função privilegiada** função para abrir a página de membros.

1. Adicionar uma marca de verificação junto ao utilizador que pretende remover e, em seguida, clique em **remover membro**.

    ![Administrador com função privilegiada - remover membro](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Na mensagem que é exibido, perguntando se quiser remover o membro da função, clique em **Sim**.

## <a name="next-steps"></a>Passos Seguintes

- [Comece a utilizar o PIM](pim-getting-started.md)
