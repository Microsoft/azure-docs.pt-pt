---
title: Começar a usar o PIM-Azure Active Directory | Microsoft Docs
description: Saiba como habilitar e começar a usar o Azure AD Privileged Identity Management (PIM) no portal do Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 018cc54a770a0d18fa9af3d93fec2f5f720ce17b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804525"
---
# <a name="start-using-pim"></a>Comece a utilizar o PIM

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), você pode gerenciar, controlar e monitorar o acesso em sua organização. Este âmbito inclui o acesso ao Azure, ao Azure AD e a outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Este artigo descreve como habilitar e começar a usar o PIM.

## <a name="prerequisites"></a>Pré-requisitos

Para usar o PIM, você deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) e5

Para obter mais informações, consulte [requisitos de licença para usar o PIM](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Primeira pessoa a usar o PIM

Se você for a primeira pessoa a usar o PIM em seu diretório, você receberá automaticamente as funções [administrador de segurança](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) e administrador de [função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) no diretório. Somente administradores de função com privilégios podem gerenciar atribuições de função de usuários do Azure AD. Além disso, você pode optar por executar o [Assistente de segurança](pim-security-wizard.md) que o orientará pela experiência inicial de descoberta e atribuição.

## <a name="enable-pim"></a>Habilitar PIM

Para começar a usar o PIM em seu diretório, primeiro você deve habilitar o PIM.

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do seu diretório.

    Você deve ser um administrador global com uma conta organizacional (por exemplo, @yourdomain.com), não um conta Microsoft (por exemplo, @outlook.com) para habilitar o PIM para um diretório.

1. Clique em **todos os serviços** e localize o serviço **Azure ad Privileged Identity Management** .

    ![Azure AD Privileged Identity Management em todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Clique para abrir o início rápido do PIM.

1. Na lista, clique em **consentimento para PIM**.

    ![Consentimento do PIM para habilitar o PIM](./media/pim-getting-started/consent-pim.png)

1. Clique em **verificar minha identidade** para verificar sua identidade com o Azure MFA. Você será solicitado a escolher uma conta.

    ![Escolha uma janela de conta para verificar sua identidade](./media/pim-getting-started/pick-account.png)

1. Se mais informações forem necessárias para verificação, você será guiado pelo processo. Para obter mais informações, consulte [obter ajuda com a verificação em duas etapas](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Mais informações sobre a janela necessária se sua organização precisar de mais informações](./media/pim-getting-started/more-information-required.png)

    Por exemplo, você pode ser solicitado a fornecer verificação por telefone.

    ![Página de verificação de segurança adicional perguntando como entrar em contato com você](./media/pim-getting-started/additional-security-verification.png)

1. Depois de concluir o processo de verificação, clique no botão de **consentimento** .

1. Na mensagem que aparece, clique em **Sim** para dar consentimento ao serviço PIM.

    ![Consentimento da mensagem do PIM para concluir o processo de consentimento](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Inscreva-se no PIM para funções do Azure AD

Depois de habilitar o PIM para seu diretório, você precisará inscrever-se no PIM para gerenciar funções do Azure AD.

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **funções do Azure ad**.

    ![Inscreva-se no PIM para funções do Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Clique em **inscrever-se**.

1. Na mensagem que aparece, clique em **Sim** para inscrever o PIM para gerenciar funções do Azure AD.

    ![Mensagem de inscrição do PIM para funções do Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Quando a inscrição for concluída, as opções do AD do Azure serão habilitadas. Talvez seja necessário atualizar o Portal.

    Para obter informações sobre como descobrir e selecionar os recursos do Azure para proteger com o PIM, consulte [descobrir recursos do Azure para gerenciar no PIM](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Navegue para as suas tarefas

Depois que o PIM é configurado, você pode executar suas tarefas de gerenciamento de identidade.

![Janela de navegação no PIM mostrando as opções tarefas e gerenciar](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tarefa + gerenciar | Descrição |
| --- | --- |
| **Minhas funções**  | Exibe uma lista de funções qualificadas e ativas atribuídas a você. Este é o local onde pode ativar quaisquer funções elegíveis atribuídas. |
| **Minhas solicitações** | Exibe suas solicitações pendentes para ativar as atribuições de função qualificadas. |
| **Aprovar pedidos** | Exibe uma lista de solicitações para ativar funções qualificadas por usuários em seu diretório que você está designado para aprovar. |
| **Examinar o acesso** | Lista as revisões de acesso ativas que você está atribuído a concluir, se você está revisando o acesso para si mesmo ou outra pessoa. |
| **Funções do Azure AD** | Exibe um painel e configurações para administradores de função com privilégios para gerenciar atribuições de função do Azure AD. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O dashboard A minha vista só apresenta informações sobre o utilizador que está a aceder ao dashboard, e não sobre o inquilino completo. |
| **Recursos do Azure** | Exibe um painel e configurações para administradores de função com privilégios para gerenciar atribuições de função de recurso do Azure. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O dashboard A minha vista só apresenta informações sobre o utilizador que está a aceder ao dashboard, e não sobre o inquilino completo. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Adicionar um bloco do PIM ao painel

Para facilitar a abertura do PIM, você deve adicionar um bloco do PIM ao seu painel de portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Clique em **todos os serviços** e localize o serviço **Azure ad Privileged Identity Management** .

    ![Azure AD Privileged Identity Management em todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Clique para abrir o início rápido do PIM.

1. Marque a **folha fixar no painel** para fixar a folha início rápido do PIM no painel.

    ![Ícone de pino para fixar folha PIM no painel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    No painel do Azure, você verá um bloco como este:

    ![Bloco de início rápido do PIM no painel](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Passos Seguintes

- [Atribuir funções do Azure AD no PIM](pim-how-to-add-role-to-user.md)
- [Descobrir recursos do Azure para gerenciar no PIM](pim-resource-roles-discover-resources.md)
