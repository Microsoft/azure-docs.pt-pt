---
title: Aprovar solicitações para funções de recurso do Azure no PIM – Azure AD | Microsoft Docs
description: Saiba como aprovar ou negar solicitações para funções de recurso do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e779f633efccf7b594c193e165a584d22b1d653b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021975"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Aprovar ou negar solicitações para funções de recurso do Azure no Privileged Identity Management

Com o Privileged Identity Management (PIM) no Azure Active Directory (Azure AD), você pode configurar funções para exigir aprovação para ativação e escolher usuários ou grupos da sua organização do Azure AD como aprovadores delegados. É recomendável selecionar dois ou mais aprovadores para cada função para reduzir a carga de trabalho para o administrador da função com privilégios. Os aprovadores delegados têm 24 horas para aprovar solicitações. Se uma solicitação não for aprovada dentro de 24 horas, o usuário elegível deverá enviar novamente uma nova solicitação. A janela de tempo de aprovação de 24 horas não é configurável.

Siga as etapas neste artigo para aprovar ou negar solicitações para funções de recurso do Azure.

## <a name="view-pending-requests"></a>Exibir solicitações pendentes

Como um Aprovador delegado, você receberá uma notificação por email quando uma solicitação de função de recurso do Azure estiver aguardando sua aprovação. Você pode exibir essas solicitações pendentes no Privileged Identity Management.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **aprovar solicitações**.

    ![Aprovar solicitações-página de recursos do Azure mostrando solicitação para revisão](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    Na seção **solicitações para ativações de função** , você verá uma lista de solicitações que aguardam sua aprovação.

## <a name="approve-requests"></a>Aprovar pedidos

1. Localize e selecione a solicitação que você deseja aprovar. Uma página aprovar ou negar é exibida.

    ![Aprovar solicitações – painel aprovar ou negar com detalhes e caixa de justificação](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na caixa de **justificação** , insira a justificativa comercial.

1. Selecione **aprovar**. Você receberá uma notificação do Azure de sua aprovação.

    ![Aprovar notificação mostrando que a solicitação foi aprovada](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Negar solicitações

1. Localize e selecione a solicitação que você deseja negar. Uma página aprovar ou negar é exibida.

    ![Aprovar solicitações – painel aprovar ou negar com detalhes e caixa de justificação](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na caixa de **justificação** , insira a justificativa comercial.

1. Selecione **negar**. Uma notificação é exibida com sua negação.

## <a name="workflow-notifications"></a>Notificações de fluxo de trabalho

Veja algumas informações sobre notificações de fluxo de trabalho:

- Os aprovadores são notificados por email quando uma solicitação de uma função está aguardando sua revisão. As notificações por email incluem um link direto para a solicitação, onde o aprovador pode aprovar ou negar.
- As solicitações são resolvidas pelo primeiro aprovador que aprova ou nega.
- Quando um Aprovador responde à solicitação, todos os aprovadores são notificados sobre a ação.
- Os administradores de recursos são notificados quando um usuário aprovado se torna ativo em sua função.

>[!Note]
>Um administrador de recursos que acredita que um usuário aprovado não deve estar ativo pode remover a atribuição de função ativa em Privileged Identity Management. Embora os administradores de recursos não sejam notificados sobre solicitações pendentes, a menos que sejam um aprovador, eles podem exibir e cancelar solicitações pendentes para todos os usuários exibindo solicitações pendentes no Privileged Identity Management.

## <a name="next-steps"></a>Passos seguintes

- [Estender ou renovar funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Notificações por email no Privileged Identity Management](pim-email-notifications.md)
- [Aprovar ou negar solicitações para funções do Azure AD no Privileged Identity Management](azure-ad-pim-approval-workflow.md)
