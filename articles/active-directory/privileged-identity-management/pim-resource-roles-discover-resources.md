---
title: Descobrir recursos do Azure para gerenciar em PIM-Azure Active Directory | Microsoft Docs
description: Saiba como descobrir recursos do Azure para gerenciar no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1f1712ab7474747829c8744a39e74b78777668
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804176"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Descobrir recursos do Azure para gerenciar no PIM

Usando o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), você pode melhorar a proteção dos recursos do Azure. Isso é útil para organizações que já usam o PIM para proteger as funções do Azure AD e para os proprietários do grupo de gerenciamento e da assinatura que estão procurando proteger os recursos de produção.

Ao configurar o PIM para recursos do Azure pela primeira vez, você precisa descobrir e selecionar os recursos a serem protegidos com o PIM. Não há limite para o número de recursos que você pode gerenciar com o PIM. No entanto, é recomendável começar com seus recursos mais críticos (de produção).

## <a name="discover-resources"></a>Detetar recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **recursos do Azure**.

    Se esta for a primeira vez que você usa o PIM para recursos do Azure, você verá um painel descobrir recursos.

    ![Descobrir o painel de recursos sem recursos listados para a primeira experiência](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Se outro administrador de recursos ou de diretório em sua organização já estiver gerenciando recursos do Azure no PIM, você verá uma lista dos recursos que estão sendo gerenciados no momento.

    ![Painel descobrir recursos listando recursos que estão sendo gerenciados no momento](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Clique em **descobrir recursos** para iniciar a experiência de descoberta.

    ![Painel de descoberta listando recursos que podem ser gerenciados, como assinaturas e grupos de gerenciamento](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. No painel descoberta, use **filtro de estado do recurso** e **selecione tipo de recurso** para filtrar os grupos de gerenciamento ou assinaturas para os quais você tem permissão de gravação. Provavelmente é mais fácil começar com **tudo** inicialmente.

    Você só pode procurar e selecionar recursos de assinatura ou grupo de gerenciamento para gerenciar usando o PIM. Ao gerenciar um grupo de gerenciamento ou uma assinatura no PIM, você também pode gerenciar seus recursos filho.

1. Adicione uma marca de seleção ao lado de quaisquer recursos não gerenciados que você deseja gerenciar.

1. Clique em **gerenciar recurso** para começar a gerenciar os recursos selecionados.

    > [!NOTE]
    > Depois que um grupo de gerenciamento ou uma assinatura é definido como gerenciado, ele não pode ser não gerenciado. Isso impede que outro administrador de recursos remova as configurações de PIM.

    ![Painel de descoberta com um recurso selecionado e a opção de gerenciar recurso realçada](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Se você vir uma mensagem para confirmar a integração do recurso selecionado para gerenciamento, clique em **Sim**.

    ![Mensagem confirmando a integração dos recursos selecionados para gerenciamento](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Passos seguintes

- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções de recurso do Azure no PIM](pim-resource-roles-assign-roles.md)
