---
title: Comece a utilizar PIM - Diretório Ativo Azure [ ) Microsoft Docs
description: Saiba como ativar e começar a usar a Azure AD Privileged Identity Management (PIM) no portal Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472865"
---
# <a name="start-using-privileged-identity-management"></a>Começar a utilizar o Privileged Identity Management

Com a Privileged Identity Management (PIM), pode gerir, controlar e monitorizar o acesso dentro da sua organização Azure Ative Directory (Azure AD). Este âmbito inclui acesso a recursos Azure, Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Este artigo descreve como ativar e começar a usar a Gestão de Identidade Privilegiada.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a Gestão de Identidade Privilegiada, deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Para mais informações, consulte [os requisitos de Licença para utilizar a Gestão de Identidade Privilegiada.](subscription-requirements.md)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Inscreva-se pim para funções de AD Azure

Uma vez que tenha ativado a Gestão de Identidade Privilegiada para o seu diretório, terá de inscrever a Privileged Identity Management para gerir as funções de AD Azure.

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **funções Azure AD**.

    ![Inscreva-se na Gestão de Identidade Privilegiada para funções de AD Azure](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Selecione **Inscrever-se**.

1. Na mensagem que aparece, clique em **Sim** para inscrever a Privilegiada Gestão de Identidade para gerir as funções da Azure AD.

    ![Inscreva-se na mensagem de Gestão de Identidade Privilegiada para a mensagem de funções da Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Quando a inscrição estiver concluída, as opções de AD Azure serão ativadas. Talvez precise refrescar o portal.

    Para obter informações sobre como descobrir e selecionar os recursos Azure para proteger com gestão de identidade privilegiada, consulte [os recursos do Discover Azure para gerir na Gestão](pim-resource-roles-discover-resources.md)de Identidade Privilegiada.

## <a name="navigate-to-your-tasks"></a>Navegue para as suas tarefas

Uma vez criada a Gestão de Identidade Privilegiada, pode iniciar as suas tarefas de gestão de identidade.

![Janela de navegação na Gestão de Identidade Privilegiada mostrando Tarefas e Gerir opções](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tarefa + Gerir | Descrição |
| --- | --- |
| **Os meus papéis**  | Apresenta uma lista de funções elegíveis e ativas que lhe foram atribuídas. Este é o local onde pode ativar quaisquer funções elegíveis atribuídas. |
| **Os meus pedidos** | Exibe os seus pedidos pendentes para ativar atribuições de funções elegíveis. |
| **Aprovar pedidos** | Apresenta uma lista de pedidos para ativar funções elegíveis pelos utilizadores no seu diretório que está designado para aprovar. |
| **Rever acesso** | Lista as avaliações de acesso ativa que lhe são atribuídas para completar, quer esteja a rever o acesso para si ou para outra pessoa. |
| **Funções do Azure AD** | Apresenta um dashboard e configurações para administradores de funções privilegiadas para gerir atribuições de funções azure AD. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O dashboard A minha vista só apresenta informações sobre o utilizador que está a aceder ao dashboard, e não sobre o inquilino completo. |
| **Recursos do Azure** | Apresenta um dashboard e configurações para administradores de funções privilegiadas para gerir atribuições de funções de recursos Azure. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O dashboard A minha vista só apresenta informações sobre o utilizador que está a aceder ao dashboard, e não sobre o inquilino completo. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Adicione um azulejo PIM ao tablier

Para facilitar a abertura da Gestão de Identidade Privilegiada, adicione um azulejo privilegiado de Gestão de Identidade ao seu portal Azure dashboard.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Selecione **todos os serviços** e encontre o serviço de Gestão de **Identidade Privilegiada azure AD.**

    ![Gestão de Identidade Privilegiada da Azure AD em todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Selecione o Quickstart de Gestão de Identidade Privilegiada.

1. Verifique **a lâmina Pin para o painel de instrumentos** para fixar a lâmina De Arranque de Identidade Privilegiada no painel de instrumentos.

    ![Ícone pushpin para fixar lâmina de gestão de identidade privilegiada para painel de instrumentos](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    No painel azure, você verá um azulejo como este:

    ![Gestão de identidade privilegiada Quickstart azulejo no painel de instrumentos](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Descubra os recursos do Azure para gerir na Gestão de Identidade Privilegiada](pim-resource-roles-discover-resources.md)
