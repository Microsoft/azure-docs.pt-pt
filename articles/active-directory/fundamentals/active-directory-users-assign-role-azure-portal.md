---
title: Atribuir funções de diretório aos utilizadores - Diretório Ativo Azure / Microsoft Docs
description: Instruções sobre como atribuir funções de administrador e não administrador a utilizadores com Diretório Ativo Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422906"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Atribuir funções de administrador e não administrador a utilizadores com Diretório Ativo Azure
Se um utilizador da sua organização necessitar de autorização para gerir os recursos do Azure Ative Directory (Azure AD), deve atribuir ao utilizador um papel adequado no Azure AD, com base nas ações que o utilizador necessita de permissão para executar.

Para obter mais informações sobre as funções disponíveis, consulte as funções de [administrador de atribuição no Diretório Ativo azure](../users-groups-roles/directory-assign-admin-roles.md). Para mais informações sobre a adição de utilizadores, consulte [Adicionar novos utilizadores ao Diretório Ativo do Azure](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Atribuir funções
Uma forma comum de atribuir funções de AD Azure a um utilizador está na página de **papel do Diretório** para um utilizador.

Também pode atribuir funções utilizando a Privileged Identity Management (PIM). Para obter informações mais detalhadas sobre como utilizar a PIM, consulte [A Gestão de Identidade Privilegiada.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)

### <a name="to-assign-a-role-to-a-user"></a>Atribuir uma função a um utilizador
1. Vá ao [portal Azure](https://portal.azure.com/) e faça login usando uma conta de administrador global para o diretório. 

2. Procure e selecione **Azure Active Directory**.

      ![Pesquisa do portal Azure por Azure Ative Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Selecione **Utilizadores**.

4. Procure e selecione o utilizador obtendo a atribuição de funções. Por exemplo, _Alain Charon._

      ![Página de todos os utilizadores - selecione o utilizador](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na página **Alain Charon - Perfil,** selecione **funções atribuídas**.

    A página de papel de **Alain Charon - Diretório** aparece.

6. **Selecione Adicionar a tarefa,** selecione a função de atribuir a Alain (por exemplo, administrador de _aplicação),_ e, em seguida, escolha **Selecionar**.

    ![Página de papéis atribuídos - mostrando o papel selecionado](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    O papel de administrador de aplicação é atribuído a Alain Charon e aparece na página de **papel de Alain Charon - Diretório.**

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função
Se precisar de remover a atribuição de funções de um utilizador, também pode fazê-lo a partir da página de **papel alain Charon - Diretório.**

### <a name="to-remove-a-role-assignment-from-a-user"></a>Para remover uma atribuição de funções de um utilizador

1. Selecione **Diretório Ativo Azure**, selecione **Utilizadores,** e depois procure e selecione o utilizador recebendo a atribuição de funções removida. Por exemplo, _Alain Charon._

2. Selecione **as funções atribuídas,** selecione administrador de **aplicação**e, em seguida, selecione **Remover a tarefa**.

    ![Página de funções atribuídas, mostrando a função selecionada e a opção de remoção](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    O papel de administrador de aplicação é removido de Alain Charon e já não aparece na página de **papel de Alain Charon - Diretório.**

## <a name="next-steps"></a>Passos seguintes
- [Adicionar ou eliminar utilizadores](add-users-azure-active-directory.md)

- [Adicionar ou alterar informações sobre o perfil](active-directory-users-profile-azure-portal.md)

- [Adicionar utilizadores convidados a partir de outro diretório](../b2b/what-is-b2b.md)

Ou pode executar outras tarefas de gestão de utilizadores, tais como atribuir delegados, usar políticas e partilhar contas de utilizadores. Para obter mais informações sobre outras ações disponíveis, consulte a documentação de [gestão de utilizadores do Azure Ative Directory.](../users-groups-roles/index.yml)


