---
title: Aprovar ou negar pedidos de funções da AD Azure na PIM - Azure AD ! Microsoft Docs
description: Saiba como aprovar ou negar pedidos de funções de Azure AD na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4778ce5efcd6145447c194eaab34bc230bcbe34c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371550"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Aprovar ou negar pedidos de funções de Azure AD na Gestão de Identidade Privilegiada

Com o Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada (PIM), pode configurar funções para exigir aprovação para ativação, e escolher um ou vários utilizadores ou grupos como aprovadores delegados. Os aprovadores delegados têm 24 horas para aprovar os pedidos. Se um pedido não for aprovado no prazo de 24 horas, o utilizador elegível deve reenencamê-lo novamente. A janela de tempo de aprovação de 24 horas não é configurável.

## <a name="determine-your-version-of-pim"></a>Determine a sua versão de PIM

A partir de novembro de 2019, a parte de funções da AZure AD da Gestão de Identidade Privilegiada está a ser atualizada para uma nova versão que corresponda às experiências para funções do Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está a ser lançada, quais os procedimentos que segue neste artigo dependem da versão de Gestão de Identidade Privilegiada que tem atualmente. Siga os passos nesta secção para determinar qual a versão da Gestão de Identidade Privilegiada que tem. Depois de conhecer a sua versão de Gestão de Identidade Privilegiada, pode selecionar os procedimentos deste artigo que correspondam a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que esteja na [função de administrador](../roles/permissions-reference.md#privileged-role-administrator) privilegiado.
1. Open **Azure AD Gestão de Identidade Privilegiada.** Se tiver um banner no topo da página geral, siga as instruções no separador **Nova versão** deste artigo. Caso contrário, siga as instruções no separador **versão anterior.**

    [![Selecione Azure AD > Gestão de Identidade Privilegiada.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Siga os passos deste artigo para aprovar ou negar pedidos de funções Azure AD.

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="view-pending-requests"></a>Ver pedidos pendentes

Como aprovador delegado, receberá uma notificação por e-mail quando um pedido de função AD Azure estiver pendente da sua aprovação. Pode ver estes pedidos pendentes na Gestão de Identidade Privilegiada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. **Selecione Aprovar pedidos**.

    ![Aprovar pedidos - página mostrando pedido para rever funções Azure AD](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    Na secção **Pedidos de Ativação de Funções,** verá uma lista de pedidos enquanto se aguarda a sua aprovação.

## <a name="approve-requests"></a>Aprovar pedidos

1. Encontre e selecione o pedido que pretende aprovar. Aparece uma página de aprovação ou negação.

    ![Screenshot que mostra a página "Aprovar pedidos - funções Ad Azure".](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Na caixa **de justificação,** insira a justificação do negócio.

1. Selecione **Aprovar**. Receberá uma notificação da Azure da sua aprovação.

    ![Aprovação de notificação mostrando pedido foi aprovado](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

## <a name="deny-requests"></a>Negar pedidos

1. Encontre e selecione o pedido que pretende negar. Aparece uma página de aprovação ou negação.

    ![Aprovar pedidos - aprovar ou negar painel com detalhes e caixa de justificação](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na caixa **de justificação,** insira a justificação do negócio.

1. **Selecione Deny**. Uma notificação aparece com a sua negação.

## <a name="workflow-notifications"></a>Notificações de fluxo de trabalho

Aqui estão algumas informações sobre notificações de fluxo de trabalho:

- Os aprovadores são notificados por e-mail quando um pedido de uma função está pendente da sua revisão. As notificações por e-mail incluem um link direto para o pedido, onde o aprovador pode aprovar ou negar.
- Os pedidos são resolvidos pelo primeiro aprovador que aprovar ou negar.
- Quando um aprovador responde ao pedido, todos os aprovadores são notificados da ação.
- Os administradores globais e os administradores privilegiados são notificados quando um utilizador aprovado se torna ativo no seu papel.

>[!NOTE]
>Um administrador global ou administrador privilegiado que acredita que um utilizador aprovado não deve estar ativo pode remover a atribuição de papel ativo na Gestão de Identidade Privilegiada. Embora os administradores não sejam notificados dos pedidos pendentes a menos que sejam um aprovador, podem visualizar e cancelar quaisquer pedidos pendentes para todos os utilizadores, visualizando pedidos pendentes na Gestão de Identidade Privilegiada.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="view-pending-requests"></a>Ver pedidos pendentes

Como aprovador delegado, receberá uma notificação por e-mail quando um pedido de função AD Azure estiver pendente da sua aprovação. Pode ver estes pedidos pendentes na Gestão de Identidade Privilegiada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Clique **nos papéis de AD Azure**.

1. Clique **em Aprovar pedidos.**

    ![Funções AD AZure - Aprovar pedidos](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Verá uma lista de pedidos enquanto aguarda a sua aprovação.

## <a name="approve-requests"></a>Aprovar pedidos

1. Selecione os pedidos que pretende aprovar e, em seguida, clique em **Aprovar** para abrir o painel de pedidos selecionados.

    ![Aprovar lista de pedidos com opção aprovação em destaque](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Na caixa **de razão Aprovação,** escreva uma razão.

    ![Aprovar o painel de pedidos selecionados com um motivo de aprovação](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Clique em **Aprovar**.

    O símbolo de Estado será atualizado com a sua aprovação.

    ![Aprovar painel de pedidos selecionados após aprovação botão clicado](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Negar pedidos

1. Selecione os pedidos que pretende negar e, em seguida, clique em **Negar** para abrir o painel de pedidos selecionados Deny.

    ![Aprovar lista de pedidos com opção Deny em destaque](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Na caixa de **razão Deny,** escreva uma razão.

    ![Negar pedidos selecionados painel com uma razão de negação](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. **Selecione Deny**.

    O símbolo de Estado será atualizado com a sua negação.

---

## <a name="next-steps"></a>Passos seguintes

- [Notificações de email em Gestão de Identidade Privilegiada](pim-email-notifications.md)
- [Aprovar ou negar pedidos de funções de recursos da Azure na Gestão de Identidade Privilegiada](pim-resource-roles-approval-workflow.md)
