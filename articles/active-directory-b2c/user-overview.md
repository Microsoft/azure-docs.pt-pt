---
title: Descrição geral de utilizador de contas no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre contas de utilizador no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9d4bcc66cfd82fee13ce57d096e061ddd8706720
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419089"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Descrição geral das contas de utilizador no Azure Active Directory B2C

No Azure Active Directory (Azure AD) B2C, pode utilizar diferentes tipos de contas. O Azure Active Directory, Azure Active Directory B2B e Azure Active Directory B2C partilham os tipos de contas de utilizador que podem ser utilizadas.

Estão disponíveis os seguintes tipos de contas:

- **Conta profissional** – uma conta profissional pode aceder a recursos num inquilino e, com um administrador de função, pode gerir os inquilinos.
- **Conta de convidado** -uma conta de convidado só pode ser uma conta Microsoft ou de um utilizador do Azure Active Directory que pode ser utilizado para aceder a aplicações ou gerir os inquilinos. 
- **Conta de consumidor** -uma conta de consumidor é criada através de um fluxo de inscrição de utilizador numa aplicação do Azure AD B2C ou com o Azure AD Graph API e é utilizada por utilizadores das aplicações que estão registados com o Azure AD B2C. 

## <a name="work-account"></a>Conta profissional

Uma conta profissional é criada da mesma forma para todos os inquilinos com base no Azure AD. Para criar uma conta profissional, pode utilizar as informações no [início rápido: Adicionar novos utilizadores ao Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). É criada uma conta de trabalho com o **novo utilizador** choice no portal do Azure.

Quando adiciona uma nova conta de trabalho, precisa considerar as seguintes definições de configuração:

- **Nome** e **nome de utilizador** – a **nome** propriedade contém a determinado e o apelido do utilizador. O **nome de utilizador** é o identificador introduzido pelo utilizador para iniciar sessão. O nome de utilizador inclui o domínio completo. A parte do nome de utilizador do nome de domínio tem de ser o nome de domínio predefinido inicial *your domain.onmicrosoft.com*, ou verificado, não federadas [domínio personalizado](../active-directory/fundamentals/add-custom-domain.md) nome, tais como  *contoso.com*.
- **Perfil** -a conta está configurada com um perfil de dados de utilizador. Terá a oportunidade de inserir um nome próprio, apelido, cargo e nome do departamento. Pode editar o perfil depois de criar a conta.
- **Grupos** -utilizar um grupo para efetuar tarefas de gestão, tais como atribuir licenças ou permissões para um número de utilizadores ou dispositivos ao mesmo tempo. Pode colocar a nova conta num existente [grupo](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) no seu inquilino. 
- **Função de diretório** -tem de especificar o nível de acesso que a conta de utilizador tem a recursos no seu inquilino. Os seguintes níveis de permissão estão disponíveis:

    - **Utilizador** -os utilizadores podem aceder a recursos atribuídos mas não é possível gerir a maioria dos recursos de inquilino.
    - **Administrador global** -os administradores globais têm controlo total sobre todos os recursos de inquilino.
    - **Administrador limitado** -selecione a função ou funções administrativas para o utilizador. Para obter mais informações sobre as funções que podem ser selecionados, consulte [atribuir funções de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md). 

### <a name="create-a-work-account"></a>Criar uma conta profissional

Pode utilizar as seguintes informações para criar uma nova conta de trabalho:

- [Portal do Azure](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Atualizar um perfil de utilizador

Pode utilizar as seguintes informações para atualizar o perfil de um utilizador:

- [Portal do Azure](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Repor uma palavra-passe para um utilizador

Pode utilizar as seguintes informações para repor a palavra-passe de um utilizador: 

- [Portal do Azure](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Utilizador convidado

Pode convidar utilizadores externos ao seu inquilino como um utilizador convidado. Um cenário típico para convidar um utilizador convidado ao seu inquilino do Azure AD B2C é compartilhar as responsabilidades de administração. Para obter um exemplo de utilizar uma conta de convidado, consulte [propriedades de um utilizador de colaboração do Azure Active Directory B2B](../active-directory/b2b/user-properties.md).

Ao convidar um utilizador convidado ao seu inquilino, fornecer o endereço de e-mail do destinatário e uma mensagem que descreve o convite. A ligação de convite leva o usuário para a página de consentimento em que o **começar** botão está selecionado e a revisão de permissões é aceite. Se uma caixa de entrada não está anexada para o endereço de e-mail, o usuário pode navegar para a página de consentimento ao aceder a uma página da Microsoft com as credenciais de convidado. Para resgatar o convite da mesma forma que clicar na ligação no e-mail, em seguida, forçar o utilizador. Por exemplo: `https://myapps.microsoft.com/B2CTENANTNAME`.

Também pode utilizar o [Microsoft Graph API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) convidar um utilizador convidado.

## <a name="consumer-user"></a>Utilizador de consumidor

O utilizador de consumidor pode iniciar sessão nas aplicações protegidas pelo Azure AD B2C, mas não é possível aceder a recursos do Azure, como o portal do Azure.  O utilizador de consumidor pode utilizar uma conta local ou federadas contas, como o Facebook e Twitter. É criada uma conta de consumidor utilizando um [fluxo de utilizador de inscrição ou início de sessão](../active-directory-b2c/active-directory-b2c-reference-policies.md).

Pode especificar os dados que são recolhidos quando é criada uma conta de utilizador de consumidor por meio de atributos de utilizador personalizada. Para obter mais informações, consulte [definir atributos personalizados no Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-reference-custom-attr.md).

Pode utilizar as informações a **criar contas de utilizador de consumidor** secção [utilizar o Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) para criar uma conta de consumidor do Azure AD B2C. Também pode utilizar as informações a **atualizar contas de utilizador do consumidor** secção no mesmo artigo para gerir as propriedades da conta.

### <a name="migrate-consumer-user-accounts"></a>Migrar contas de utilizador do consumidor

Poderá ter uma necessidade para migrar as contas de utilizador existentes do consumidor de qualquer fornecedor de identidade para o Azure AD B2C. Para obter mais informações, consulte [migração de utilizador](active-directory-b2c-user-migration.md) ou [migrar os utilizadores com identidades sociais](active-directory-b2c-social-migration.md).
