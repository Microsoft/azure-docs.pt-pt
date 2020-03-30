---
title: Aprovar pedidos de funções de recurso Azure na PIM - Azure AD Microsoft Docs
description: Saiba como aprovar ou negar pedidos de funções de recursos Azure na Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021975"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Aprovar ou negar pedidos de funções de recurso Azure na Gestão de Identidade Privilegiada

Com a Privileged Identity Management (PIM) no Azure Ative Directory (Azure AD), pode configurar funções para exigir aprovação para ativação, e escolher utilizadores ou grupos da sua organização Azure AD como aprovadores delegados. Recomendamos selecionar dois ou mais aprovadores para cada função para reduzir a carga de trabalho para o administrador de funções privilegiado. Os aprovadores delegados têm 24 horas para aprovar os pedidos. Se um pedido não for aprovado no prazo de 24 horas, o utilizador elegível deve reenviar um novo pedido. A janela de tempo de aprovação de 24 horas não é configurável.

Siga os passos deste artigo para aprovar ou negar pedidos de funções de recurso Azure.

## <a name="view-pending-requests"></a>Ver pedidos pendentes

Como um aprovador delegado, receberá uma notificação por e-mail quando um pedido de função de recurso Azure estiver pendente da sua aprovação. Pode ver estes pedidos pendentes na Gestão de Identidade Privilegiada.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **Aprovar pedidos**.

    ![Aprovar pedidos - Página de recursos do Azure mostrando pedido de revisão](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    Na secção Pedidos de Ativação de **Papéis,** verá uma lista de pedidos pendentes da sua aprovação.

## <a name="approve-requests"></a>Aprovar pedidos

1. Encontre e selecione o pedido que pretende aprovar. Aparece uma página de aprovação ou negação.

    ![Aprovar pedidos - aprovar ou negar painel com detalhes e caixa de justificação](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na caixa **de Justificação,** insira a justificação do negócio.

1. Selecione **Aprovar**. Receberá uma notificação Azure da sua aprovação.

    ![Aprovar notificação mostrando que o pedido foi aprovado](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Negar pedidos

1. Encontre e selecione o pedido que pretende negar. Aparece uma página de aprovação ou negação.

    ![Aprovar pedidos - aprovar ou negar painel com detalhes e caixa de justificação](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na caixa **de Justificação,** insira a justificação do negócio.

1. Selecione **Negar**. Uma notificação aparece com a sua negação.

## <a name="workflow-notifications"></a>Notificações de fluxo de trabalho

Aqui está algumas informações sobre notificações de fluxo de trabalho:

- Os aprovadores são notificados por e-mail quando um pedido de função está pendente da sua revisão. As notificações por e-mail incluem um link direto para o pedido, onde o aprovador pode aprovar ou negar.
- Os pedidos são resolvidos pelo primeiro aprovador que aprova ou nega.
- Quando um aprovador responde ao pedido, todos os aprovadores são notificados da ação.
- Os administradores de recursos são notificados quando um utilizador aprovado se torna ativo na sua função.

>[!Note]
>Um administrador de recursos que acredite que um utilizador aprovado não deve estar ativo pode remover a atribuição de funções ativas na Gestão de Identidade Privilegiada. Embora os administradores de recursos não sejam notificados dos pedidos pendentes a menos que sejam um aprovador, podem visualizar e cancelar pedidos pendentes para todos os utilizadores, visualizando pedidos pendentes em Gestão de Identidade Privilegiada.

## <a name="next-steps"></a>Passos seguintes

- [Alargar ou renovar funções de recurso Azure na Gestão de Identidade Privilegiada](pim-resource-roles-renew-extend.md)
- [Notificações por e-mail em Gestão de Identidade Privilegiada](pim-email-notifications.md)
- [Aprovar ou negar pedidos de funções da Azure AD na Gestão de Identidade Privilegiada](azure-ad-pim-approval-workflow.md)
