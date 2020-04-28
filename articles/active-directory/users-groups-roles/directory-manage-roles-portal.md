---
title: Ver e atribuir permissões de funções de administrador - Azure AD / Microsoft Docs
description: Agora pode ver e gerir membros de um papel de administrador da AD Azure no portal. Para aqueles que frequentemente gerem tarefas de funções.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2815b496545ca5f920c00df7b2b5b7efe15c12a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900915"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Ver e atribuir funções de administrador no Diretório Ativo do Azure

Agora pode ver e gerir todos os membros das funções de administrador no portal azure Ative Diretório. Se gere frequentemente tarefas de papéis, provavelmente preferirá esta experiência. E se alguma vez te perguntaste "O que diabos estes papéis realmente fazem?", podes ver uma lista detalhada de permissões para cada um dos cargos de administrador da AD Azure.

## <a name="view-all-roles"></a>Ver todos os papéis

Procure e selecione **Azure Active Directory**. Selecione **Funções e administradores** para ver a lista de todas as funções disponíveis.

Clique na elipse à direita de cada linha para abrir a descrição detalhada do papel.

![lista de funções no portal Azure AD](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Ver os meus papéis

É fácil ver as suas próprias permissões também. Selecione **o seu papel** na página de **Funções e administradores** para ver as funções que estão atualmente atribuídas a si.

## <a name="view-assignments-for-a-role"></a>Ver atribuições para um papel

Clique numa função para visualizar os utilizadores atribuídos à função. Pode selecionar **Gerir em PIM** para obter capacidades de gestão adicionais. Os Administradores de Funções Privilegiadas podem alterar atribuições "Permanentes" (sempre ativas no papel) a "Elegíveis" (no papel apenas quando elevados). Se não tiver PIM, ainda pode selecionar **Manage in PIM** para se inscrever para um julgamento. A Gestão de Identidade Privilegiada requer um plano de [licença Azure AD Premium P2.](../privileged-identity-management/subscription-requirements.md)

![lista de membros de um papel de administrador](./media/directory-manage-roles-portal/member-list.png)

Se for um Administrador Global ou um Administrador de Papel Privilegiado, pode facilmente adicionar ou remover membros, filtrar a lista ou selecionar um membro para ver as suas funções ativas atribuídas.

## <a name="view-a-users-role-permissions"></a>Ver permissões de papel de um utilizador

Quando estiver a ver os membros de um papel, selecione **Descrição** para ver a lista completa de permissões concedidas pela atribuição do papel. A página inclui links para documentação relevante para ajudá-lo a guiá-lo através de funções de diretório de gestão.

![lista de permissões para um papel de administrador](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Passos seguintes

* Sinta-se livre para partilhar connosco no fórum de [funções administrativas da Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
* Para mais informações sobre funções e atribuição de funções de administrador, consulte [as funções de administrador de atribuição](directory-assign-admin-roles.md).
* Para obter permissões de utilizador predefinidas, consulte uma [comparação das permissões padrão dos hóspedes e dos utilizadores dos membros](../fundamentals/users-default-permissions.md).
