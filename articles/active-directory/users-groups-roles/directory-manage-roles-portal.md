---
title: Ver e atribuir permissões de função de administrador - Azure AD / Microsoft Docs
description: Agora pode ver e gerir membros de um papel de administrador AD Azure no portal. Para aqueles que frequentemente gerem tarefas de papel.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9bfc7f194730545469e7d17d46c47f7293f016e
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84728899"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Ver e atribuir funções de administrador no Azure Ative Directory

Agora pode ver e gerir todos os membros das funções de administrador no portal Azure Ative Directory. Se gerir frequentemente tarefas de funções, provavelmente preferirá esta experiência. E se alguma vez te perguntaste "O que diabos estes papéis realmente fazem?", podes ver uma lista detalhada de permissões para cada uma das funções de administrador da AD Azure.

## <a name="view-all-roles"></a>Ver todos os papéis

Procure e selecione **Azure Active Directory**. Selecione **Funções e administradores** para ver a lista de todas as funções disponíveis.

Clique na elipse à direita de cada linha para abrir a descrição detalhada do papel.

![lista de papéis no portal AD AZure](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Ver os meus papéis

É fácil ver as suas próprias permissões também. Selecione **a sua função** na página **de funções e administradores** para ver as funções que lhe são atribuídas.

## <a name="view-assignments-for-a-role"></a>Ver atribuições para um papel

Clique numa função para ver os utilizadores designados para o papel. Pode selecionar **Gerir em PIM** para obter capacidades de gestão adicionais. Os Administradores privilegiados podem alterar atribuições "Permanentes" (sempre ativas no papel) para "Elegíveis" (no papel apenas quando elevados). Se não tiver PIM, ainda pode selecionar **Gerir em PIM** para se inscrever para um julgamento. A Gestão de Identidade Privilegiada requer um [plano de licença Azure AD Premium P2.](../privileged-identity-management/subscription-requirements.md)

![lista de membros de um papel de administração](./media/directory-manage-roles-portal/member-list.png)

Se for administrador global ou administrador privilegiado, pode facilmente adicionar ou remover membros, filtrar a lista ou selecionar um membro para ver as suas funções atribuídas ativas.

## <a name="view-a-users-role-permissions"></a>Ver permissões de função de um utilizador

Ao visualizar os membros de uma função, **selecione Descrição** para ver a lista completa de permissões concedidas pela atribuição de funções. A página inclui links para documentação relevante para ajudar a guiá-lo através de funções de diretório de gestão.

![lista de permissões para um papel de administrador](./media/directory-manage-roles-portal/role-description.png)

## <a name="next-steps"></a>Passos seguintes

* Sinta-se livre para compartilhar conosco no fórum de [funções administrativas Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
* Para obter mais informações sobre funções e atribuição de funções de Administrador, consulte [as funções de administrador de atribuição](directory-assign-admin-roles.md).
* Para obter permissões de utilizador predefinidos, consulte uma [comparação entre as permissões de utilizador e de hóspedes predefinidos](../fundamentals/users-default-permissions.md).
