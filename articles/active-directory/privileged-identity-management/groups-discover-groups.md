---
title: Gerir grupos atribuíveis como grupos privilegiados de acesso - Azure AD ! Microsoft Docs
description: Consentimento para grupos atribuíveis a bordo para gerir como grupos privilegiados de acesso em Gestão de Identidade Privilegiada (PIM).
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
ms.date: 08/17/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: a707c66f2562fe442c58ce6292e51c4e67a20ae2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869541"
---
# <a name="manage-privileged-access-groups-preview-in-privileged-identity-management"></a>Gerir grupos de acesso privilegiados (pré-visualização) em Gestão de Identidade Privilegiada

No Azure Ative Directory (Azure AD), pode atribuir funções integradas a Azure AD a grupos de nuvem para simplificar a forma como gere as atribuições de funções. Para proteger as funções de Azure AD e para garantir o acesso, pode agora utilizar a Gestão de Identidade Privilegiada (PIM) para gerir o acesso just-in-time aos membros ou proprietários destes grupos. Para gerir um grupo azure AD atribuível como um grupo privilegiado de acesso à Gestão de Identidade Privilegiada, você deve trazê-lo sob gestão na PIM.

## <a name="identify-groups-to-manage"></a>Identificar grupos para gerir

Pode criar um grupo atribuível a funções em Azure AD, conforme descrito na [Criação de um grupo atribuível a funções no Azure Ative Directory](../users-groups-roles/roles-groups-create-eligible.md). Tem de ser proprietário do grupo para o colocar sob gestão na Gestão de Identidade Privilegiada.

1. [Inscreva-se no Azure AD](https://aad.portal.azure.com) com permissões privilegiadas de administrador de funções.
1. Selecione **Grupos** e, em seguida, selecione o grupo atribuível a funções que pretende gerir. Pode pesquisar ou filtrar a lista.

    ![encontrar um grupo atribuível a funções para gerir em PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Abra o grupo e selecione **acesso privilegiado (Pré-visualização)**.

    ![Abra a experiência de Gestão de Identidade Privilegiada](./media/groups-discover-groups/groups-discover-groups.png)

1. Se os seus grupos ainda não foram submetidos a gestão em PIM, selecione **Ative o acesso privilegiado** ao consentimento para a gestão. Apenas o Administrador Global ou Proprietário de um grupo pode dar este consentimento.

    ![consentimento para gerir o grupo em Gestão de Identidade Privilegiada, se necessário](./media/groups-discover-groups/consent-page.png)

1. Comece a gerir tarefas na PIM.

    ![Gerir atribuições em Gestão de Identidade Privilegiada](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Uma vez que um grupo de acesso privilegiado é gerido, não pode ser retirado da gestão. Isto impede outro administrador de remover as definições de Gestão de Identidade Privilegiada.

## <a name="next-steps"></a>Passos seguintes

- [Configurar atribuições de grupos privilegiados de acesso em Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Atribuir grupos privilegiados de acesso em Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
