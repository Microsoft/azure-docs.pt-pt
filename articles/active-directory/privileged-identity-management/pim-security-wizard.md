---
title: Azure AD funções assistente de segurança em PIM - Azure Ative Directory / Microsoft Docs
description: Descreve o assistente de segurança que pode usar para converter atribuições de funções Azure AD privilegiadas permanentes para elegíveis usando Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 04/21/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cc7aed1cc79a8c08a7ff11382a1c7a51455d5c3
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84743665"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Azure AD funções assistente de segurança na Gestão de Identidade Privilegiada

Se for a primeira pessoa a utilizar a Gestão de Identidade Privilegiada (PIM) na sua organização Azure Ative Directory (Azure AD), é-lhe apresentado um assistente para começar. O assistente ajuda-o a compreender os riscos de segurança de identidades privilegiadas e como usar a Gestão de Identidade Privilegiada para reduzir esses riscos. Não precisa de fazer alterações nas atribuições de funções existentes no assistente, se preferir fazê-lo mais tarde.

> [!Important]
> O assistente de segurança está temporariamente indisponível. Obrigado pela paciência.

## <a name="wizard-overview"></a>Visão geral do assistente

Antes da sua organização começar a utilizar a Gestão de Identidade Privilegiada, todas as atribuições de funções são permanentes: os utilizadores estão sempre nestas funções, mesmo que não precisem atualmente dos seus privilégios. O primeiro passo do assistente mostra-lhe uma lista de papéis privilegiados e quantos utilizadores estão atualmente nessas funções. Você pode perfurar para um papel particular para aprender mais sobre os utilizadores se um ou mais deles não são familiares.

O segundo passo do assistente dá-lhe a oportunidade de alterar as atribuições de funções do administrador.  

> [!WARNING]
> É importante que tenha pelo menos um administrador Global, e mais de um administrador privilegiado com uma conta de trabalho ou escola (não uma conta Microsoft). Se houver apenas um administrador privilegiado, a organização não pode gerir a Gestão de Identidade Privilegiada se essa conta for eliminada.
> Além disso, mantenha as atribuições de funções permanentes se um utilizador tiver uma conta Microsoft (por outras palavras, uma conta que utilize para iniciar sessão em serviços da Microsoft como o Skype e Outlook.com). Se pretender exigir uma autenticação multi-factor para ativação para essa função, esse utilizador ficará bloqueado.

## <a name="run-the-wizard"></a>Executar o assistente

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **as funções AD do Azure** e, em seguida, selecione **Wizard**.

    ![Funções AD AD - Página de assistente mostrando os 3 passos para executar o assistente](./media/pim-security-wizard/wizard-start.png)

1. Selecione **1 Descubra funções privilegiadas.**

1. Reveja a lista de funções privilegiadas para ver quais os utilizadores permanentes ou elegíveis.

    ![Descubra papéis privilegiados - Painel de papéis mostrando membros permanentes e elegíveis](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Selecione **Seguinte** para selecionar os utilizadores ou grupos que pretende tornar elegíveis.

    ![Converter membros em página elegível com opções para selecionar membros que pretende tornar elegíveis para funções](./media/pim-security-wizard/convert-members-eligible.png)

1. Depois de selecionar os utilizadores ou grupos, selecione **Next**.

    ![Rever página de alterações mostrando membros com atribuições de funções permanentes que serão convertidos](./media/pim-security-wizard/review-changes.png)

1. Selecione **OK** para converter as atribuições permanentes para elegíveis.

    Quando a conversão terminar, verá uma notificação.

    ![Notificação que mostra o estado de uma conversão](./media/pim-security-wizard/notification-completion.png)

Se precisar de converter outras atribuições de funções privilegiadas para elegíveis, pode voltar a executar o assistente. Se pretender utilizar a interface de Gestão de Identidade Privilegiada em vez do assistente, consulte [as funções de AD de Atribuição em Gestão de Identidade Privilegiada.](pim-how-to-add-role-to-user.md)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de Azure AD em Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Conceder acesso a outros administradores para gerir a Gestão de Identidade Privilegiada](pim-how-to-give-access-to-pim.md)
