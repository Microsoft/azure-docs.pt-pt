---
title: Adicionar ou eliminar utilizadores - Azure Ative Directory | Microsoft Docs
description: Instruções sobre como adicionar novos utilizadores ou eliminar utilizadores existentes utilizando o Azure Ative Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36455b3f6395216bd1ed26c8bd193f0e3ca10b5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594602"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Adicionar ou eliminar utilizadores usando o Azure Ative Directory

Adicione novos utilizadores ou elimine os utilizadores existentes da sua organização Azure Ative Directory (Azure AD). Para adicionar ou eliminar utilizadores tem de ser administrador do Utilizador ou administrador global.

## <a name="add-a-new-user"></a>Adicionar um novo utilizador

Pode criar um novo utilizador utilizando o portal Azure Ative Directory.

Para adicionar um novo utilizador, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador do Utilizador para a organização.

1. Procure e selecione *Azure Ative Directory* a partir de qualquer página.

1. Selecione **Utilizadores** e, em seguida, selecione **Novo utilizador**.

    ![Adicionar um utilizador através dos Utilizadores - Todos os utilizadores em Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Na página **do Utilizador,** introduza informações para este utilizador:

   - **Nome**. Obrigatório. O primeiro e último nome do novo utilizador. Por exemplo, *Mary Parker.*

   - **Nome do utilizador**. Obrigatório. O nome de utilizador do novo utilizador. Por exemplo, `mary@contoso.com`.

     A parte de domínio do nome de utilizador deve utilizar o nome de domínio inicial, *\<yourdomainname> .onmicrosoft.com*, ou um nome de domínio personalizado, como *contoso.com*. Para obter mais informações sobre como criar um nome de domínio personalizado, consulte [Adicionar o seu nome de domínio personalizado utilizando o portal Azure Ative Directory](add-custom-domain.md).

   - **Grupos**. Opcionalmente, pode adicionar o utilizador a um ou mais grupos existentes. Também pode adicionar o utilizador a grupos mais tarde. Para obter mais informações sobre a adição de utilizadores a grupos, consulte [criar um grupo básico e adicionar membros usando o Azure Ative Directory](active-directory-groups-create-azure-portal.md).

   - **Função diretório**: Se necessitar de permissões administrativas Azure AD para o utilizador, pode adicioná-las a uma função AZure AD. Pode atribuir ao utilizador um administrador global ou uma ou mais das funções de administrador limitada no AZure AD. Para obter mais informações sobre a atribuição de funções, consulte [Como atribuir funções aos utilizadores](active-directory-users-assign-role-azure-portal.md).

   - **Informações de trabalho**: Pode adicionar mais informações sobre o utilizador aqui, ou fazê-lo mais tarde. Para obter mais informações sobre a adição de informações do utilizador, consulte [Como adicionar ou alterar as informações do perfil do utilizador.](active-directory-users-profile-azure-portal.md)

1. Copie a palavra-passe autogerada fornecida na **caixa de senha.** Terá de dar esta palavra-passe ao utilizador para iniciar sação pela primeira vez.

1. Selecione **Criar**.

O utilizador é criado e adicionado à sua organização Azure AD.

## <a name="add-a-new-guest-user"></a>Adicione um novo utilizador convidado

Também pode convidar um novo utilizador convidado a colaborar com a sua organização selecionando o **utilizador Convidar** a partir da nova página do **utilizador.** Se as configurações de colaboração externa da sua organização estiverem configuradas de modo a que possa convidar os hóspedes, o utilizador receberá um convite que deve aceitar para começar a colaborar. Para obter mais informações sobre convidar utilizadores de colaboração B2B, consulte [utilizadores de Convite B2B para o Azure Ative Directory](../external-identities/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Adicionar um utilizador de consumo

Pode haver cenários em que pretende criar manualmente contas de consumidores no seu diretório Azure Ative Diretório B2C (Azure AD B2C). Para obter mais informações sobre a criação de contas de consumo, consulte [Criar e eliminar os utilizadores de consumidores em Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Adicione um novo utilizador dentro de um ambiente híbrido

Se tiver um ambiente com o Azure Ative Directory (cloud) e o Windows Server Ative Directory (no local), pode adicionar novos utilizadores sincronizando os dados da conta de utilizador existentes. Para obter mais informações sobre ambientes híbridos e utilizadores, consulte [Integre os seus diretórios no local com o Azure Ative Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Eliminar um utilizador

Pode eliminar um utilizador existente utilizando o portal Azure Ative Directory.

>[!Note]
>Tem de ter uma atribuição de função de administrador global ou administrador de utilizador para eliminar os utilizadores na sua organização. Os administradores globais podem eliminar qualquer utilizadores, incluindo outros administradores. Os administradores do utilizador podem eliminar quaisquer utilizadores não administrativos, administradores da Helpdesk e outros administradores do Utilizador. Para obter mais informações, consulte [permissões de função do Administrador em Azure AD](../roles/permissions-reference.md).

Para eliminar um utilizador, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta de administrador do Utilizador para a organização.

1. Procure e selecione *Azure Ative Directory* a partir de qualquer página.

1. Procure e selecione o utilizador que pretende eliminar do seu inquilino AZure AD. Por exemplo, _Mary Parker._

1. Selecione **Eliminar utilizador**.

    ![Utilizadores - Todas as páginas dos utilizadores com Delete utilizador destacado](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

O utilizador é eliminado e já não aparece na página **dos Utilizadores - Todos os utilizadores.** O utilizador pode ser visto na página de **utilizadores eliminados** durante os próximos 30 dias e pode ser restaurado durante esse período. Para obter mais informações sobre a restauração de um utilizador, consulte [Restaurar ou remover um utilizador recentemente eliminado utilizando o Azure Ative Directory](active-directory-users-restore.md).

Quando um utilizador é eliminado, quaisquer licenças consumidas pelo utilizador são disponibilizadas para outros utilizadores.

>[!Note]
>Para atualizar a identidade, informações de contacto ou informações de trabalho para utilizadores cuja fonte de autoridade é o Windows Server Ative Directory, tem de utilizar o Windows Server Ative Directory. Depois de concluir a atualização, tem de esperar que o próximo ciclo de sincronização esteja concluído antes de ver as alterações.

## <a name="next-steps"></a>Passos seguintes

Depois de adicionar os seus utilizadores, pode fazer os seguintes processos básicos:

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Trabalhar com grupos dinâmicos e utilizadores](../enterprise-users/groups-create-rule.md)

Ou pode fazer outras tarefas de gestão do utilizador, tais como [adicionar utilizadores convidados a partir de outro diretório](../external-identities/what-is-b2b.md) ou [restaurar um utilizador eliminado](active-directory-users-restore.md). Para obter mais informações sobre outras ações disponíveis, consulte [a documentação de gestão de utilizadores do Azure Ative Directory](../enterprise-users/index.yml).