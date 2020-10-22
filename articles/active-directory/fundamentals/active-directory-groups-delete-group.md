---
title: Excluir um grupo - Azure Ative Directory / Microsoft Docs
description: Instruções sobre como eliminar um grupo utilizando o Azure Ative Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aed5f282ff7b5d573a6f8511f2fc4dbfd27135d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371822"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Excluir um grupo que usa o Azure Ative Directory
Pode eliminar um grupo Azure Ative Directory (Azure AD) por várias razões, mas normalmente será porque:

- Desacorda incorretamente o **tipo de Grupo** à opção errada.

- Criou o errado ou um grupo duplicado por engano. 

- Já não preciso do grupo.

## <a name="to-delete-a-group"></a>Para eliminar um grupo
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Azure Ative Directory**e, em seguida, selecione **Grupos**.

3. A partir da página **Grupos - Todos os grupos,** procure e selecione o grupo que pretende eliminar. Para estes passos, usaremos a política do **MDM - Leste**.

    ![Groups-All página de grupos, nome de grupo em destaque](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Na política do **MDM - Página de Visão Geral leste** e, em seguida, selecione **Delete**.

    O grupo é eliminado do seu inquilino Azure Ative Directory.

    ![Política do MDM - Página de Visão Geral do Oriente, excluir opção realçada](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Passos seguintes

- Se eliminar um grupo por engano, pode criá-lo novamente. Para obter mais informações, consulte [Como criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md).

- Se eliminar um grupo Microsoft 365 por engano, poderá ser capaz de restaurá-lo. Para obter mais informações, consulte [Restaurar um grupo do Office 365 eliminado](../enterprise-users/groups-restore-deleted.md).
