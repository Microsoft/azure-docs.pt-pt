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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262116"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Adicionar ou eliminar os utilizadores que utilizam o Azure Active Directory

Adicione novos utilizadores ou elimine os utilizadores existentes da sua organização Azure Ative Directory (Azure AD). Para adicionar ou excluir utilizadores, tem de ser administrador de utilizador ou administrador global.

## <a name="add-a-new-user"></a>Adicionar um novo utilizador

Pode criar um novo utilizador com o portal do Azure Active Directory.

Para adicionar um novo utilizador, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador de utilizador para a organização.

1. Procure e selecione *Azure Ative Directory* a partir de qualquer página.

1. Selecione **Utilizadores**e, em seguida, selecione **Novo utilizador**.

    ![Adicione um utilizador através de Utilizadores - Todos os utilizadores em Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Na página **utilizador,** introduza informações para este utilizador:

   - **Nome**. Necessário. O nome próprio e apelido do utilizador novo. Por exemplo, *Mary Parker.*

   - **Nome do utilizador**. Necessário. O nome de utilizador do utilizador novo. Por exemplo, `mary@contoso.com`.

     A parte de domínio do nome de utilizador deve utilizar o nome de domínio padrão inicial, *\<yourdomainname>.onmicrosoft.com*, ou um nome de domínio personalizado, como *contoso.com*. Para obter mais informações sobre como criar um nome de domínio personalizado, consulte Adicionar o seu nome de [domínio personalizado usando o portal Azure Ative Directory](add-custom-domain.md).

   - **Grupos**. Opcionalmente, pode adicionar o utilizador a um ou mais grupos existentes. Também pode adicionar o utilizador a grupos num momento posterior. Para obter mais informações sobre a adição de utilizadores a grupos, consulte [Criar um grupo básico e adicionar membros usando o Diretório Ativo Azure](active-directory-groups-create-azure-portal.md).

   - **Função de diretório**: Se necessitar de permissões administrativas da AD Azure para o utilizador, pode adicioná-las a uma função Azure AD. Pode atribuir ao utilizador um administrador global ou uma ou mais das funções de administrador limitado no Azure AD. Para obter mais informações sobre a atribuição de funções, consulte [como atribuir funções aos utilizadores.](active-directory-users-assign-role-azure-portal.md)

   - **Informação de trabalho**: Pode adicionar mais informações sobre o utilizador aqui, ou fazê-lo mais tarde. Para mais informações sobre a adição de informações do utilizador, consulte [como adicionar ou alterar as informações sobre](active-directory-users-profile-azure-portal.md)o perfil do utilizador .

1. Copie a palavra-passe autogerada fornecida na caixa **password.** Terá de dar esta palavra-passe ao utilizador para iniciar sessão pela primeira vez.

1. Selecione **Criar**.

O utilizador é criado e adicionado à sua organização Azure AD.

## <a name="add-a-new-guest-user"></a>Adicione um novo utilizador convidado

Também pode convidar um novo utilizador convidado a colaborar com a sua organização, selecionando **o utilizador convidar** a partir da página de utilizador Da Nova página de **utilizadores.** Se as definições de colaboração externa da sua organização estiverem configuradas de tal forma que lhe é permitido convidar os hóspedes, o utilizador receberá um convite que deve aceitar para começar a colaborar. Para mais informações sobre o convite aos utilizadores de colaboração B2B, consulte [os utilizadores do B2B para o Diretório Ativo Azure](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Adicione um utilizador de consumo

Pode haver cenários em que pretende criar manualmente contas de consumo no seu diretório Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações sobre a criação de contas de consumo, consulte [Criar e eliminar utilizadores de consumidores no Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Adicionar um novo utilizador dentro de um ambiente híbrido

Se tiver um ambiente com o Azure Active Directory (cloud) e o Windows Server Active Directory (no local), pode adicionar novos utilizadores ao sincronizar os dados da conta de utilizador existente. Para obter mais informações sobre ambientes híbridos e utilizadores, consulte [Integrar os seus diretórios no local com o Diretório Ativo Azure.](../hybrid/whatis-hybrid-identity.md)

## <a name="delete-a-user"></a>Eliminar um utilizador

Pode eliminar um utilizador existente através do portal do Azure Active Directory.

Para eliminar um utilizador, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta de administrador do Utilizador para a organização.

1. Procure e selecione *Azure Ative Directory* a partir de qualquer página.

1. Procure e selecione o utilizador que pretende eliminar do seu inquilino Azure AD. Por exemplo, _Mary Parker._

1. **Selecione Eliminar o utilizador**.

    ![Utilizadores - página de todos os utilizadores com a eliminação do utilizador realçada](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

O utilizador é eliminado e já não aparece na página **Utilizadores - Todos os utilizadores.** O utilizador pode ser visto na página de **utilizadores Apagados** durante os próximos 30 dias e pode ser restaurado durante esse período. Para obter mais informações sobre a restauração de um utilizador, consulte [Restaurar ou remover um utilizador recentemente eliminado utilizando o Diretório Ativo Azure](active-directory-users-restore.md).

Quando um utilizador é eliminado, quaisquer licenças consumidas pelo utilizador são disponibilizadas para outros utilizadores.

>[!Note]
>Deve utilizar o Diretório Ativo do Windows Server para atualizar a identidade, informações de contacto ou informações de trabalho para utilizadores cuja fonte de autoridade é o Diretório Ativo do Servidor do Windows. Depois de concluir a atualização, tem de aguardar o próximo ciclo de sincronização seja concluída antes de verá as alterações.

## <a name="next-steps"></a>Passos seguintes

Depois de adicionar os seus utilizadores, pode fazer os seguintes processos básicos:

- [Adicionar ou alterar informações sobre o perfil](active-directory-users-profile-azure-portal.md)

- [Atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Trabalhar com grupos dinâmicos e utilizadores](../users-groups-roles/groups-create-rule.md)

Ou pode fazer outras tarefas de gestão do utilizador, tais como [adicionar utilizadores convidados de outro diretório](../b2b/what-is-b2b.md) ou [restaurar um utilizador eliminado](active-directory-users-restore.md). Para obter mais informações sobre outras ações disponíveis, consulte a documentação de [gestão de utilizadores do Azure Ative Directory.](../users-groups-roles/index.yml)
