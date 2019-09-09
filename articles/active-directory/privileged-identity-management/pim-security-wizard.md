---
title: Assistente de segurança de funções do Azure AD no PIM-Azure Active Directory | Microsoft Docs
description: Descreve o assistente de segurança que você pode usar para converter atribuições de função com privilégios permanentes do Azure AD para qualificado usando Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d6d94df29ba16ecee06d70f5edac15a96a4299d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804007"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Assistente de segurança de funções do Azure AD no PIM

Se você for a primeira pessoa a executar Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para sua organização, você verá um assistente. O assistente ajuda você a entender os riscos de segurança de identidades com privilégios e a usar o PIM para reduzir esses riscos. Você não precisa fazer alterações nas atribuições de função existentes no assistente, se preferir fazer isso mais tarde.

## <a name="wizard-overview"></a>Visão geral do assistente

Antes que sua organização comece a usar o PIM, todas as atribuições de função são permanentes: os usuários sempre estarão nessas funções, mesmo que não precisem atualmente de seus privilégios. A primeira etapa do assistente mostra uma lista de funções com altos privilégios e quantos usuários estão atualmente nessas funções. Você pode analisar uma função específica para saber mais sobre os usuários se um ou mais deles não forem familiares.

A segunda etapa do assistente oferece a oportunidade de alterar as atribuições de função do administrador.  

> [!WARNING]
> É importante que você tenha pelo menos um administrador global e mais de um administrador de função com privilégios com uma conta organizacional (não um conta Microsoft). Se houver apenas um administrador de função com privilégios, a organização não será capaz de gerenciar o PIM se essa conta for excluída.
> Além disso, mantenha as atribuições de função permanentes se um usuário tiver um conta Microsoft (uma conta que eles usam para entrar nos serviços da Microsoft, como o Skype e o Outlook.com). Se você planeja exigir MFA para ativação para essa função, esse usuário será bloqueado.

## <a name="run-the-wizard"></a>Executar o assistente

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **funções do Azure ad** e clique em **Assistente**.

    ![Funções do Azure AD – página do assistente mostrando as 3 etapas para executar o assistente](./media/pim-security-wizard/wizard-start.png)

1. Clique em **1 descobrir funções privilegiadas**.

1. Examine a lista de funções com privilégios para ver quais usuários são permanentes ou qualificados.

    ![Descobrir funções privilegiadas – painel de funções mostrando membros permanentes e qualificados](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Clique em **Avançar** para selecionar os membros que você deseja tornar qualificados.

    ![Converter Membros em uma página qualificada com opções para selecionar membros que você deseja tornar qualificados para funções](./media/pim-security-wizard/convert-members-eligible.png)

1. Depois de selecionar os membros, clique em **Avançar**.

    ![Página examinar alterações mostrando Membros com atribuições de função permanentes que serão convertidas](./media/pim-security-wizard/review-changes.png)

1. Clique em **OK** para converter as atribuições permanentes para qualificado.

    Quando a conversão for concluída, você verá uma notificação.

    ![Notificação mostrando o status de uma conversão](./media/pim-security-wizard/notification-completion.png)

Se precisar converter outras atribuições de função com privilégios para qualificado, você poderá executar o assistente novamente. Se você quiser usar a interface PIM em vez do assistente, consulte [atribuir funções do Azure AD no PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Passos Seguintes

- [Atribuir funções do Azure AD no PIM](pim-how-to-add-role-to-user.md)
- [Conceder acesso a outros administradores para gerenciar o PIM](pim-how-to-give-access-to-pim.md)
