---
title: Comece a usar PIM - Azure Ative Directory | Microsoft Docs
description: Saiba como ativar e começar a utilizar a Azure AD Privileged Identity Management (PIM) no portal Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: how-to
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bcfb21ab15355653780355f1b5e459bc806ec8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90600726"
---
# <a name="start-using-privileged-identity-management"></a>Começar a utilizar o Privileged Identity Management

Este artigo descreve como ativar a Gestão de Identidade Privilegiada (PIM) e começar a usá-la.

Utilize a Gestão de Identidade Privilegiada (PIM) para gerir, controlar e monitorizar o acesso dentro da sua organização Azure Ative Directory (Azure AD). Com o PIM pode fornecer o acesso necessário e just-in-time aos recursos Azure, recursos AD Azure e outros serviços online da Microsoft como o Microsoft 365 ou o Microsoft Intune.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a Gestão de Identidade Privilegiada, deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Para mais informações, consulte [os requisitos da Licença para utilizar a Gestão de Identidade Privilegiada.](subscription-requirements.md)

> [!Note]
> Quando um utilizador que esteja ativo num papel privilegiado numa organização AD Azure com uma licença Premium P2 vai para **Funções e administradores** em AD Azure e seleciona uma função (ou até mesmo apenas visita a Gestão de Identidade Privilegiada):
>
> - Ativamos automaticamente a PIM para a organização
> - A sua experiência é agora que podem atribuir uma atribuição de função "regular" ou uma atribuição de papel elegível
>
> Quando o PIM está ativado, não tem qualquer outro efeito na sua organização com o qual tenha de se preocupar. Ele dá-lhe opções de atribuição adicionais, tais como ative vs elegíveis com tempo de início e fim. O PIM também permite definir o âmbito para atribuições de funções utilizando Unidades Administrativas e funções personalizadas. Se você é um Administrador Global ou Administrador de Função Privilegiado, você pode começar a receber alguns e-mails adicionais como a digestão semanal PIM. Você também pode ver o principal do serviço MS-PIM no registo de auditoria relacionado com a atribuição de funções. Esta é uma mudança esperada que não deverá ter qualquer efeito no seu fluxo de trabalho.

## <a name="prepare-pim-for-azure-ad-roles"></a>Preparar PIM para funções AD Azure

Aqui estão as tarefas que recomendamos para preparar a Gestão de Identidade Privilegiada para gerir as funções de AD Azure:

1. [Configurar definições de funções Ad Azure](pim-how-to-change-default-settings.md).
1. [Dar atribuições elegíveis.](pim-how-to-add-role-to-user.md)
1. [Permitir que os utilizadores elegíveis ativem a sua função AZure AD just-in-time](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>Preparar PIM para funções de Azure

Aqui estão as tarefas que recomendamos para preparar a Gestão de Identidade Privilegiada para gerir as funções do Azure para uma subscrição:

1. [Descubra os recursos do Azure](pim-resource-roles-discover-resources.md)
1. [Configurar definições de funções Azure](pim-resource-roles-configure-role-settings.md).
1. [Dar atribuições elegíveis.](pim-resource-roles-assign-roles.md)
1. [Permitir que os utilizadores elegíveis ativem as suas funções Azure mesmo a tempo](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>Navegue para as suas tarefas

Assim que a Gestão de Identidade Privilegiada estiver configurada, poderá aprender o seu caminho.

![Janela de navegação em Gestão de Identidade Privilegiada mostrando tarefas e opções de gestão](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tarefa + Gerir | Description |
| --- | --- |
| **Os meus papéis**  | Apresenta uma lista de funções elegíveis e ativas atribuídas a si. Este é o local onde pode ativar quaisquer funções elegíveis atribuídas. |
| **Os meus pedidos** | Apresenta os seus pedidos pendentes para ativar atribuições de funções elegíveis. |
| **Aprovar pedidos** | Apresenta uma lista de pedidos para ativar funções elegíveis pelos utilizadores no seu diretório que está designado para aprovar. |
| **Rever acesso** | Lista comentários de acesso ativo que lhe são atribuídos, quer esteja a rever o acesso para si ou para outra pessoa. |
| **Funções do Azure AD** | Apresenta um dashboard e configurações para administradores de funções privilegiados para gerir atribuições de funções Azure. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O painel de instrumentos My View apenas apresenta informações sobre o utilizador que acede ao painel de instrumentos, e não toda a organização. |
| **Recursos do Azure** | Apresenta um dashboard e configurações para administradores de funções privilegiados para gerir atribuições de funções de recursos Azure. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O painel de instrumentos My View apenas apresenta informações sobre o utilizador que acede ao painel de instrumentos, e não toda a organização. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Adicione um azulejo PIM ao tablier

Para facilitar a abertura da Gestão de Identidade Privilegiada, adicione um azulejo PIM ao seu painel de instrumentos do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Todos os serviços** e encontre o serviço **de Gestão de Identidade Privilegiada Azure AD.**

    ![Gestão de Identidade Privilegiada Azure AD em todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Selecione o **arranque rápido** da Gestão de Identidade Privilegiada .

1. Selecione **a lâmina pin para o painel de instrumentos** para fixar a página de arranque **rápida** da Gestão de Identidade Privilegiada no painel de instrumentos.

    ![Ícone de pinpin para pin página de gestão de identidade privilegiada para painel de instrumentos](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    No painel Azure, você verá um azulejo como este:

    ![Gestão de Identidade Privilegiada Início rápido azulejo no tablier](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de Azure AD em Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [Gerir o acesso a recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-discover-resources.md)
