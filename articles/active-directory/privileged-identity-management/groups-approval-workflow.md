---
title: Aprovar pedidos de ativação para membros do grupo e proprietários em Gestão de Identidade Privilegiada - Azure AD
description: Saiba como aprovar ou negar pedidos de grupos atribuíveis por funções na Azure AD Privileged Identity Management (PIM).
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
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a3b6dad33ce678478a201dc8b7b612f4b8507c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91536992"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>Aprovar pedidos de ativação para membros e proprietários privilegiados do grupo de acesso (pré-visualização)

Com Gestão de Identidade Privilegiada (PIM) em Azure Ative Directory (Azure AD), pode configurar membros e proprietários privilegiados do grupo de acesso para exigir aprovação para ativação, e escolher utilizadores ou grupos da sua organização Azure AD como aprovadores delegados. Recomendamos a seleção de dois ou mais aprovadores para cada grupo para reduzir a carga de trabalho para o administrador privilegiado. Os aprovadores delegados têm 24 horas para aprovar os pedidos. Se um pedido não for aprovado no prazo de 24 horas, o utilizador elegível deve reenencamê-lo novamente. A janela de tempo de aprovação de 24 horas não é configurável.

Siga as etapas deste artigo para aprovar ou negar pedidos de funções de recurso Azure.

## <a name="view-pending-requests"></a>Ver pedidos pendentes

Como um aprovador delegado, receberá uma notificação por e-mail quando um pedido de função de recurso Azure estiver pendente da sua aprovação. Pode visualizar pedidos pendentes na Gestão de Identidade Privilegiada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. **Selecione Aprovar pedidos**.

    ![Aprovar pedidos - Página de recursos Azure mostrando pedido de revisão](./media/groups-approval-workflow/groups-select-request.png)

    Na secção **Pedidos de Ativação de Funções,** verá uma lista de pedidos enquanto se aguarda a sua aprovação.

## <a name="approve-requests"></a>Aprovar pedidos

1. Encontre e selecione o pedido que pretende aprovar e selecione **Aprovar.**

    ![Screenshot que mostra a página "Aprovar pedidos" com os botões "Aprovar" e "Confirmar" realçados.](./media/groups-approval-workflow/groups-confirm-approval.png)

1. Na caixa **de justificação,** insira a justificação do negócio.

1. **Selecione Confirmar**. Uma notificação Azure é gerada pela sua aprovação.

## <a name="deny-requests"></a>Negar pedidos

1. Encontre e selecione o pedido que pretende negar e selecione **Deny.**

    ![Aprovar pedidos - aprovar ou negar painel com detalhes e caixa de justificação](./media/groups-approval-workflow/groups-confirm-denial.png)

1. Na caixa **de justificação,** insira a justificação do negócio.

1. **Selecione Confirmar**. Uma notificação Azure é gerada pela negação.

## <a name="workflow-notifications"></a>Notificações de fluxo de trabalho

Aqui estão algumas informações sobre notificações de fluxo de trabalho:

- Os aprovadores são notificados por e-mail quando um pedido de atribuição de grupo está pendente da sua revisão. As notificações por e-mail incluem um link direto para o pedido, onde o aprovador pode aprovar ou negar.
- Os pedidos são resolvidos pelo primeiro aprovador que aprovar ou negar.
- Quando um aprovador responde ao pedido, todos os aprovadores são notificados da ação.

>[!Note]
>Um administrador que acredite que um utilizador aprovado não deve estar ativo pode remover a atribuição do grupo ativo na Gestão de Identidade Privilegiada. Embora os administradores de recursos não sejam notificados de pedidos pendentes a menos que sejam um aprovador, podem visualizar e cancelar pedidos pendentes para todos os utilizadores, visualizando pedidos pendentes na Gestão de Identidade Privilegiada.

## <a name="next-steps"></a>Passos seguintes

- [Alargar ou renovar atribuições de grupos em Gestão de Identidade Privilegiada](pim-resource-roles-renew-extend.md)
- [Notificações de email em Gestão de Identidade Privilegiada](pim-email-notifications.md)
- [Aprovar ou negar pedidos de atribuição de grupos em Gestão de Identidade Privilegiada](azure-ad-pim-approval-workflow.md)
