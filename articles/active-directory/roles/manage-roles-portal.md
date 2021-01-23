---
title: Ver e atribuir permissões de função de administrador - Azure AD | Microsoft Docs
description: Agora pode ver e gerir membros de um papel de administrador AD Azure no portal. Para aqueles que frequentemente gerem tarefas de papel.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26217930b79b958ae86d976d06a28ba4a4852ab6
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742034"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Ver e atribuir funções de administrador no Azure Active Directory

Agora pode ver e gerir todos os membros das funções de administrador no portal Azure Ative Directory. Se gerir frequentemente tarefas de funções, provavelmente preferirá esta experiência. E se alguma vez te perguntaste "O que diabos estes papéis realmente fazem?", podes ver uma lista detalhada de permissões para cada uma das funções de administrador da AD Azure.

## <a name="view-all-roles"></a>Ver todos os papéis

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **O Diretório Ativo Azure**.

1. Selecione **Funções e administradores** para ver a lista de todas as funções disponíveis.

1. Selecione a elipse à direita de cada linha para ver as permissões para o papel. Selecione uma função para visualizar os utilizadores designados para o papel. Se vir algo diferente da imagem a seguir, leia as atribuições note in [View para funções privilegiadas](#view-assignments-for-privileged-roles) para verificar se está na Gestão de Identidade Privilegiada (PIM).

    ![lista de papéis no portal AD AZure](./media/manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Ver os meus papéis

É fácil ver as suas próprias permissões também. Selecione **a sua função** na página **de funções e administradores** para ver as funções que lhe são atribuídas.

## <a name="view-assignments-for-privileged-roles"></a>Ver atribuições para funções privilegiadas

Pode selecionar **Gerir em PIM** para obter capacidades de gestão adicionais. Os Administradores privilegiados podem alterar atribuições "Permanentes" (sempre ativas no papel) para "Elegíveis" (no papel apenas quando elevados). Se não tiver Gestão de Identidade Privilegiada, ainda pode selecionar **Gerir em PIM** para se inscrever para um julgamento. A Gestão de Identidade Privilegiada requer um [plano de licença Azure AD Premium P2.](../privileged-identity-management/subscription-requirements.md)

![lista de membros de um papel de administração](./media/manage-roles-portal/member-list.png)

Se for administrador global ou administrador privilegiado, pode facilmente adicionar ou remover membros, filtrar a lista ou selecionar um membro para ver as suas funções atribuídas ativas.

> [!Note]
> Se tem uma licença P2 premium Azure E já utiliza Gestão de Identidade Privilegiada, todas as tarefas de gestão de funções são executadas na Gestão de Identidade de Privilégio e não na Azure AD.
>
> ![Funções AD AD geridas em PIM para utilizadores que já usam PIM e têm uma licença Premium P2](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>Ver permissões de função de um utilizador

Ao visualizar os membros de uma função, **selecione Descrição** para ver a lista completa de permissões concedidas pela atribuição de funções. A página inclui links para documentação relevante para ajudar a guiá-lo através de funções de diretório de gestão.

![Screenshot que mostra a página "Administrador Global - Descrição".](./media/manage-roles-portal/role-description.png)

## <a name="download-role-assignments"></a>Descarregue atribuições de funções

Para descarregar todas as atribuições para uma função específica, na página **Roles e administradores,** selecione uma função e, em seguida, selecione **descarregamento de atribuições de funções**. Um ficheiro CSV que lista atribuições em todos os âmbitos para essa função é descarregado.

![baixar todas as atribuições para um papel](./media/manage-roles-portal/download-role-assignments.png)

## <a name="assign-a-role"></a>Atribuir um papel

1. Inscreva-se no [portal Azure](https://portal.azure.com) com permissões de Administrador Global ou Administrador privilegiado e selecione **O Diretório Ativo Azure**.

1. Selecione **Funções e administradores** para ver a lista de todas as funções disponíveis.

1. Selecione um papel para ver as suas atribuições.

    ![Screenshot que mostra a página "User administrator - Assignments" com a ação "Gerir em PIM" selecionada.](./media/manage-roles-portal/member-list.png)

1. **Selecione Adicionar atribuições** e selecione as funções que pretende atribuir. Pode selecionar **Gerir em PIM** para obter capacidades de gestão adicionais. Se vir algo diferente da imagem a seguir, leia as atribuições note in [View para funções privilegiadas](#view-assignments-for-privileged-roles) para verificar se está em PIM.

    ![lista de permissões para um papel de administrador](./media/manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>Próximos passos

* Sinta-se livre para compartilhar conosco no fórum de [funções administrativas Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
* Para obter mais informações sobre funções e atribuição de funções de Administrador, consulte [as funções de administrador de atribuição](permissions-reference.md).
* Para obter permissões de utilizador predefinidos, consulte uma [comparação entre as permissões de utilizador e de hóspedes predefinidos](../fundamentals/users-default-permissions.md).
