---
title: Visão geral das contas de usuário no Azure Active Directory B2C
description: Saiba mais sobre os tipos de contas de usuário que podem ser usados no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a627b0eebc3aa4a19b0670f899d3032d8df58da4
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620463"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Visão geral das contas de usuário no Azure Active Directory B2C

No Azure Active Directory B2C (Azure AD B2C), há vários tipos de contas que podem ser criadas. Azure Active Directory, Active Directory B2B e Active Directory B2C compartilhamento nos tipos de contas de usuário que podem ser usados.

Os seguintes tipos de contas estão disponíveis:

- **Conta corporativa** – uma conta corporativa pode acessar recursos em um locatário e, com uma função de administrador, pode gerenciar locatários.
- **Conta de convidado** -uma conta de convidado só pode ser uma conta Microsoft ou um usuário Azure Active Directory que possa ser usado para acessar aplicativos ou gerenciar locatários.
- **Conta de consumidor** -uma conta de consumidor é usada por um usuário dos aplicativos que você registrou com Azure ad B2C. As contas de consumidor podem ser criadas por:
  - O usuário que está passando por um fluxo de usuário de inscrição em um aplicativo Azure AD B2C
  - Usando o Azure AD API do Graph
  - Utilizar o portal do Azure

## <a name="work-account"></a>Conta corporativa

Uma conta corporativa é criada da mesma maneira para todos os locatários com base no Azure AD. Para criar uma conta de trabalho, você pode usar as informações em [início rápido: adicionar novos usuários a Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Uma conta corporativa é criada usando a **nova** opção de usuário na portal do Azure.

Ao adicionar uma nova conta de trabalho, você precisa considerar as seguintes definições de configuração:

- **Nome** e **nome de usuário** -a propriedade **Name** contém o dado e o sobrenome do usuário. O **nome de usuário** é o identificador que o usuário insere para entrar. O nome de usuário inclui o domínio completo. A parte do nome de domínio do nome de usuário deve ser o nome de domínio padrão inicial *your-domain.onmicrosoft.com*ou um nome de [domínio personalizado](../active-directory/fundamentals/add-custom-domain.md) verificado e não federado, como *contoso.com*.
- **Perfil** -a conta é configurada com um perfil de dados do usuário. Você tem a oportunidade de inserir nome, sobrenome, cargo e nome do departamento. Você pode editar o perfil depois que a conta é criada.
- **Grupos** – use um grupo para executar tarefas de gerenciamento, como atribuir licenças ou permissões a vários usuários ou dispositivos de uma vez. Você pode colocar a nova conta em um [grupo](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) existente em seu locatário.
- **Função de diretório** – você precisa especificar o nível de acesso que a conta de usuário tem aos recursos em seu locatário. Os seguintes níveis de permissão estão disponíveis:

    - **Usuário** -os usuários podem acessar recursos atribuídos, mas não podem gerenciar a maioria dos recursos de locatário.
    - **Administrador global** -os administradores globais têm controle total sobre todos os recursos de locatário.
    - **Administrador limitado** – selecione a função administrativa ou as funções para o usuário. Para obter mais informações sobre as funções que podem ser selecionadas, consulte [atribuindo funções de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="create-a-work-account"></a>Criar uma conta corporativa

Você pode usar as seguintes informações para criar uma nova conta de trabalho:

- [Portal do Azure](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Atualizar um perfil de usuário

Você pode usar as seguintes informações para atualizar o perfil de um usuário:

- [Portal do Azure](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Redefinir uma senha para um usuário

Você pode usar as seguintes informações para redefinir a senha de um usuário:

- [Portal do Azure](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Usuário convidado

Você pode convidar usuários externos para seu locatário como um usuário convidado. Um cenário típico para convidar um usuário convidado para seu locatário de Azure AD B2C é compartilhar responsabilidades de administração. Para obter um exemplo de como usar uma conta de convidado, consulte [Propriedades de um usuário de colaboração Azure Active Directory B2B](../active-directory/b2b/user-properties.md).

Ao convidar um usuário convidado para seu locatário, você fornece o endereço de email do destinatário e uma mensagem que descreve o convite. O link do convite leva o usuário para a página de consentimento em que o botão de **introdução** está selecionado e a revisão das permissões é aceita. Se uma caixa de entrada não estiver anexada ao endereço de email, o usuário poderá navegar até a página de consentimento acessando uma página da Microsoft usando as credenciais convidadas. O usuário é forçado a resgatar o convite da mesma forma que clicar no link no email. Por exemplo: `https://myapps.microsoft.com/B2CTENANTNAME`.

Você também pode usar a [API Microsoft Graph](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) para convidar um usuário convidado.

## <a name="consumer-user"></a>Usuário do consumidor

O usuário do consumidor pode entrar em aplicativos protegidos por Azure AD B2C, mas não pode acessar recursos do Azure, como o portal do Azure. O usuário consumidor pode usar uma conta local ou contas federadas, como o Facebook ou o Twitter. Uma conta de consumidor é criada usando um [fluxo de usuário de inscrição ou de entrada](../active-directory-b2c/active-directory-b2c-reference-policies.md), usando o API do Graph do Azure ad ou usando o portal do Azure.

Você pode especificar os dados que são coletados quando uma conta de usuário do consumidor é criada usando atributos de usuário personalizados. Para obter mais informações, consulte [definir atributos personalizados no Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-reference-custom-attr.md).

Você pode usar as informações na seção **criar contas de usuário do consumidor** em [usar o API do Graph do Azure ad](active-directory-b2c-devquickstarts-graph-dotnet.md) para criar uma conta de consumidor do Azure ad B2C. Você também pode usar as informações na seção **atualizar contas de usuário do consumidor** no mesmo artigo para gerenciar as propriedades da conta.

### <a name="migrate-consumer-user-accounts"></a>Migrar contas de usuário do consumidor

Talvez você precise migrar contas de usuário de consumidor existentes de qualquer provedor de identidade para Azure AD B2C. Para obter mais informações, consulte [migração do usuário](active-directory-b2c-user-migration.md) ou [migrar usuários com identidades sociais](active-directory-b2c-social-migration.md).
