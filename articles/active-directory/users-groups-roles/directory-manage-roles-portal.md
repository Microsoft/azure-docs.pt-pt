---
title: Exibir e atribuir permissões de função de administrador – Azure AD | Microsoft Docs
description: Agora você pode ver e gerenciar membros de uma função de administrador do Azure AD no Portal. Para aqueles que freqüentemente gerenciam atribuições de função.
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
ms.openlocfilehash: 7ac62a4f318690a1e8156d619c82e9b936973159
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027889"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Exibir e atribuir funções de administrador no Azure Active Directory

Agora você pode ver e gerenciar todos os membros das funções de administrador no portal de Azure Active Directory. Se você gerenciar com frequência as atribuições de função, provavelmente preferirá essa experiência. E se você já se perguntou "o que realmente faz essas funções?", você pode ver uma lista detalhada de permissões para cada uma das funções de administrador do Azure AD.

## <a name="view-all-roles"></a>Exibir todas as funções

Em Azure Active Directory, selecione **funções e administradores** para ver a lista de todas as funções disponíveis. 

Clique nas reticências à direita de cada linha para abrir a descrição detalhada da função.

![lista de funções no portal do Azure AD](./media/directory-manage-roles-portal/role-list.png)

## <a name="view-my-roles"></a>Exibir minhas funções

Também é fácil exibir suas próprias permissões. Selecione **sua função** na página **funções e administradores** para ver as funções que estão atribuídas a você no momento.

## <a name="view-assignments-for-a-role"></a>Exibir atribuições para uma função

Clique em uma função para exibir os usuários atribuídos à função. Você pode selecionar **gerenciar no PIM** para recursos de gerenciamento adicionais. Os administradores de função com privilégios podem alterar as atribuições "permanentes" (sempre ativas na função) para "qualificado" (na função somente quando elevado). Se você não tiver o PIM, ainda poderá selecionar **gerenciar no PIM** para se inscrever em uma avaliação. Privileged Identity Management requer um [plano de licença do Azure ad Premium P2](../privileged-identity-management/subscription-requirements.md).

![lista de membros de uma função de administrador](./media/directory-manage-roles-portal/member-list.png)

Se você for um administrador global ou um administrador de função com privilégios, poderá facilmente adicionar ou remover membros, filtrar a lista ou selecionar um membro para ver suas funções atribuídas ativas.

## <a name="view-a-users-role-permissions"></a>Exibir as permissões de função de um usuário

Quando você estiver exibindo os membros de uma função, selecione **Descrição** para ver a lista completa de permissões concedidas pela atribuição de função. A página inclui links para a documentação relevante para orientá-lo no gerenciamento de funções de diretório.

![lista de permissões para uma função de administrador](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Passos seguintes

* Fique à vontade para compartilhar conosco no [Fórum de funções administrativas do Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Para obter mais informações sobre funções e atribuição de função de administrador, consulte [atribuir funções de administrador](directory-assign-admin-roles.md).
* Para permissões de usuário padrão, consulte uma [comparação das permissões padrão de usuário convidado e membro](../fundamentals/users-default-permissions.md).
