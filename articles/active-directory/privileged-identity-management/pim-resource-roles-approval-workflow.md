---
title: Aprovar ou negar solicitações para funções de recurso do Azure em PIM-Azure Active Directory | Microsoft Docs
description: Saiba como aprovar ou negar solicitações para funções de recurso do Azure no Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 8b39434f8763e44a126f74ac9a19596e4413ae9c
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804269"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Aprovar ou negar solicitações para funções de recurso do Azure no PIM

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), você pode configurar funções para exigir aprovação para ativação e escolher um ou vários usuários ou grupos como aprovadores delegados. Os aprovadores delegados têm 24 horas para aprovar solicitações. Se uma solicitação não for aprovada dentro de 24 horas, o usuário elegível deverá enviar novamente uma nova solicitação. A janela de tempo de aprovação de 24 horas não é configurável.

Siga as etapas neste artigo para aprovar ou negar solicitações para funções de recurso do Azure.

## <a name="view-pending-requests"></a>Ver pedidos pendentes

Como um Aprovador delegado, você receberá uma notificação por email quando uma solicitação de função de recurso do Azure estiver aguardando sua aprovação. Você pode exibir essas solicitações pendentes no PIM.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **aprovar solicitações**.

    ![Aprovar solicitações-página de recursos do Azure mostrando solicitação para revisão](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    Na seção **solicitações para ativações de função** , você verá uma lista de solicitações que aguardam sua aprovação.

## <a name="approve-requests"></a>Aprovar pedidos

1. Localize e clique na solicitação que você deseja aprovar. Um painel aprovar ou negar é exibido.

    ![Aprovar solicitações – painel aprovar ou negar com detalhes e caixa de justificação](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na caixa de **justificação** , digite um motivo.

1. Clique em **aprovar**.

    Uma notificação é exibida com sua aprovação.

    ![Aprovar notificação mostrando que a solicitação foi aprovada](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Negar solicitações

1. Localize e clique na solicitação que você deseja negar. Um painel aprovar ou negar é exibido.

    ![Aprovar solicitações – painel aprovar ou negar com detalhes e caixa de justificação](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na caixa de **justificação** , digite um motivo.

1. Clique em **negar**.

    Uma notificação é exibida com sua negação.

## <a name="workflow-notifications"></a>Notificações de fluxo de trabalho

Veja algumas informações sobre notificações de fluxo de trabalho:

- Todos os membros da lista de aprovadores são notificados por email quando uma solicitação de uma função está aguardando sua revisão. As notificações por email incluem um link direto para a solicitação, onde o aprovador pode aprovar ou negar.
- As solicitações são resolvidas pelo primeiro membro da lista que aprova ou nega.
- Quando um Aprovador responde à solicitação, todos os membros da lista de aprovadores são notificados sobre a ação.
- Os administradores de recursos são notificados quando um membro aprovado se torna ativo em sua função.

>[!Note]
>Um administrador de recursos que acredita que um membro aprovado não deve estar ativo pode remover a atribuição de função ativa no PIM. Embora os administradores de recursos não sejam notificados sobre solicitações pendentes, a menos que sejam membros da lista de aprovadores, eles podem exibir e cancelar solicitações pendentes de todos os usuários exibindo solicitações pendentes no PIM. 

## <a name="next-steps"></a>Passos Seguintes

- [Estender ou renovar funções de recurso do Azure no PIM](pim-resource-roles-renew-extend.md)
- [Notificações por email no PIM](pim-email-notifications.md)
- [Aprovar ou negar solicitações para funções do Azure AD no PIM](azure-ad-pim-approval-workflow.md)
