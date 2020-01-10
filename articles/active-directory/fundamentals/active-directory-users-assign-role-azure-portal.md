---
title: Atribuir funções de diretório para os utilizadores - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como atribuir funções de administrador e não-administrador aos utilizadores no Azure Active Directory.
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
ms.openlocfilehash: 2df52969ea79e5d1af132aa82c2ec1ceedb92b82
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422906"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Atribuir funções de administrador e não-administrador aos utilizadores no Azure Active Directory
Se um utilizador na sua organização necessita da permissão para gerir os recursos do Azure Active Directory (Azure AD), tem de atribuir ao utilizador uma função adequada no Azure AD, com base nas ações que o utilizador tem permissão para executar.

Para obter mais informações sobre as funções disponíveis, consulte [atribuir funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Para obter mais informações sobre como adicionar utilizadores, consulte [adicionar novos utilizadores ao Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Atribuir funções
Uma maneira comum de atribuir funções do Azure AD a um utilizador é sobre o **função de diretório** para um utilizador.

Também pode atribuir funções usando o Privileged Identity Management (PIM). Para obter mais informações sobre como utilizar o PIM, consulte [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Para atribuir uma função a um utilizador
1. Vá para o [portal do Azure](https://portal.azure.com/) e faça logon usando uma conta de administrador global para o diretório. 

2. Procure e selecione **Azure Active Directory**.

      ![portal do Azure Pesquisar Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Selecione **Utilizadores**.

4. Procure e selecione o usuário que está obtendo a atribuição de função. Por exemplo, _Alain Charon_.

      ![Página todos os usuários – selecionar o usuário](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na página **Alain Charon-Profile** , selecione **funções atribuídas**.

    O **Alain Charon - função de diretório** é apresentada a página.

6. Selecione **Adicionar atribuição**, selecione a função a ser atribuída a Alain (por exemplo, _administrador de aplicativos_) e escolha **selecionar**.

    ![Página funções atribuídas-mostrando a função selecionada](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    A função de administrador da aplicação é atribuída a Alain Charon e é apresentado no **Alain Charon - função de diretório** página.

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função
Se precisar de remover a atribuição de função de um utilizador, também pode fazer a partir da **Alain Charon - função de diretório** página.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Para remover uma atribuição de função de um utilizador

1. Selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, procure e selecione o utilizador a obter a atribuição de função removida. Por exemplo, _Alain Charon_.

2. Selecione **funções atribuídas**, selecione **administrador do aplicativo**e, em seguida, selecione **remover atribuição**.

    ![Página funções atribuídas, mostrando a função selecionada e a opção remover](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    A função de administrador do aplicativo é removida da Alain Charon e deixa de aparecer no **Alain Charon - função de diretório** página.

## <a name="next-steps"></a>Passos seguintes
- [Adicionar ou eliminar utilizadores](add-users-azure-active-directory.md)

- [Adicionar ou alterar as informações de perfil](active-directory-users-profile-azure-portal.md)

- [Adicionar utilizadores convidados a partir de outro diretório](../b2b/what-is-b2b.md)

Ou pode realizar outras tarefas de gestão de utilizador, como a atribuição de delegados, através de políticas e partilhar contas de utilizador. Para obter mais informações sobre outras ações disponíveis, consulte [documentação de gestão de utilizador do Azure Active Directory](../users-groups-roles/index.yml).


