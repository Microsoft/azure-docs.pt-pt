---
title: Descubra grupos atribuíveis para gerir em PIM - Azure AD ! Microsoft Docs
description: Saiba como descobrir grupos atribuíveis por funções para gerir como grupos privilegiados de acesso em Gestão de Identidade Privilegiada (PIM).
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
ms.date: 07/27/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b66a41f0f1aa48433dac77fca56d5e2877989a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506007"
---
# <a name="discover-privileged-access-groups-preview-to-manage-in-privileged-identity-management"></a>Descubra grupos privilegiados de acesso (pré-visualização) para gerir em Gestão de Identidade Privilegiada

No Azure Ative Directory (Azure AD), pode atribuir funções integradas a Azure AD a grupos de nuvem para simplificar a gestão de atribuições de funções. Agora pode utilizar a Gestão de Identidade Privilegiada (PIM) para atribuir a elegibilidade para a adesão ou propriedade destes grupos, para proteger as funções de AZure AD e para garantir o acesso. Antes de poder gerir um grupo atribuível a Azure AD como um grupo privilegiado de acesso em Gestão de Identidade Privilegiada, deve descobri-lo e trazê-lo sob gestão na PIM.

## <a name="discover-resources"></a>Detetar recursos

1. [Inscreva-se no Azure AD](https://aad.portal.azure.com) com permissões de função de Administrador de Função Privilegiada.
1. Criar um grupo atribuível a papéis em Azure AD. Deve ser proprietário do grupo para descobri-lo e geri-lo com Gestão de Identidade Privilegiada.
1. **Gestão de Identidade Privilegiada Aberta.**
1. Selecione **acesso privilegiado (pré-visualização)**.

    ![Descubra o comando de grupos pela primeira vez](./media/groups-discover-groups/groups-discover-groups.png)

1. Selecione **Grupos De descobrir**.
1. Procure por nome de grupo.
1. Selecione o seu grupo e **selecione Gerir grupos** para o colocar sob a gestão pim.

    ![Descubra grupos sem recursos listados para a primeira experiência](./media/groups-discover-groups/groups-bring-under-management.png)

    > [!NOTE]
    > Uma vez que um grupo de acesso privilegiado é gerido, não pode ser retirado da gestão. Isto impede que outro administrador de recursos retire as definições de Gestão de Identidade Privilegiada.

1. Se vir uma mensagem para confirmar o embarque do recurso selecionado para gestão, selecione **Sim**.

## <a name="next-steps"></a>Passos seguintes

- [Configurar atribuições de grupos privilegiados de acesso em Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Atribuir grupos privilegiados de acesso em Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
