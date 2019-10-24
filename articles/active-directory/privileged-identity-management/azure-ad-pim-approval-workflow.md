---
title: Aprovar ou negar solicitações para funções do Azure AD no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como aprovar ou negar solicitações para funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3245f7343a48d3e54795c14dcb23b836c8d9d988
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756433"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Aprovar ou negar solicitações para funções do Azure AD no Privileged Identity Management

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), você pode configurar funções para exigir aprovação para ativação e escolher um ou vários usuários ou grupos como aprovadores delegados. Os aprovadores delegados têm 24 horas para aprovar solicitações. Se uma solicitação não for aprovada dentro de 24 horas, o usuário elegível deverá enviar novamente uma nova solicitação. A janela de tempo de aprovação de 24 horas não é configurável.

Siga as etapas neste artigo para aprovar ou negar solicitações para funções do Azure AD.

## <a name="view-pending-requests"></a>Exibir solicitações pendentes

Como um Aprovador delegado, você receberá uma notificação por email quando uma solicitação de função do Azure AD estiver aguardando sua aprovação. Você pode exibir essas solicitações pendentes no Privileged Identity Management.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **funções do Azure ad**.

1. Clique em **aprovar solicitações**.

    ![Funções do Azure AD – aprovar solicitações](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Você verá uma lista de solicitações que aguardam sua aprovação.

## <a name="approve-requests"></a>Aprovar pedidos

1. Selecione as solicitações que você deseja aprovar e, em seguida, clique em **aprovar** para abrir o painel aprovar solicitações selecionadas.

    ![Aprovar a lista de solicitações com a opção aprovar realçada](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Na caixa **aprovar motivo** , digite um motivo.

    ![Aprovar o painel de solicitações selecionadas com um motivo de aprovação](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Clique em **aprovar**.

    O símbolo de status será atualizado com sua aprovação.

    ![Aprovar o painel de solicitações selecionadas após o botão aprovar clicado](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Negar solicitações

1. Selecione as solicitações que você deseja negar e clique em **negar** para abrir o painel negar solicitações selecionadas.

    ![Aprovar a lista de solicitações com a opção negar realçada](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Na caixa **motivo da negação** , digite um motivo.

    ![Negar o painel de solicitações selecionadas com um motivo de negação](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Clique em **negar**.

    O símbolo de status será atualizado com sua negação.

## <a name="next-steps"></a>Passos seguintes

- [Notificações por email no Privileged Identity Management](pim-email-notifications.md)
- [Aprovar ou negar solicitações para funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-approval-workflow.md)
