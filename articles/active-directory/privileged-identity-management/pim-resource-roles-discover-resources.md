---
title: Descubra os recursos do Azure para gerir no PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como detetar recursos do Azure para gerir no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 786b5ed99cce29c7217bfa42446821848ca8e05f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60288194"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Descubra os recursos do Azure para gerir no PIM

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), pode melhorar a proteção dos seus recursos do Azure. Isso é útil para as organizações que já utilizam o PIM para proteger as funções do Azure AD e proprietários de subscrição e grupo de gestão que estão procurando para proteger os recursos de produção.

Quando configura primeiro a PIM para recursos do Azure, tem de detetar e selecione os recursos para proteger com o PIM. Não existe nenhum limite para o número de recursos que podem ser geridos com o PIM. No entanto, recomendamos começar com os seus recursos (produção) mais críticos.

## <a name="discover-resources"></a>Detetar recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **recursos do Azure**.

    Se esta for a primeira vez que utilizar o PIM para recursos do Azure, verá um painel de recursos de descoberta.

    ![Detetar recursos - pela primeira vez](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Se outro administrador de recursos ou o diretório da sua organização já está a gerir os recursos do Azure no PIM, verá uma lista dos recursos que estão atualmente a ser geridos.

    ![Detetar o painel de recursos](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Clique em **detetar recursos** para iniciar a experiência de deteção.

    ![Painel de deteção](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. No painel de deteção, utilize **filtro de estado de recurso** e **selecione o tipo de recurso** para filtrar a gestão de grupos ou subscrições que tem permissão para escrever. Ela provavelmente é mais fácil para começar **todos os** inicialmente.

    Só é possível procurar e selecione recursos de subscrição ou grupo de gestão para gerir com o PIM. Quando gerir um grupo de gestão ou de uma subscrição no PIM, também pode gerir os recursos subordinados.

1. Adicione uma marca de verificação junto a quaisquer recursos não gerenciados que pretende gerir.

1. Clique em **gerir recurso** para começar a gerir os recursos selecionados.

    > [!NOTE]
    > Assim que uma subscrição ou grupo de gestão é definida gerida, não pode ser gerido. Isto impede o outro administrador de recursos e remover as definições do PIM.

    ![Deteção - gerir recursos](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Se vir uma mensagem para confirmar a inclusão do recurso selecionado para a gestão, clique em **Sim**.

    ![Deteção - gerir a mensagem de recurso](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Passos Seguintes

- [Configurar as definições de função de recursos do Azure no PIM](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções de recursos do Azure no PIM](pim-resource-roles-assign-roles.md)
