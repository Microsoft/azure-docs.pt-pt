---
title: Atribuir funções Azure AD aos utilizadores - Azure Ative Directory
description: Saiba como conceder acesso aos utilizadores no Azure Ative Directory atribuindo funções Azure AD.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ced586db1b4e417e623431c137c43dac8ba56f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466649"
---
# <a name="assign-azure-ad-roles-to-users"></a>Atribuir funções AZURE AD aos utilizadores

Agora pode ver e gerir todos os membros das funções de administrador no centro de administração Azure AD. Se gerir frequentemente tarefas de funções, provavelmente preferirá esta experiência. Este artigo descreve como atribuir funções Azure AD usando o centro de administração Azure AD.

## <a name="assign-a-role"></a>Atribuir um papel

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com permissões de Administrador Global ou Administrador privilegiado.

1. Selecione **Azure Active Directory**.

1. Selecione **Funções e administradores** para ver a lista de todas as funções disponíveis.

    ![Screenshot da página Roles e administradores](./media/manage-roles-portal/roles-and-administrators.png)

1. Selecione um papel para ver as suas atribuições.

    Para ajudá-lo a encontrar o papel de que precisa, o Azure AD pode mostrar-lhe subconjuntos das funções baseadas em categorias de papéis. Confira o filtro **Tipo** para lhe mostrar apenas as funções do tipo selecionado.

1. **Selecione Adicionar atribuições** e, em seguida, selecione os utilizadores que pretende atribuir a esta função.

    Se vir algo diferente da imagem a seguir, leia a Nota em [Gestão de Identidade Privilegiada (PIM)](#privileged-identity-management-pim) para verificar se está a utilizar o PIM.

    ![lista de permissões para um papel de administrador](./media/manage-roles-portal/add-assignments.png)

1. **Selecione Adicionar** para atribuir a função.

## <a name="privileged-identity-management-pim"></a>Gestão de Identidade Privilegiada (PIM)

Pode selecionar **Gerir em PIM** para obter capacidades de gestão adicionais utilizando [a Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Os Administradores privilegiados podem alterar atribuições "Permanentes" (sempre ativas no papel) para "Elegíveis" (no papel apenas quando elevados). Se não tiver Gestão de Identidade Privilegiada, ainda pode selecionar **Gerir em PIM** para se inscrever para um julgamento. A Gestão de Identidade Privilegiada requer um [plano de licença Azure AD Premium P2.](../privileged-identity-management/subscription-requirements.md)

![Screenshot que mostra a página "User administrator - Assignments" com a ação "Gerir em PIM" selecionada](./media/manage-roles-portal/member-list-pim.png)

Se for administrador global ou administrador privilegiado, pode facilmente adicionar ou remover membros, filtrar a lista ou selecionar um membro para ver as suas funções atribuídas ativas.

> [!Note]
> Se tem uma licença P2 premium Azure E já utiliza Gestão de Identidade Privilegiada, todas as tarefas de gestão de funções são executadas na Gestão de Identidade de Privilégio e não na Azure AD.
>
> ![Funções AD AD geridas em PIM para utilizadores que já usam PIM e têm uma licença Premium P2](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>Passos seguintes

* Sinta-se livre para compartilhar conosco no fórum de [funções administrativas Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
* Para mais sobre papéis, consulte [as funções incorporadas da Azure AD.](permissions-reference.md)
* Para obter permissões de utilizador predefinidos, consulte uma [comparação entre as permissões de utilizador e de hóspedes predefinidos](../fundamentals/users-default-permissions.md).
