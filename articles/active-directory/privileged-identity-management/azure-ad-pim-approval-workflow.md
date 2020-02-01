---
title: Aprovar ou negar solicitações para funções do Azure AD no PIM – Azure AD | Microsoft Docs
description: Saiba como aprovar ou negar solicitações para funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bc4b518cd2400cf3826b31a590d499c1ad031e7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896526"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Aprovar ou negar solicitações para funções do Azure AD no Privileged Identity Management

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), você pode configurar funções para exigir aprovação para ativação e escolher um ou vários usuários ou grupos como aprovadores delegados. Os aprovadores delegados têm 24 horas para aprovar solicitações. Se uma solicitação não for aprovada dentro de 24 horas, o usuário elegível deverá enviar novamente uma nova solicitação. A janela de tempo de aprovação de 24 horas não é configurável.

## <a name="determine-your-version-of-pim"></a>Determinar sua versão do PIM

A partir de novembro de 2019, a parte das funções do Azure AD da Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências de funções de recurso do Azure. Isso cria recursos adicionais, bem como [as alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo distribuída, os procedimentos que você seguir neste artigo dependem da versão do Privileged Identity Management que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Privileged Identity Management você tem. Depois de saber sua versão do Privileged Identity Management, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que esteja na função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Abra **Azure ad Privileged Identity Management**. Se você tiver uma faixa na parte superior da página Visão geral, siga as instruções na **nova guia versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior** .

    ![Nova versão das funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-new-version.png)

Siga as etapas neste artigo para aprovar ou negar solicitações para funções do Azure AD.

# <a name="previous-versiontabprevious"></a>[Versão anterior](#tab/previous)

## <a name="view-pending-requests"></a>Ver pedidos pendentes

Como um Aprovador delegado, você receberá uma notificação por email quando uma solicitação de função do Azure AD estiver aguardando sua aprovação. Você pode exibir essas solicitações pendentes no Privileged Identity Management.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **funções do Azure ad**.

1. Clique em **aprovar solicitações**.

    ![Funções do Azure AD – aprovar solicitações](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Você verá uma lista de solicitações que aguardam sua aprovação.

## <a name="approve-requests"></a>Aprovar pedidos

1. Selecione as solicitações que você deseja aprovar e, em seguida, clique em **aprovar** para abrir o painel aprovar solicitações selecionadas.

    ![Aprovar a lista de solicitações com a opção aprovar realçada](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Na caixa **aprovar motivo** , digite um motivo.

    ![Aprovar o painel de solicitações selecionadas com um motivo de aprovação](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Clique **em Aprovar**.

    O símbolo de status será atualizado com sua aprovação.

    ![Aprovar o painel de solicitações selecionadas após o botão aprovar clicado](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Negar solicitações

1. Selecione as solicitações que você deseja negar e clique em **negar** para abrir o painel negar solicitações selecionadas.

    ![Aprovar a lista de solicitações com a opção negar realçada](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Na caixa **motivo da negação** , digite um motivo.

    ![Negar o painel de solicitações selecionadas com um motivo de negação](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Clique em **negar**.

    O símbolo de status será atualizado com sua negação.

# <a name="new-versiontabnew"></a>[Nova versão](#tab/new)

## <a name="view-pending-requests"></a>Ver pedidos pendentes

Como um Aprovador delegado, você receberá uma notificação por email quando uma solicitação de função de recurso do Azure estiver aguardando sua aprovação. Você pode exibir essas solicitações pendentes no Privileged Identity Management.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **aprovar solicitações**.

    ![Aprovar solicitações-página de recursos do Azure mostrando solicitação para revisão](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    Na seção **solicitações para ativações de função** , você verá uma lista de solicitações que aguardam sua aprovação.

## <a name="approve-requests"></a>Aprovar pedidos

1. Localize e selecione a solicitação que você deseja aprovar. Uma página aprovar ou negar é exibida.

    ![Aprovar solicitações – painel aprovar ou negar com detalhes e caixa de justificação](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

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

>[!NOTE]
>Um administrador de recursos que acredita que um usuário aprovado não deve estar ativo pode remover a atribuição de função ativa em Privileged Identity Management. Embora os administradores de recursos não sejam notificados sobre solicitações pendentes, a menos que sejam um aprovador, eles podem exibir e cancelar solicitações pendentes para todos os usuários exibindo solicitações pendentes no Privileged Identity Management.

---

## <a name="next-steps"></a>Passos seguintes

- [Notificações por email no Privileged Identity Management](pim-email-notifications.md)
- [Aprovar ou negar solicitações para funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-approval-workflow.md)
