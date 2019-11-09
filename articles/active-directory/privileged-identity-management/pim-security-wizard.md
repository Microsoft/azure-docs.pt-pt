---
title: Assistente de segurança de funções do Azure AD no PIM-Azure Active Directory | Microsoft Docs
description: Descreve o assistente de segurança que você pode usar para converter atribuições de função com privilégios permanentes do Azure AD para qualificado usando Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04bd0993873568ba7cce368ddd9277ed356b636c
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847036"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Assistente de segurança de funções do Azure AD no Privileged Identity Management

Se você for a primeira pessoa a usar Privileged Identity Management (PIM) em sua organização do Azure Active Directory (Azure AD), verá um assistente para começar. O assistente ajuda você a entender os riscos de segurança de identidades com privilégios e a usar Privileged Identity Management para reduzir esses riscos. Você não precisa fazer alterações nas atribuições de função existentes no assistente, se preferir fazer isso mais tarde.

## <a name="wizard-overview"></a>Visão geral do assistente

Antes que sua organização comece a usar Privileged Identity Management, todas as atribuições de função são permanentes: os usuários sempre estarão nessas funções, mesmo que não precisem atualmente de seus privilégios. A primeira etapa do assistente mostra uma lista de funções com altos privilégios e quantos usuários estão atualmente nessas funções. Você pode analisar uma função específica para saber mais sobre os usuários se um ou mais deles não forem familiares.

A segunda etapa do assistente oferece a oportunidade de alterar as atribuições de função do administrador.  

> [!WARNING]
> É importante que você tenha pelo menos um administrador global e mais de um administrador de função com privilégios com uma conta organizacional (não um conta Microsoft). Se houver apenas um administrador de função com privilégios, a organização não poderá gerenciar Privileged Identity Management se essa conta for excluída.
> Além disso, mantenha as atribuições de função permanentes se um usuário tiver um conta Microsoft (em outras palavras, uma conta que use para entrar nos serviços da Microsoft, como o Skype e o Outlook.com). Se você planeja exigir a autenticação multifator para ativação para essa função, esse usuário será bloqueado.

## <a name="run-the-wizard"></a>Executar o assistente

1. Iniciar sessão no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad** e, em seguida, selecione **Assistente**.

    ![Funções do Azure AD – página do assistente mostrando as 3 etapas para executar o assistente](./media/pim-security-wizard/wizard-start.png)

1. Selecione **1 descobrir funções com privilégios**.

1. Examine a lista de funções com privilégios para ver quais usuários são permanentes ou qualificados.

    ![Descobrir funções privilegiadas – painel de funções mostrando membros permanentes e qualificados](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Selecione **Avançar** para selecionar os usuários ou grupos que você deseja tornar qualificados.

    ![Converter Membros em uma página qualificada com opções para selecionar membros que você deseja tornar qualificados para funções](./media/pim-security-wizard/convert-members-eligible.png)

1. Depois de selecionar os usuários ou grupos, selecione **Avançar**.

    ![Página examinar alterações mostrando Membros com atribuições de função permanentes que serão convertidas](./media/pim-security-wizard/review-changes.png)

1. Selecione **OK** para converter as atribuições permanentes para qualificado.

    Quando a conversão for concluída, você verá uma notificação.

    ![Notificação mostrando o status de uma conversão](./media/pim-security-wizard/notification-completion.png)

Se precisar converter outras atribuições de função com privilégios para qualificado, você poderá executar o assistente novamente. Se você quiser usar a interface Privileged Identity Management em vez do assistente, consulte [atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Conceder acesso a outros administradores para gerenciar Privileged Identity Management](pim-how-to-give-access-to-pim.md)
