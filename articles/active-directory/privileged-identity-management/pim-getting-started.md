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
ms.date: 04/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f8d41fb3e6233d319725cac924c0c0ed41b7ce4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234303"
---
# <a name="start-using-privileged-identity-management"></a>Começar a utilizar o Privileged Identity Management

Este artigo descreve como permitir a Gestão de Identidade Privilegiada (PIM) e começar a usá-lo.

Utilize a Gestão de Identidade Privilegiada (PIM) para gerir, controlar e monitorizar o acesso dentro da sua organização Azure Ative Directory (Azure AD). Com a PIM pode fornecer o acesso necessário e just-in-time aos recursos Do Azure, recursos AD Azure e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a Gestão de Identidade Privilegiada, deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Para mais informações, consulte [os requisitos de Licença para utilizar a Gestão de Identidade Privilegiada.](subscription-requirements.md)

## <a name="prepare-pim-for-azure-ad-roles"></a>Preparar PIM para funções de AD Azure

Uma vez que tenha ativado a Gestão de Identidade Privilegiada para o seu diretório, pode preparar a Gestão de Identidade Privilegiada para gerir funções de AD Azure.

Aqui estão as tarefas que recomendamos para que se prepare para funções de AD Azure, por ordem:

1. [Configure as definições de função ad](pim-how-to-change-default-settings.md).
1. [Dar atribuições elegíveis.](pim-how-to-add-role-to-user.md)
1. [Permitir que os utilizadores elegíveis ativem a sua função De AD Azure just-in-time](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>Preparar PIM para funções de Azure

Uma vez que tenha ativado a Gestão de Identidade Privilegiada para o seu diretório, pode preparar a Gestão de Identidade Privilegiada para gerir as funções azure para acesso a recursos Azure numa subscrição.

Aqui estão as tarefas que recomendamos para que se prepare para funções Azure, por ordem:

1. [Descubra os recursos do Azure](pim-resource-roles-discover-resources.md)
1. [Configure as definições de função Azure](pim-resource-roles-configure-role-settings.md).
1. [Dar atribuições elegíveis.](pim-resource-roles-assign-roles.md)
1. [Permitir que os utilizadores elegíveis ativem as suas funções Azure apenas no tempo](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>Navegue para as suas tarefas

Assim que a Gestão de Identidade Privilegiada estiver configurada, pode aprender o seu caminho.

![Janela de navegação na Gestão de Identidade Privilegiada mostrando Tarefas e Gerir opções](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tarefa + Gerir | Descrição |
| --- | --- |
| **Os meus papéis**  | Apresenta uma lista de funções elegíveis e ativas que lhe foram atribuídas. Este é o local onde pode ativar quaisquer funções elegíveis atribuídas. |
| **Os meus pedidos** | Exibe os seus pedidos pendentes para ativar atribuições de funções elegíveis. |
| **Aprovar pedidos** | Apresenta uma lista de pedidos para ativar funções elegíveis pelos utilizadores no seu diretório que está designado para aprovar. |
| **Rever acesso** | Lista as avaliações de acesso ativa que lhe são atribuídas para completar, quer esteja a rever o acesso para si ou para outra pessoa. |
| **Funções do Azure AD** | Apresenta um dashboard e configurações para administradores privilegiados para gerir atribuições de funções azure AD. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O painel de instrumentos My view apenas mostra informações sobre o utilizador que acede ao painel de instrumentos, e não a toda a organização. |
| **Recursos do Azure** | Apresenta um dashboard e configurações para administradores privilegiados para gerir atribuições de funções de recursos Azure. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O painel de instrumentos My view apenas mostra informações sobre o utilizador que acede ao painel de instrumentos, e não a toda a organização. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Adicione um azulejo PIM ao tablier

Para facilitar a abertura da Gestão de Identidade Privilegiada, adicione um azulejo PIM ao seu painel de instrumentos do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **todos os serviços** e encontre o serviço de Gestão de **Identidade Privilegiada azure AD.**

    ![Gestão de Identidade Privilegiada da Azure AD em todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Selecione o Quickstart de Gestão de Identidade Privilegiada.

1. Verifique **a lâmina Pin para o painel de instrumentos** para fixar a lâmina De Arranque de Identidade Privilegiada no painel de instrumentos.

    ![Ícone pushpin para fixar lâmina de gestão de identidade privilegiada para painel de instrumentos](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    No painel azure, você verá um azulejo como este:

    ![Gestão de identidade privilegiada Quickstart azulejo no painel de instrumentos](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Gerir o acesso a recursos azure na Gestão de Identidade Privilegiada](pim-resource-roles-discover-resources.md)
