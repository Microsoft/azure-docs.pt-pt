---
title: Comece a utilizar PIM - Diretório Ativo Azure  ) Microsoft Docs
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
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 733743f4680f3197a1754d87b4672641b2321907
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399332"
---
# <a name="start-using-privileged-identity-management"></a>Começar a utilizar o Privileged Identity Management

Com a Privileged Identity Management (PIM), pode gerir, controlar e monitorizar o acesso dentro da sua organização Azure Ative Directory (Azure AD). Este âmbito inclui acesso a recursos Azure, Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Este artigo descreve como ativar e começar a usar a Gestão de Identidade Privilegiada.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a Gestão de Identidade Privilegiada, deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Mobilidade Empresarial + Segurança (EMS) E5

Para mais informações, consulte [os requisitos de Licença para utilizar a Gestão de Identidade Privilegiada.](subscription-requirements.md)

## <a name="first-person-to-use-pim"></a>Primeira pessoa a usar PIM

Se for a primeira pessoa a utilizar a Gestão de Identidade Privilegiada no seu diretório, é-lhe atribuído automaticamente as funções de Administrador de [Segurança](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) e Administrador de [Funções Privilegiadas](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) no diretório. Apenas administradores privilegiados podem gerir atribuições de funções de AD Azure dos utilizadores. Além disso, pode optar por executar o assistente de [segurança](pim-security-wizard.md) que o acompanha através da experiência inicial de descoberta e atribuição.

## <a name="enable-pim"></a>Ativar pim

Para começar a utilizar a Gestão de Identidade Privilegiada no seu diretório, tem primeiro de ativar a Gestão de Identidade Privilegiada.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como Administrador Global do seu diretório.

    Deve ser um Administrador Global com uma conta organizacional (por exemplo, @yourdomain.com), não uma conta Microsoft (por exemplo, @outlook.com), para permitir a Gestão de Identidade Privilegiada para um diretório.

1. Clique em **todos os serviços** e encontre o serviço de Gestão de **Identidade Privilegiada azure AD.**

    ![Gestão de Identidade Privilegiada da Azure AD em todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Clique para abrir o Quickstart de Gestão de Identidade Privilegiada.

1. Na lista, clique **em Consentimento para PIM**.

    ![Consentimento para Gestão de Identidade Privilegiada para permitir Gestão de Identidade Privilegiada](./media/pim-getting-started/consent-pim.png)

1. Clique em Verificar a **minha identidade** para verificar a sua identidade com o Azure MFA. Pedir-lhe-á para escolher uma conta.

    ![Escolha uma janela de conta para verificar a sua identidade](./media/pim-getting-started/pick-account.png)

1. Se for necessária mais informação para verificação, será guiado através do processo. Para mais informações, consulte Procure ajuda com a [verificação em duas etapas.](../user-help/multi-factor-authentication-end-user-troubleshoot.md)

    ![Mais informações requerem janela se a sua organização precisar de mais informações](./media/pim-getting-started/more-information-required.png)

    Por exemplo, pode ser-lhe pedido que forneça verificação do telefone.

    ![Página adicional de verificação de segurança perguntando como contatá-lo](./media/pim-getting-started/additional-security-verification.png)

1. Depois de concluído o processo de verificação, clique no botão **Consentimento.**

1. Na mensagem que aparece, clique **em Sim** para consentir com o serviço de Gestão de Identidade Privilegiada.

    ![Consentimento para mensagem privilegiada de Gestão de Identidade para completar processo de consentimento](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Inscreva-se pim para funções de AD Azure

Uma vez que tenha ativado a Gestão de Identidade Privilegiada para o seu diretório, terá de inscrever a Privileged Identity Management para gerir as funções de AD Azure.

1. Open **Azure AD Privileged Identity Management.**

1. Clique em **funções de AD Azure**.

    ![Inscreva-se na Gestão de Identidade Privilegiada para funções de AD Azure](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Clique **em Inscrever-se**.

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
| **Rever o acesso** | Lista as avaliações de acesso ativa que lhe são atribuídas para completar, quer esteja a rever o acesso para si ou para outra pessoa. |
| **Funções da AD Azure** | Apresenta um dashboard e configurações para administradores de funções privilegiadas para gerir atribuições de funções azure AD. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O dashboard A minha vista só apresenta informações sobre o utilizador que está a aceder ao dashboard, e não sobre o inquilino completo. |
| **Recursos do Azure** | Apresenta um dashboard e configurações para administradores de funções privilegiadas para gerir atribuições de funções de recursos Azure. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O dashboard A minha vista só apresenta informações sobre o utilizador que está a aceder ao dashboard, e não sobre o inquilino completo. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Adicione um azulejo PIM ao tablier

Para facilitar a abertura da Gestão de Identidade Privilegiada, deverá adicionar um azulejo privilegiado de Gestão de Identidade ao seu portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Clique em **todos os serviços** e encontre o serviço de Gestão de **Identidade Privilegiada azure AD.**

    ![Gestão de Identidade Privilegiada da Azure AD em todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Clique para abrir o Quickstart de Gestão de Identidade Privilegiada.

1. Verifique **a lâmina Pin para o painel de instrumentos** para fixar a lâmina De Arranque de Identidade Privilegiada no painel de instrumentos.

    ![Ícone pushpin para fixar lâmina de gestão de identidade privilegiada para painel de instrumentos](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    No painel azure, você verá um azulejo como este:

    ![Gestão de identidade privilegiada Quickstart azulejo no painel de instrumentos](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Descubra os recursos do Azure para gerir na Gestão de Identidade Privilegiada](pim-resource-roles-discover-resources.md)
