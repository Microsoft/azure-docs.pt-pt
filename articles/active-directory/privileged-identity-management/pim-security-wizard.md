---
title: Azure AD participa Descoberta e insights (pré-visualização) em Gestão de Identidade Privilegiada ex-Assistente de Segurança - Diretório Ativo Azure
description: A descoberta e os insights (anteriormente Assistente de Segurança) ajudam-no a converter atribuições permanentes de funções Azure AD para missões just-in-time com Gestão de Identidade Privilegiada.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/01/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff5d15ef66b597fdf56fefe90f35cbf122bb093f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534442"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Discovery and Insights (pré-visualização) para os papéis de Azure AD (anteriormente Assistente de Segurança)

Se estiver a começar com a Gestão de Identidade Privilegiada (PIM) na sua organização Azure Ative Directory (Azure AD), pode utilizar a página **Discovery e insights (pré-visualização)** para começar. Esta funcionalidade mostra-lhe quem está designado para funções privilegiadas na sua organização e como usar o PIM para alterar rapidamente atribuições de funções permanentes em atribuições just-in-time. Pode visualizar ou fazer alterações nas suas atribuições de funções privilegiadas permanentes em **Discovery and Insights (pré-visualização)**. É uma ferramenta de análise e uma ferramenta de ação.

## <a name="discovery-and-insights-preview"></a>Descoberta e insights (pré-visualização)

Antes da sua organização começar a usar Gestão de Identidade Privilegiada, todas as atribuições de funções são permanentes. Os utilizadores estão sempre nas suas funções atribuídas mesmo quando não precisam dos seus privilégios. A descoberta e insights (pré-visualização), que substitui o antigo Assistente de Segurança, mostra-lhe uma lista de funções privilegiadas e quantos utilizadores estão atualmente nessas funções. Pode listar atribuições para uma função para saber mais sobre os utilizadores designados se um ou mais deles não forem familiares.

:heavy_check_mark: A Microsoft recomenda que mantenha **duas** contas de vidro de rutura que são permanentemente atribuídas ao papel de administrador global. Certifique-se de que estas contas não requerem o mesmo mecanismo de autenticação de vários fatores que as suas contas administrativas normais para iniciar sedutação, conforme descrito em [Gerir contas de acesso de emergência em Azure AD](../users-groups-roles/directory-emergency-access.md).

Além disso, mantenha as atribuições de funções permanentes se um utilizador tiver uma conta Microsoft (por outras palavras, uma conta que utilize para iniciar sessão em serviços da Microsoft como o Skype, ou Outlook.com). Se necessitar de autenticação multi-factor para um utilizador com uma conta Microsoft para ativar uma atribuição de funções, o utilizador será bloqueado.

## <a name="open-discovery-and-insights-preview"></a>Open Discovery e insights (pré-visualização)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **as funções AD do Azure** e, em seguida, selecione **Discovery and insights (Preview)**. A abertura da página inicia-se o processo de descoberta para encontrar atribuições de funções relevantes.

    ![Funções AD AD - Página de descobertas e insights mostrando as 3 opções](./media/pim-security-wizard/new-preview-link.png)

1. **Selecione Reduzir os administradores globais**.

    ![Screenshot que mostra a ação "Discovery and insights (Preview)" com a ação "Reduzir os administradores globais" selecionada.](./media/pim-security-wizard/new-preview-page.png)

1. Reveja a lista de atribuições de funções de Administrador Global.

    ![Reduzir os administradores globais - Painel de funções mostrando todos os Administradores Globais](./media/pim-security-wizard/new-global-administrator-list.png)

1. Selecione **Seguinte** para selecionar os utilizadores ou grupos que pretende tornar elegíveis e, em seguida, selecione Fazer **a atribuição** **Deselegível** ou Remover .

    ![Converter membros em página elegível com opções para selecionar membros que pretende tornar elegíveis para funções](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. Também pode exigir que todos os administradores globais revejam o seu próprio acesso.

    ![Página de administradores globais mostrando seção de comentários de acesso](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. Depois de selecionar qualquer uma destas alterações, verá uma notificação do Azure.

1. Pode então selecionar Eliminar os principais do serviço **de acesso permanente** ou **de Revisão** para repetir os passos acima sobre outras funções privilegiadas e sobre atribuições de funções principais de serviço. Para atribuições de funções principais de serviço, só pode remover atribuições de funções.

    ![Opções adicionais de Insights para eliminar os principais dos serviços de acesso e revisão permanentes ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de Azure AD em Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Conceder acesso a outros administradores para gerir a Gestão de Identidade Privilegiada](pim-how-to-give-access-to-pim.md)
