---
title: Atribuir funções de diretório para os utilizadores - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como atribuir funções de administrador e não-administrador aos utilizadores no Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd26fa53c91c53893c7f326afda5158fa430be1e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60248166"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Atribuir funções de administrador e não-administrador aos utilizadores no Azure Active Directory
Se um utilizador na sua organização necessita da permissão para gerir os recursos do Azure Active Directory (Azure AD), tem de atribuir ao utilizador uma função adequada no Azure AD, com base nas ações que o utilizador tem permissão para executar.

Para obter mais informações sobre as funções disponíveis, consulte [atribuir funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Para obter mais informações sobre como adicionar utilizadores, consulte [adicionar novos utilizadores ao Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Atribuir funções
Uma maneira comum de atribuir funções do Azure AD a um utilizador é sobre o **função de diretório** para um utilizador.

Também pode atribuir funções usando o Privileged Identity Management (PIM). Para obter mais informações sobre como utilizar o PIM, consulte [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Para atribuir uma função a um utilizador
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, procure e selecione o utilizador a obter a atribuição de função. Por exemplo, _Alain Charon_.

3. Sobre o **Alain Charon - perfil** página, selecione **função de diretório**.

    O **Alain Charon - função de diretório** é apresentada a página.

4. Selecione **Adicionar função**, selecione a função para atribuir a Alain (por exemplo, _administrador da aplicação_) e, em seguida, escolha **selecionar**.

    ![Página de funções de diretório, que mostra a função selecionada](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    A função de administrador da aplicação é atribuída a Alain Charon e é apresentado no **Alain Charon - função de diretório** página.

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função
Se precisar de remover a atribuição de função de um utilizador, também pode fazer a partir da **Alain Charon - função de diretório** página.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Para remover uma atribuição de função de um utilizador

1. Selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, procure e selecione o utilizador a obter a atribuição de função removida. Por exemplo, _Alain Charon_.

2. Selecione **função de diretório**, selecione **administrador da aplicação**e, em seguida, selecione **remover função**.

    ![Página de funções de diretório, que mostra a função selecionada e a opção Remover](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    A função de administrador do aplicativo é removida da Alain Charon e deixa de aparecer no **Alain Charon - função de diretório** página.

## <a name="next-steps"></a>Passos Seguintes
- [Adicionar ou eliminar utilizadores](add-users-azure-active-directory.md)

- [Adicionar ou alterar as informações de perfil](active-directory-users-profile-azure-portal.md)

- [Adicionar utilizadores convidados a partir de outro diretório](../b2b/what-is-b2b.md)

Ou pode realizar outras tarefas de gestão de utilizador, como a atribuição de delegados, através de políticas e partilhar contas de utilizador. Para obter mais informações sobre outras ações disponíveis, consulte [documentação de gestão de utilizador do Azure Active Directory](../users-groups-roles/index.yml).


