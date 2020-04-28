---
title: Descubra os recursos do Azure para gerir na PIM - Azure AD [ Microsoft Docs
description: Saiba como descobrir os recursos do Azure para gerir na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87da43100c7494937ddc842e0f903ba3a360959e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74022895"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Descubra os recursos do Azure para gerir na Gestão de Identidade Privilegiada

Utilizando o Azure Ative Directory (Azure AD) Privileged Identity Management (PIM), pode melhorar a proteção dos seus recursos Azure. Isto é útil para organizações que já utilizam a Gestão de Identidade Privilegiada para proteger as funções da Azure AD, e para os proprietários de grupos de gestão e subscrições que procuram garantir recursos de produção.

Quando cria a Privilegiada Gestão de Identidade para os recursos Do Azure, precisa de descobrir e selecionar os recursos para proteger com a Gestão de Identidade Privilegiada. Não há limite para o número de recursos que pode gerir com a Gestão de Identidade Privilegiada. No entanto, recomendamos começar pelos seus recursos mais críticos (de produção).

## <a name="discover-resources"></a>Detetar recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **recursos Azure**.

    Se esta for a sua primeira vez usando a Privilegiada Gestão de Identidade para recursos Azure, você verá uma página de **recursos Discover.**

    ![Descubra o painel de recursos sem recursos listados para a primeira experiência](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Se outro administrador da sua organização já estiver a gerir os recursos do Azure na Gestão de Identidade Privilegiada, verá uma lista dos recursos que estão a ser geridos.

    ![Descubra recursos que estão a ser geridos](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Selecione **descobrir recursos** para lançar a experiência de descoberta.

    ![Discovery pane listing recursos que podem ser geridos como subscrições e grupos de gestão](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Na página **Discovery,** utilize o **filtro do Estado do Recurso** e **selecione o tipo** de recurso para filtrar os grupos de gestão ou subscrições a que tem permissão de escrita. É provavelmente mais fácil começar com **tudo** inicialmente.

    Só pode pesquisar e selecionar recursos de gestão ou subscrição para gerir usando a Gestão de Identidade Privilegiada. Ao gerir um grupo de gestão ou uma subscrição em Gestão de Identidade Privilegiada, também pode gerir os seus recursos infantis.

1. Selecione a caixa de verificação ao lado de quaisquer recursos não geridos que pretenda gerir.

1. Selecione **Gerir o recurso** para começar a gerir os recursos selecionados.

    > [!NOTE]
    > Uma vez gerido um grupo de gestão ou subscrição, não pode ser desgerido. Isto impede que outro administrador de recursos remova as definições de Gestão de Identidade Privilegiada.

    ![Discovery pane com um recurso selecionado e a opção gerir recursos em destaque](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Se vir uma mensagem para confirmar o embarque do recurso selecionado para gestão, selecione **Sim**.

    ![Mensagem confirmando a bordo os recursos selecionados para a gestão](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Passos seguintes

- [Configure definições de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções de recurso Azure na Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
