---
title: Azure AD roles assistente de segurança em PIM - Azure Ative Directory [ Azure Ative Directory ] Microsoft Docs
description: Descreve o assistente de segurança que pode usar para converter atribuições de funções azure privilegiadas permanentes para elegível usando a Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/21/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9f12b2b31da4c7fe67eef9674d96b517d4e2bfa
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867755"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Azure AD funções assistente de segurança na Gestão de Identidade Privilegiada

Se é a primeira pessoa a utilizar a Privileged Identity Management (PIM) na sua organização Azure Ative Directory (Azure AD),é-lhe apresentado um assistente para começar. O assistente ajuda-o a compreender os riscos de segurança de identidades privilegiadas e como usar a Gestão de Identidade Privilegiada para reduzir esses riscos. Não precisa de fazer alterações nas atribuições de papéis existentes no assistente, se preferir fazê-lo mais tarde.

> [!Important]
> O assistente de segurança está temporariamente indisponível. Obrigado pela paciência.

## <a name="wizard-overview"></a>Visão geral do feiticeiro

Antes de a sua organização começar a utilizar a Gestão de Identidade Privilegiada, todas as atribuições de funções são permanentes: os utilizadores estão sempre nestas funções mesmo que não necessitem atualmente dos seus privilégios. O primeiro passo do assistente mostra-lhe uma lista de papéis privilegiados e quantos utilizadores estão atualmente nessas funções. Pode perfurar um papel particular para saber mais sobre os utilizadores se um ou mais deles não estiverem familiarizados.

O segundo passo do assistente dá-lhe a oportunidade de alterar as atribuições de funções do administrador.  

> [!WARNING]
> É importante que tenha pelo menos um administrador global, e mais de um administrador privilegiado com uma conta de trabalho ou escola (não uma conta Microsoft). Se houver apenas um administrador privilegiado, a organização não pode gerir a Gestão de Identidade Privilegiada se essa conta for eliminada.
> Além disso, mantenha as atribuições de funções permanentes se um utilizador tiver uma conta Microsoft (por outras palavras, uma conta que usam para iniciar sessão em serviços da Microsoft como o Skype e Outlook.com). Se pretender exigir a autenticação de vários fatores para a ativação dessa função, esse utilizador ficará bloqueado.

## <a name="run-the-wizard"></a>Executar o assistente

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções AD Azure** e, em seguida, selecione **Wizard**.

    ![Funções Azure AD - Página do feiticeiro mostrando os 3 passos para executar o assistente](./media/pim-security-wizard/wizard-start.png)

1. Selecione **1 Descubra funções privilegiadas**.

1. Reveja a lista de funções privilegiadas para ver quais os utilizadores permanentes ou elegíveis.

    ![Descubra papéis privilegiados - Painel de papéis mostrando membros permanentes e elegíveis](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Selecione **Next** para selecionar os utilizadores ou grupos que pretende tornar elegíveis.

    ![Converta os membros em página elegível com opções para selecionar membros que pretende tornar elegíveis para funções](./media/pim-security-wizard/convert-members-eligible.png)

1. Depois de ter selecionado os utilizadores ou grupos, selecione **Next**.

    ![Rever altera página mostrando membros com atribuições de funções permanentes que serão convertidos](./media/pim-security-wizard/review-changes.png)

1. Selecione **OK** para converter as atribuições permanentes em elegíveis.

    Quando a conversão estiver concluída, verá uma notificação.

    ![Notificação que mostre o estado de uma conversão](./media/pim-security-wizard/notification-completion.png)

Se precisar converter outras atribuições privilegiadas para elegível, pode voltar a dirigir o assistente. Se pretender utilizar a interface privilegiada de Gestão de Identidade em vez do assistente, consulte [as funções de AD De Atribuição Azure na Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Conceder acesso a outros administradores para gerir a Gestão de Identidade Privilegiada](pim-how-to-give-access-to-pim.md)
