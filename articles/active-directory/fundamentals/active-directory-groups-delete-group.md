---
title: Eliminar um grupo - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: Instruções sobre como eliminar um grupo utilizando o Diretório Ativo Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561911"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Eliminar um grupo usando o Diretório Ativo Azure
Pode eliminar um grupo de Diretórios Ativos Azure (Azure AD) por várias razões, mas normalmente será porque:

- Coloque incorretamente o **tipo de Grupo** na opção errada.

- Criou o grupo errado ou duplicado por engano. 

- Já não preciso do grupo.

## <a name="to-delete-a-group"></a>Para eliminar um grupo
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Diretório Ativo Azure**e, em seguida, selecione **Grupos**.

3. A partir da página **Grupos - Todos os grupos,** procure e selecione o grupo que pretende eliminar. Para estes passos, usaremos a política do **MDM - Leste.**

    ![Página de grupos-todos os grupos, nome de grupo destacado](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Na **política** do MDM - página de visão geral do Oriente e, em seguida, selecione **Delete**.

    O grupo é eliminado do seu inquilino azure Ative Directory.

    ![Política do MDM - Página de visão geral do Leste, excluir opção destacada](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Passos seguintes

- Se apagar um grupo por engano, pode criá-lo novamente. Para mais informações, consulte [Como criar um grupo básico e adicionar membros.](active-directory-groups-create-azure-portal.md)

- Se apagar um grupo do Office 365 por engano, poderá restaurá-lo. Para mais informações, consulte [Restaurar um grupo eliminado do Office 365](../users-groups-roles/groups-restore-deleted.md).
