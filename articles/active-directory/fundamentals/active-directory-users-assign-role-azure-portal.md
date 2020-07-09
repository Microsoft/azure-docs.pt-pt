---
title: Atribuir funções de diretório aos utilizadores - Azure Ative Directory Microsoft Docs
description: Instruções sobre como atribuir funções de administrador e não administrador aos utilizadores com o Azure Ative Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87b063a4d51d5d5d1e3d7949be3754ccbe74acca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85604119"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Atribuir funções de administrador e não administrador aos utilizadores com o Azure Ative Directory
Se um utilizador da sua organização precisar de permissão para gerir os recursos do Azure Ative Directory (Azure AD), deve atribuir ao utilizador um papel adequado no Azure AD, com base nas ações que o utilizador necessita de permissão para realizar.

Para obter mais informações sobre as funções disponíveis, consulte [as funções de administrador de atribuição no Diretório Ativo Azure](../users-groups-roles/directory-assign-admin-roles.md). Para obter mais informações sobre a adição de utilizadores, consulte [adicionar novos utilizadores ao Azure Ative Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Atribuir funções
Uma forma comum de atribuir funções Azure AD a um utilizador está na página **de função** do Diretório para um utilizador.

Também pode atribuir funções utilizando Gestão de Identidade Privilegiada (PIM). Para obter informações mais detalhadas sobre como utilizar a PIM, consulte [Gestão de Identidade Privilegiada.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)

### <a name="to-assign-a-role-to-a-user"></a>Atribuir uma função a um utilizador
1. Vá ao [portal Azure](https://portal.azure.com/) e faça login usando uma conta de administrador global para o diretório. 

2. Procure e selecione **Azure Active Directory**.

      ![Pesquisa no portal Azure para O Diretório Ativo Azure](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Selecione **Utilizadores**.

4. Procure e selecione o utilizador recebendo a atribuição de funções. Por exemplo, _Alain Caronte._

      ![Todas as páginas dos utilizadores - selecione o utilizador](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na página **Alain Charon - Profile,** selecione **Funções atribuídas**.

    Aparece a página **de papel do Alain Charon - Diretório.**

6. **Selecione Adicionar a atribuição,** selecione a função a atribuir a Alain (por exemplo, _administrador de aplicação),_ e, em seguida, escolha **Selecione**.

    ![Página de funções atribuídas - mostrando o papel selecionado](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    A função de administrador de aplicação é atribuída a Alain Charon e aparece na página de **função do Alain Charon - Diretório.**

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função
Se precisar de remover a atribuição de funções de um utilizador, também pode fazê-lo a partir da página de **função Alain Charon - Diretório.**

### <a name="to-remove-a-role-assignment-from-a-user"></a>Para remover uma atribuição de função de um utilizador

1. Selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, procure e selecione o utilizador removendo a atribuição de funções. Por exemplo, _Alain Caronte._

2. Selecione **funções atribuídas**, selecione **administrador de aplicação**e, em seguida, selecione **Remover a designação**.

    ![Página de funções atribuídas, mostrando o papel selecionado e a opção de remoção](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    A função de administrador de aplicação é removida de Alain Charon e já não aparece na página de **função do Alain Charon - Diretório.**

## <a name="next-steps"></a>Próximos passos
- [Adicionar ou eliminar utilizadores](add-users-azure-active-directory.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Adicionar utilizadores convidados a partir de outro diretório](../b2b/what-is-b2b.md)

Ou pode executar outras tarefas de gestão de utilizadores, como atribuir delegados, usar políticas e partilhar contas de utilizador. Para obter mais informações sobre outras ações disponíveis, consulte [a documentação de gestão de utilizadores do Azure Ative Directory](../users-groups-roles/index.yml).


