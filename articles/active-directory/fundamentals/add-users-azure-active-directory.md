---
title: Adicionar ou eliminar utilizadores - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como adicionar novos utilizadores ou eliminar os utilizadores existentes com o Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d72616422934501e042375edfb10a25aa27c527
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073477"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Adicionar ou eliminar os utilizadores que utilizam o Azure Active Directory

Adicione novos usuários ou exclua usuários existentes da organização do Azure Active Directory (Azure AD). Para adicionar ou excluir usuários, você deve ser um administrador de usuário ou um administrador global.

## <a name="add-a-new-user"></a>Adicionar um novo utilizador

Pode criar um novo utilizador com o portal do Azure Active Directory.

Para adicionar um novo usuário, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de usuário para a organização.

1. Pesquise e selecione *Azure Active Directory* em qualquer página.

1. Selecione **usuários**e, em seguida, selecione **novo usuário**.

    ![Adicionar um usuário por meio de usuários-todos os usuários no Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Na página do **usuário** , insira as informações para este usuário:

   - **Nome**. Necessário. O nome próprio e apelido do utilizador novo. Por exemplo, *Mary Parker*.

   - **Nome de usuário**. Necessário. O nome de utilizador do utilizador novo. Por exemplo, `mary@contoso.com`.

     A parte do domínio do nome de usuário deve usar o nome de domínio padrão inicial, *\<yourdomainname >. onmicrosoft. com*ou um nome de domínio personalizado, como *contoso.com*. Para obter mais informações sobre como criar um nome de domínio personalizado, consulte [Adicionar seu nome de domínio personalizado usando o portal de Azure Active Directory](add-custom-domain.md).

   - **Grupos**. Opcionalmente, pode adicionar o utilizador a um ou mais grupos existentes. Também pode adicionar o utilizador a grupos num momento posterior. Para obter mais informações sobre como adicionar usuários a grupos, consulte [criar um grupo básico e adicionar membros usando Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Função de diretório**: se você precisar de permissões administrativas do Azure ad para o usuário, poderá adicioná-las a uma função do Azure AD. Você pode atribuir o usuário para ser um administrador global ou uma ou mais das funções de administrador limitadas no Azure AD. Para obter mais informações sobre a atribuição de funções, consulte [como atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md).

   - **Informações do trabalho**: você pode adicionar mais informações sobre o usuário aqui ou fazer isso mais tarde. Para obter mais informações sobre como adicionar informações do utilizador, consulte [como adicionar ou alterar as informações de perfil do usuário](active-directory-users-profile-azure-portal.md).

1. Copie a senha gerada automaticamente fornecida na caixa **senha** . Você precisará fornecer essa senha ao usuário para entrar pela primeira vez.

1. Selecione **Criar**.

O usuário é criado e adicionado à sua organização do Azure AD.

## <a name="add-a-new-guest-user"></a>Adicionar um novo usuário convidado

Você também pode convidar o novo usuário convidado a colaborar com sua organização selecionando **convidar usuário** na página **novo usuário** . Se as configurações de colaboração externa da sua organização estiverem configuradas de forma que você tenha permissão para convidar convidados, o usuário receberá um convite por email para que ele possa começar a colaborar. Para obter mais informações sobre como convidar usuários de colaboração B2B, consulte [convidar usuários B2B para Azure Active Directory](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Adicionar um usuário do consumidor

Pode haver cenários nos quais você deseja criar manualmente contas de consumidor em seu diretório Azure Active Directory B2C (Azure AD B2C). Para obter mais informações sobre como criar contas de consumidor, consulte [criar e excluir usuários do consumidor no Azure ad B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Adicionar um novo utilizador dentro de um ambiente híbrido

Se tiver um ambiente com o Azure Active Directory (cloud) e o Windows Server Active Directory (no local), pode adicionar novos utilizadores ao sincronizar os dados da conta de utilizador existente. Para obter mais informações sobre ambientes híbridos e utilizadores, consulte [integrar seus diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Eliminar um utilizador

Pode eliminar um utilizador existente através do portal do Azure Active Directory.

Para excluir um usuário, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador de usuário para a organização.

1. Pesquise e selecione *Azure Active Directory* em qualquer página.

1. Pesquise e selecione o usuário que você deseja excluir do seu locatário do Azure AD. Por exemplo, _Mary Parker_.

1. Selecione **eliminação do utilizador**.

    ![Utilizadores - página de todos os utilizadores com a eliminação do utilizador realçada](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

O utilizador é eliminado e já não aparece no **utilizadores - todos os utilizadores** página. O utilizador pode ser visto no **utilizadores eliminados** página para os próximos 30 dias e podem ser restaurados durante esse período. Para obter mais informações sobre como restaurar um usuário, consulte [restaurar ou remover um usuário excluído recentemente usando Azure Active Directory](active-directory-users-restore.md).

Quando um usuário é excluído, todas as licenças consumidas pelo usuário são disponibilizadas para outros usuários.

>[!Note]
>Você deve usar o Windows Server Active Directory para atualizar a identidade, as informações de contato ou as informações do trabalho para usuários cuja origem de autoridade é Active Directory do Windows Server. Depois de concluir a atualização, tem de aguardar o próximo ciclo de sincronização seja concluída antes de verá as alterações.

## <a name="next-steps"></a>Passos seguintes

Depois de adicionar os usuários, você pode fazer os seguintes processos básicos:

- [Adicionar ou alterar as informações de perfil](active-directory-users-profile-azure-portal.md)

- [Atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Trabalhar com grupos dinâmicos e utilizadores](../users-groups-roles/groups-create-rule.md)

Ou você pode fazer outras tarefas de gerenciamento de usuário, como [Adicionar usuários convidados de outro diretório](../b2b/what-is-b2b.md) ou [restaurar um usuário excluído](active-directory-users-restore.md). Para obter mais informações sobre outras ações disponíveis, consulte [documentação de gestão de utilizador do Azure Active Directory](../users-groups-roles/index.yml).
