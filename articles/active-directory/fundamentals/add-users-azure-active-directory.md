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
ms.openlocfilehash: b01eb7730290fbf7340fc0a6d8cac8157498f64a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013621"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Adicionar ou eliminar os utilizadores que utilizam o Azure Active Directory

Adicione novos usuários ou exclua usuários existentes da organização do Azure Active Directory (Azure AD). Para adicionar ou excluir usuários, você deve ser um administrador de usuário ou um administrador global.

## <a name="add-a-new-user"></a>Adicionar um novo utilizador

Pode criar um novo utilizador com o portal do Azure Active Directory.

### <a name="to-add-a-new-user"></a>Para adicionar um novo utilizador

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de usuário para a organização.

2. Selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **novo utilizador**.

    ![Utilizadores - página de todos os utilizadores com o novo utilizador realçado](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Na página **novo usuário** , selecione **criar usuário** e, em seguida, adicione as informações do usuário.

    ![Adicionar novo utilizador, a página de utilizador com informações do utilizador](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Nome (obrigatório)** : o nome e o sobrenome do novo usuário. Por exemplo, Chris Green.

   - **Nome de usuário (obrigatório)** : o nome de usuário do novo usuário. Por exemplo, chris@contoso.com.

     A parte do domínio do nome de usuário deve usar o nome de domínio padrão inicial, <_yourdomainname_>. onmicrosoft. com ou um nome de domínio personalizado em sua organização do Azure AD, como contoso.com. Você pode selecionar na lista de domínios disponíveis. Você também pode filtrar a lista digitando uma parte do nome de domínio. Para obter mais informações sobre como criar um nome de domínio personalizado, consulte [como adicionar um nome de domínio personalizado ao Azure Active Directory](add-custom-domain.md).

   - **Grupos**: você pode adicionar o usuário a um ou mais grupos existentes ou pode fazer isso mais tarde. Para obter mais informações sobre como adicionar utilizadores a grupos, consulte [como criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md).

   - **Função de diretório**: se você precisar de permissões administrativas do Azure ad para o usuário, poderá adicioná-las a uma função do Azure AD. Você pode atribuir o usuário para ser um administrador global ou uma ou mais das funções de administrador limitadas no Azure AD. Para obter mais informações sobre a atribuição de funções, consulte [como atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md).

   - **Informações do trabalho**: você pode adicionar mais informações sobre o usuário aqui ou fazer isso mais tarde. Para obter mais informações sobre como adicionar informações do utilizador, consulte [como adicionar ou alterar as informações de perfil do usuário](active-directory-users-profile-azure-portal.md).

4. Copie o gerado automaticamente palavra-passe fornecida no **palavra-passe** caixa. Você pode optar por usar a senha gerada automaticamente fornecida na caixa senha ou criar uma senha personalizada. Terá de dar esta palavra-passe ao utilizador para o processo de início de sessão inicial.

5. Selecione **Criar**.

O usuário é criado e adicionado à sua organização do Azure AD.

## <a name="add-a-new-guest-user"></a>Adicionar um novo usuário convidado

Você também pode convidar o novo usuário convidado a colaborar com sua organização selecionando **convidar usuário** na página **novo usuário** . Se as configurações de colaboração externa da sua organização estiverem configuradas de forma que você tenha permissão para convidar convidados, o usuário receberá um convite por email para que ele possa começar a colaborar. Para obter mais informações sobre como convidar usuários de colaboração B2B, consulte [convidar usuários B2B para Azure Active Directory](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Adicionar um usuário do consumidor

Pode haver cenários nos quais você deseja criar manualmente contas de consumidor em seu diretório Azure Active Directory B2C (Azure AD B2C). Para obter mais informações sobre como criar contas de consumidor, consulte [criar e excluir usuários do consumidor no Azure ad B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Adicionar um novo utilizador dentro de um ambiente híbrido

Se tiver um ambiente com o Azure Active Directory (cloud) e o Windows Server Active Directory (no local), pode adicionar novos utilizadores ao sincronizar os dados da conta de utilizador existente. Para obter mais informações sobre ambientes híbridos e utilizadores, consulte [integrar seus diretórios no local com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Eliminar um utilizador

Pode eliminar um utilizador existente através do portal do Azure Active Directory.

### <a name="to-delete-a-user"></a>Para eliminar um utilizador

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador de usuário para a organização.

1. Selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, procure e selecione o utilizador que pretende eliminar do seu inquilino do Azure AD. Por exemplo, _Mary Parker_.

1. Selecione **eliminação do utilizador**.

    ![Utilizadores - página de todos os utilizadores com a eliminação do utilizador realçada](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    O utilizador é eliminado e já não aparece no **utilizadores - todos os utilizadores** página. O utilizador pode ser visto no **utilizadores eliminados** página para os próximos 30 dias e podem ser restaurados durante esse período. Para obter mais informações sobre como restaurar um utilizador, consulte [como restaurar ou remover permanentemente um utilizador eliminado recentemente](active-directory-users-restore.md). Quando um usuário é excluído, todas as licenças consumidas pelo usuário são disponibilizadas para que outros usuários sejam consumidos.

    >[!Note]
    >Tem de utilizar o Windows Server Active Directory para atualizar a identidade, informações de contacto ou informações da tarefa para os utilizadores cuja origem de autoridade é o Windows Server Active Directory. Depois de concluir a atualização, tem de aguardar o próximo ciclo de sincronização seja concluída antes de verá as alterações.

## <a name="next-steps"></a>Passos seguintes

Depois de adicionar os seus utilizadores, pode efetuar os seguintes processos de basic:

- [Adicionar ou alterar as informações de perfil](active-directory-users-profile-azure-portal.md)

- [Atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Trabalhar com grupos dinâmicos e utilizadores](../users-groups-roles/groups-create-rule.md)

Ou você pode executar outras tarefas de gerenciamento de usuário, como [Adicionar usuários convidados de outra organização do Azure ad](../b2b/what-is-b2b.md) ou [restaurar um usuário excluído](active-directory-users-restore.md). Para obter mais informações sobre outras ações disponíveis, consulte [documentação de gestão de utilizador do Azure Active Directory](../users-groups-roles/index.yml).
