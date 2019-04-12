---
title: Aprovar ou recusar pedidos para funções de recursos do Azure no PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como aprovar ou recusar pedidos para funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
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
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f645b7077ef43dc7eb4d70261b6b601b5e4af1b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492163"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Aprovar ou recusar pedidos para funções de recursos do Azure no PIM

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), pode configurar funções para exigir a aprovação para a ativação e escolher um ou vários utilizadores ou grupos como aprovadores delegados. Aprovadores delegados têm 24 horas para aprovar pedidos. Se um pedido não está aprovado dentro de 24 horas, em seguida, o utilizador elegível tem novamente submeter um novo pedido. A janela de tempo de aprovação de 24 horas não é configurável.

Siga os passos neste artigo para aprovar ou recusar pedidos para funções de recursos do Azure.

## <a name="view-pending-requests"></a>Ver pedidos pendentes

Como um aprovador de delegados, receberá uma notificação por e-mail quando uma solicitação de função de recursos do Azure está com aprovação pendente. Pode ver estes pedidos pendentes no PIM.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **aprovar pedidos**.

    ![Recursos do Azure - aprovar pedidos](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    Na **pedidos de ativações de função** seção, verá uma lista de pedidos com aprovação pendente.

## <a name="approve-requests"></a>Aprovar pedidos

1. Encontre e clique em pedido de que pretende aprovar. Um painel de aprovação será exibido.

    ![Aprovar o painel de pedidos](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na **justificação** , escreva um motivo.

1. Clique em **aprovar**.

    É apresentada uma notificação com a sua aprovação.

    ![Aprovar a notificação](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Negar pedidos

1. Encontre e clique em pedido de que pretende negar. Um painel de aprovação será exibido.

    ![Aprovar o painel de pedidos](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na **justificação** , escreva um motivo.

1. Clique em **negar**.

    É apresentada uma notificação com o tipo de negação.

## <a name="workflow-notifications"></a>Notificações do fluxo de trabalho

Eis algumas informações sobre notificações de fluxo de trabalho:

- Todos os membros da lista aprovador notificados por e-mail quando um pedido para uma função está a aguardar a revisão. As notificações de e-mail incluem uma ligação direta para o pedido, em que o aprovador pode aprovar ou negar.
- Pedidos são resolvidos pelo primeiro membro da lista que aprova ou nega.
- Quando um aprovador responde à solicitação, todos os membros da lista aprovador são notificados da ação.
- Os administradores de recursos são notificados quando um membro aprovado se torna ativo na sua função.

>[!Note]
>Um administrador de recursos que acredita que um membro aprovado não deve estar ativo pode remover a atribuição de função ativa no PIM. Embora os administradores de recursos não são notificados de pedidos pendentes, a menos que são membros da lista aprovador, pode ver e cancelar solicitações de todos os utilizadores pendentes visualizando pedidos no PIM pendentes. 

## <a name="next-steps"></a>Passos Seguintes

- [Expandir ou renovar funções de recursos do Azure no PIM](pim-resource-roles-renew-extend.md)
- [Notificações por e-mail no PIM](pim-email-notifications.md)
- [Aprovar ou recusar pedidos para funções do Azure AD no PIM](azure-ad-pim-approval-workflow.md)
