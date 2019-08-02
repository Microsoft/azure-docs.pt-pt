---
title: Eliminar um grupo - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como eliminar um grupo com o Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdba55e0655a13e65e403f5da73fcb69db5dbca5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561911"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Eliminar um grupo com o Azure Active Directory
Pode eliminar um grupo do Azure Active Directory (Azure AD) para qualquer número de motivos, mas normalmente é porque:

- Definir incorretamente o **tipo de grupo** para a opção errada.

- Foi criado um grupo errado ou duplicado por engano. 

- Não precisa mais do grupo.

## <a name="to-delete-a-group"></a>Para eliminar um grupo
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**e, em seguida, selecione **grupos**.

3. Partir do **grupos – todos os grupos** página, procure e selecione o grupo que pretende eliminar. Estes passos, vamos utilizar **política MDM - Leste**.

    ![Página de grupos de todos os grupos, nome do grupo realçado](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Sobre o **política MDM - descrição geral do Leste** página e, em seguida, selecione **eliminar**.

    O grupo é eliminado do seu inquilino do Azure Active Directory.

    ![Política MDM - página de descrição geral do leste, a opção de eliminação realçado](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Passos Seguintes

- Se eliminar um grupo por engano, pode criá-la novamente. Para obter mais informações, consulte [como criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md).

- Se eliminar um grupo do Office 365 por engano, poderá restaurá-lo. Para obter mais informações, consulte [restaurar um grupo do Office 365 eliminado](../users-groups-roles/groups-restore-deleted.md).
