---
title: Identificar um grupo para gerir em Gestão de Identidade Privilegiada - Azure AD | Microsoft Docs
description: Saiba como embarcar grupos atribuíveis para gerir como grupos privilegiados de acesso em Gestão de Identidade Privilegiada (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/03/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c9e2580d2a88fbbab755f0c3df2f923bdc45548
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688353"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>Trazer grupos de acesso privilegiados (pré-visualização) para a Gestão de Identidade Privilegiada

No Azure Ative Directory (Azure AD), pode atribuir funções integradas a Azure AD a grupos de nuvem para simplificar a forma como gere as atribuições de funções. Para proteger as funções de Azure AD e para garantir o acesso, pode agora utilizar a Gestão de Identidade Privilegiada (PIM) para gerir o acesso just-in-time aos membros ou proprietários destes grupos. Para gerir um grupo azure AD atribuível como um grupo privilegiado de acesso à Gestão de Identidade Privilegiada, você deve trazê-lo sob gestão na PIM.

## <a name="identify-groups-to-manage"></a>Identificar grupos para gerir

Pode criar um grupo atribuível a funções em Azure AD, conforme descrito na [Criação de um grupo atribuível a funções no Azure Ative Directory](../roles/groups-create-eligible.md). Tem de ser proprietário do grupo para o colocar sob gestão com Gestão de Identidade Privilegiada.

1. [Inscreva-se no Azure AD](https://aad.portal.azure.com) com permissões de função de Administrador de Função Privilegiada.
1. Selecione **Grupos** e, em seguida, selecione o grupo atribuível a funções que pretende gerir em PIM. Pode pesquisar e filtrar a lista.

    ![encontrar um grupo atribuível a funções para gerir em PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Abra o grupo e selecione **acesso privilegiado (Pré-visualização)**.

    ![Abra a experiência de Gestão de Identidade Privilegiada](./media/groups-discover-groups/groups-discover-groups.png)

1. Comece a gerir tarefas na PIM.

    ![Gerir atribuições em Gestão de Identidade Privilegiada](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Uma vez que um grupo de acesso privilegiado é gerido, não pode ser retirado da gestão. Isto impede que outro administrador de recursos retire as definições de Gestão de Identidade Privilegiada.
>

> [!IMPORTANT]
> Se um grupo de acesso privilegiado for eliminado do Azure Ative Directory, pode demorar até 24 horas para que o grupo seja removido da lâmina dos grupos de acesso privilegiados (Preview). 
>


## <a name="next-steps"></a>Passos seguintes

- [Configurar atribuições de grupos privilegiados de acesso em Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Atribuir grupos privilegiados de acesso em Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
