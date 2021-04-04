---
title: Descubra recursos Azure para gerir em PIM - Azure AD | Microsoft Docs
description: Saiba como descobrir os recursos da Azure para gerir na Azure AD Privileged Identity Management (PIM).
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
ms.date: 09/29/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d38990289169412f06b0c7e4bcbdf67f688da7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539014"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Descubra recursos da Azure para gerir em Gestão de Identidade Privilegiada

Utilizando o Azure Ative Directory (Azure AD) Privileged Identity Management (PIM), pode melhorar a proteção dos seus recursos Azure. Isto é útil para:

- Organizações que já utilizam Gestão de Identidade Privilegiada para proteger funções de AD AZure
- Grupo de gestão e proprietários de subscrições que estão a tentar garantir recursos de produção

Quando cria pela primeira vez a Gestão de Identidade Privilegiada para recursos Azure, precisa de descobrir e selecionar os recursos para proteger com Gestão de Identidade Privilegiada. Não há limite para o número de recursos que pode gerir com a Gestão de Identidade Privilegiada. No entanto, recomendamos começar com os seus recursos de produção mais críticos.

## <a name="discover-resources"></a>Detetar recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **recursos Azure**.

    Se esta é a sua primeira vez usando Gestão de Identidade Privilegiada para recursos Azure, você verá uma página **de recursos Discover.**

    ![Descubra o painel de recursos sem recursos listados para a primeira experiência](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Se outro administrador da sua organização já estiver a gerir os recursos da Azure na Gestão de Identidade Privilegiada, verá uma lista dos recursos que estão a ser geridos.

    ![Descubra o painel de recursos que está a ser gerido](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Selecione **Descubra recursos** para lançar a experiência de descoberta.

    ![O painel de descobertas lista recursos que podem ser geridos, como subscrições e grupos de gestão](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Na página **Discovery,** utilize o **filtro de estado de recurso** e **selecione** o tipo de recurso para filtrar os grupos de gestão ou subscrições a que tem permissão de escrita. É provavelmente mais fácil começar com **All** inicialmente.

   Pode pesquisar e selecionar recursos de gestão ou de subscrição para gerir em Gestão de Identidade Privilegiada. Quando gere um grupo de gestão ou uma subscrição na Gestão de Identidade Privilegiada, também pode gerir os seus recursos para crianças.

   > [!Note]
   > Quando adicionar um novo recurso Azure a um grupo de gestão gerido pela PIM, pode colocar o recurso da criança sob gestão procurando-o em PIM.

1. Selecione quaisquer recursos não geridos que queira gerir.

1. **Selecione Gerir o recurso** para começar a gerir os recursos selecionados.

    > [!NOTE]
    > Uma vez que um grupo de gestão ou subscrição é gerido, não pode ser gerido. Isto impede que outro administrador de recursos retire as definições de Gestão de Identidade Privilegiada.

    ![Painel de descoberta com um recurso selecionado e a opção de recurso Manage em destaque](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Se vir uma mensagem para confirmar o embarque do recurso selecionado para gestão, selecione **Sim**. A PIM será então configurada para gerir todos os novos e existentes objetos infantis sob os recursos.

    ![Mensagem confirmando a bordo os recursos selecionados para a gestão](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Passos seguintes

- [Configurar configurações de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções de recursos da Azure na Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
