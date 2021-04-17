---
title: Atribuir funções AZURE AD aos utilizadores - Azure Ative Directory | Microsoft Docs
description: Instruções sobre como atribuir funções de administrador e não administrador aos utilizadores com o Azure Ative Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4ffcad6f6be16ba7ac3674d710dd543f729f0c3
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575416"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Atribuir funções de administrador e não administrador aos utilizadores com o Azure Ative Directory

No Azure Ative Directory (Azure AD), se um dos seus utilizadores precisar de permissão para gerir os recursos Azure AD, deve atribuí-los a uma função que forneça as permissões de que necessitam. Para obter informações sobre quais as funções que gerem os recursos da Azure e quais as funções que gerem os recursos da AD Azure, consulte [as funções de administrador de subscrição clássica, funções de Azure e Ad AD.](../../role-based-access-control/rbac-and-directory-admin-roles.md)

Para obter mais informações sobre as funções de Ad Azure disponíveis, consulte [as funções de administrador de atribuição no Diretório Ativo Azure](../roles/permissions-reference.md). Para adicionar utilizadores, consulte [Adicionar novos utilizadores ao Azure Ative Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Atribuir funções

Uma forma comum de atribuir funções Azure AD a um utilizador está na página **de funções atribuídas** para um utilizador. Também pode configurar a elegibilidade do utilizador para ser elevado just-in-time para uma função usando Gestão de Identidade Privilegiada (PIM). Para obter mais informações sobre como utilizar a PIM, consulte [Gestão de Identidade Privilegiada.](../privileged-identity-management/index.yml)

> [!Note]
> Se tiver um plano de licença Azure AD Premium P2 e já utilizar o PIM, todas as tarefas de gestão de funções são desempenhadas na [experiência de Gestão de Identidade Privilegiada.](../roles/manage-roles-portal.md) Atualmente, esta funcionalidade limita-se a atribuir apenas uma função de cada vez. Não é possível selecionar várias funções e atribuí-las a um utilizador de uma só vez.
>
> ![Funções AD AD geridas em PIM para utilizadores que já usam PIM e têm uma licença Premium P2](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Atribuir uma função a um utilizador

1. Vá ao [portal Azure](https://portal.azure.com/) e inscreva-se usando uma conta de administrador global para o diretório.

2. Procure e selecione **Azure Active Directory**.

      ![Pesquisa no portal Azure para O Diretório Ativo Azure](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. Selecione **Utilizadores**.

4. Procure e selecione o utilizador recebendo a atribuição de funções. Por exemplo, _Alain Caronte._

      ![Todas as páginas dos utilizadores - selecione o utilizador](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na página **Alain Charon - Profile,** selecione **Funções atribuídas**.

    Aparece a página **Alain Charon - Funções Administrativas.**

6. Selecione **atribuir atribuições,** selecione a função a atribuir a Alain (por exemplo, _administrador de aplicação)_ e, em seguida, escolha **Selecione**.

    ![Página de funções atribuídas - mostrando o papel selecionado](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    A função de administrador de aplicação é atribuída a Alain Charon e aparece na página **Alain Charon - Funções Administrativas.**

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

Se precisar de remover a atribuição de funções de um utilizador, também pode fazê-lo a partir da página **Alain Charon - Funções Administrativas.**

### <a name="to-remove-a-role-assignment-from-a-user"></a>Para remover uma atribuição de função de um utilizador

1. Selecione **Azure Ative Directory**, selecione **Utilizadores** e, em seguida, procure e selecione o utilizador removendo a atribuição de funções. Por exemplo, _Alain Caronte._

2. Selecione **funções atribuídas**, selecione **administrador de aplicação** e, em seguida, selecione **Remover a designação**.

    ![Página de funções atribuídas, mostrando o papel selecionado e a opção de remoção](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    A função de administrador de aplicação é removida de Alain Charon e já não aparece na página **Alain Charon - Funções Administrativas.**

## <a name="next-steps"></a>Passos seguintes

- [Adicionar ou eliminar utilizadores](add-users-azure-active-directory.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Adicionar utilizadores convidados a partir de outro diretório](../external-identities/what-is-b2b.md)

Outras tarefas de gestão do utilizador que pode consultar estão disponíveis na documentação de [gestão de utilizadores do Azure Ative Directory.](../enterprise-users/index.yml)
