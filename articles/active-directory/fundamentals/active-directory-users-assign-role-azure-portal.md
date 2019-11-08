---
title: Atribuir funções de diretório a usuários-Azure Active Directory | Microsoft Docs
description: Instruções sobre como atribuir funções de administrador e não administrador a usuários com Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d071ff071c13637b15479d86a699d0b368119196
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73742569"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Atribuir funções de administrador e não administrador a usuários com Azure Active Directory
Se um usuário em sua organização precisar de permissão para gerenciar os recursos do Azure Active Directory (AD do Azure), você deverá atribuir ao usuário uma função apropriada no Azure AD, com base nas ações que o usuário precisará de permissão para executar.

Para obter mais informações sobre as funções disponíveis, consulte [atribuindo funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Para obter mais informações sobre como adicionar usuários, consulte [Adicionar novos usuários a Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Atribuir funções
Uma maneira comum de atribuir funções do Azure AD a um usuário está na página **função de diretório** de um usuário.

Você também pode atribuir funções usando o Privileged Identity Management (PIM). Para obter informações mais detalhadas sobre como usar o PIM, consulte [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Para atribuir uma função a um usuário
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **Azure Active Directory**, selecione **usuários**e, em seguida, pesquise e selecione o usuário que obtém a atribuição de função. Por exemplo, _Alain Charon_.

      ![Página todos os usuários – selecionar o usuário](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

3. Na página **Alain Charon-Profile** , selecione **funções atribuídas**.

    A página da **função Alain Charon-Directory** é exibida.

4. Selecione **Adicionar atribuição**, selecione a função a ser atribuída a Alain (por exemplo, _administrador de aplicativos_) e escolha **selecionar**.

    ![Página funções atribuídas-mostrando a função selecionada](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    A função Administrador de aplicativos é atribuída a Alain Charon e aparece na página de **função Alain Charon-Directory** .

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função
Se você precisar remover a atribuição de função de um usuário, também poderá fazer isso na página de **função Alain Charon-Directory** .

### <a name="to-remove-a-role-assignment-from-a-user"></a>Para remover uma atribuição de função de um usuário

1. Selecione **Azure Active Directory**, selecione **usuários**e, em seguida, pesquise e selecione o usuário que está obtendo a atribuição de função removida. Por exemplo, _Alain Charon_.

2. Selecione **funções atribuídas**, selecione **administrador do aplicativo**e, em seguida, selecione **remover atribuição**.

    ![Página funções atribuídas, mostrando a função selecionada e a opção remover](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    A função Administrador de aplicativos é removida de Alain Charon e não aparece mais na página de **função Alain Charon-Directory** .

## <a name="next-steps"></a>Passos seguintes
- [Adicionar ou excluir usuários](add-users-azure-active-directory.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Adicionar utilizadores convidados a partir de outro diretório](../b2b/what-is-b2b.md)

Ou você pode executar outras tarefas de gerenciamento de usuário, como a atribuição de delegados, o uso de políticas e o compartilhamento de contas de usuário. Para obter mais informações sobre outras ações disponíveis, consulte [Azure Active Directory documentação de gerenciamento de usuário](../users-groups-roles/index.yml).


