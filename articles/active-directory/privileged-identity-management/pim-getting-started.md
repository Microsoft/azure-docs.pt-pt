---
title: Começar a utilizar o PIM - Azure | Documentos da Microsoft
description: Saiba como ativar e começar a utilizar o Azure AD Privileged Identity Management (PIM) no portal do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 11/09/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26e94c9b56ba65e3a7c51c1a85e97f7521377bae
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546862"
---
# <a name="start-using-pim"></a>Comece a utilizar o PIM

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), pode gerir, controlar e monitorizar o acesso dentro da sua organização. Este âmbito inclui o acesso ao Azure, ao Azure AD e a outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Este artigo descreve como ativar e começar a utilizar o PIM.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o PIM, tem de ter uma das seguintes licenças:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Para obter mais informações, consulte [os requisitos para utilizar o PIM de licença](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Primeira pessoa a utilizar o PIM

Se for a primeira pessoa a utilizar o PIM no seu diretório, é-lhe automaticamente atribuída a [administrador de segurança](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) e [administrador com função privilegiada](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) funções no diretório. As atribuições de funções de diretório do Azure AD dos utilizadores só podem ser geridas por administradores de funções com privilégios. Além disso, pode optar por executar o [Assistente de segurança](pim-security-wizard.md) que o orienta através da experiência de deteção e atribuição inicial.

## <a name="enable-pim"></a>Ativar PIM

Para começar a utilizar o PIM no seu diretório, primeiro tem de ativar o PIM.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) como um Administrador Global do seu diretório.

    Tem de ser um Administrador Global com uma conta institucional (por exemplo, @yourdomain.com), não uma conta Microsoft (por exemplo, @outlook.com), para ativar o PIM para um diretório.

1. Clique em **todos os serviços** e localize a **Azure AD Privileged Identity Management** serviço.

    ![O Azure AD Privileged Identity Management em todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Clique para abrir o guia de introdução do PIM.

1. Na lista, clique em **consentir PIM**.

    ![Consentir PIM](./media/pim-getting-started/consent-pim.png)

1. Clique em **verificar a minha identidade** para verificar a sua identidade com a MFA do Azure. Será solicitado a escolher uma conta.

    ![Selecione uma conta](./media/pim-getting-started/pick-account.png)

1. Se necessário para a verificação de obter mais informações, irá ser orientado ao longo do processo. Para obter mais informações, consulte [obter ajuda com verificação de dois passos](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Mais informações necessárias](./media/pim-getting-started/more-information-required.png)

    Por exemplo, poderá ser-lhe pedido para fornecer verificação por telefone.

    ![Verificação adicional de segurança](./media/pim-getting-started/additional-security-verification.png)

1. Depois de concluir o processo de verificação, clique nas **consentimento** botão.

1. Na mensagem que aparece, clique em **Sim** para dar consentimento ao serviço PIM.

    ![Dar consentimento a mensagem do PIM](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Inscreva o PIM para funções do Azure AD

Assim que tiver ativado o PIM para o seu diretório, terá de se inscrever no PIM para gerir funções do Azure AD.

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **funções do Azure AD**.

    ![Inscreva o PIM para funções do Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Clique em **Inscreva-se**.

1. Na mensagem que aparece, clique em **Sim** para se inscrever no PIM para gerir funções do Azure AD.

    ![Inscreva o PIM para mensagem de funções do Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Quando tiver concluído o início de sessão, as opções do Azure AD serão ativadas. Poderá ter de atualizar o portal.

    Para obter informações sobre como detetar e selecione os recursos do Azure para proteger com o PIM, consulte [recursos do Azure detetar para gerir no PIM](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Navegue para as suas tarefas

Depois do PIM ser configurado, pode realizar as tarefas de gestão de identidade.

![Tarefas de nível superior para PIM - captura de ecrã](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tarefas + Gerir | Descrição |
| --- | --- |
| **Minhas funções**  | Apresenta uma lista de funções elegíveis e ativas atribuídos ao utilizador. Este é o local onde pode ativar quaisquer funções elegíveis atribuídas. |
| **Os meus pedidos** | Apresenta os pedidos pendentes para ativar as atribuições de funções elegíveis. |
| **Aprovar pedidos** | Apresenta uma lista de pedidos de ativação de funções elegíveis pelos utilizadores no seu diretório que está a seu cargo para aprovar. |
| **Rever acesso** | Lista as revisões de acesso do Active Directory que está atribuído a concluir, se estiver a rever o acesso para si próprio ou por outra pessoa. |
| **Funções do Azure AD** | Apresenta um dashboard e as definições para os administradores de função com privilégios gerirem atribuições de função de diretório do Azure AD. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O dashboard A minha vista só apresenta informações sobre o utilizador que está a aceder ao dashboard, e não sobre o inquilino completo. |
| **Recursos do Azure** | Apresenta um dashboard e as definições para os administradores de função com privilégios gerirem atribuições de função de recursos do Azure. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O dashboard A minha vista só apresenta informações sobre o utilizador que está a aceder ao dashboard, e não sobre o inquilino completo. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Adicionar um mosaico PIM ao dashboard

Para facilitar abrir o PIM, deve adicionar um mosaico PIM ao dashboard do portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Clique em **todos os serviços** e localize a **Azure AD Privileged Identity Management** serviço.

    ![O Azure AD Privileged Identity Management em todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Clique para abrir o guia de introdução do PIM.

1. Verifique **afixar painel ao dashboard** para afixar o painel de início rápido do PIM ao dashboard.

    ![Afixar o painel ao dashboard](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    No dashboard do Azure, verá um mosaico como este:

    ![Mosaico de início rápido do PIM](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Passos Seguintes

- [Atribuir funções de diretório do Azure AD no PIM](pim-how-to-add-role-to-user.md)
- [Descubra os recursos do Azure para gerir no PIM](pim-resource-roles-discover-resources.md)
