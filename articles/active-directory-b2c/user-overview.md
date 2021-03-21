---
title: Visão geral das contas dos utilizadores no Azure Ative Directory B2C
description: Saiba mais sobre os tipos de contas de utilizador que podem ser usadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c42b79cb8b92b5f96f31eb58f091ff0ce79b84f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652135"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Visão geral das contas dos utilizadores no Azure Ative Directory B2C

No Azure Ative Directory B2C (Azure AD B2C), existem vários tipos de contas que podem ser criadas. O Azure Ative Directory, o Ative Directory B2B e o Ative Directory B2C partilham os tipos de contas de utilizador que podem ser utilizadas.

Estão disponíveis os seguintes tipos de contas:

- **Conta de trabalho** - Uma conta de trabalho pode aceder a recursos num inquilino, e com uma função de administrador, pode gerir inquilinos.
- **Conta de hóspedes** - Uma conta de hóspedes só pode ser uma conta Microsoft ou um utilizador do Azure Ative Directory que pode ser usado para aceder a aplicações ou gerir inquilinos.
- **Conta de consumo** - Uma conta de consumo é utilizada por um utilizador das aplicações que registou no Azure AD B2C. As contas do consumidor podem ser criadas por:
  - O utilizador que passa por um fluxo de utilizador de inscrição numa aplicação AZure AD B2C
  - Utilização da Microsoft Graph API
  - Utilizar o portal do Azure

## <a name="work-account"></a>Conta de trabalho

Uma conta de trabalho é criada da mesma forma para todos os inquilinos com base na Azure AD. Para criar uma conta de trabalho, pode utilizar a informação em [Quickstart: Adicionar novos utilizadores ao Azure Ative Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Uma conta de trabalho é criada utilizando a escolha do **novo utilizador** no portal Azure.

Quando adicionar uma nova conta de trabalho, tem de considerar as seguintes definições de configuração:

- **Nome** e **nome de utilizador** - A propriedade **Name** contém o dado e o sobrenome do utilizador. O **nome de Utilizador** é o identificador em que o utilizador entra para iniciar súm. O nome de utilizador inclui todo o domínio. A parte do nome de domínio do nome de utilizador deve ser o nome de domínio padrão inicial *your-domain.onmicrosoft.com*, ou um nome de [domínio personalizado](../active-directory/fundamentals/add-custom-domain.md) não federado verificado, como *contoso.com*.
- **Perfil** - A conta é configurada com um perfil de dados do utilizador. Tem a oportunidade de introduzir um primeiro nome, apelido, cargo e nome do departamento. Pode editar o perfil após a criação da conta.
- **Grupos** - Utilize grupos para executar tarefas de gestão, tais como atribuir licenças ou permissões a muitos utilizadores ou dispositivos ao mesmo tempo. Pode colocar a nova conta num [grupo](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) existente no seu inquilino.
- **Função diretório** - É necessário especificar o nível de acesso que a conta de utilizador tem para recursos no seu inquilino. Estão disponíveis os seguintes níveis de permissão:

    - **Utilizador** - Os utilizadores podem aceder aos recursos atribuídos, mas não conseguem gerir a maioria dos recursos dos inquilinos.
    - **Administrador global** - Os administradores globais têm total controlo sobre todos os recursos dos inquilinos.
    - **Administrador limitado** - Selecione a função administrativa ou as funções para o utilizador. Para obter mais informações sobre as funções que podem ser selecionadas, consulte [atribuindo funções de administrador no Diretório Ativo Azure](../active-directory/roles/permissions-reference.md).

### <a name="create-a-work-account"></a>Criar uma conta de trabalho

Pode utilizar as seguintes informações para criar uma nova conta de trabalho:

- [Portal do Azure](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](/graph/api/user-post-users)

### <a name="update-a-user-profile"></a>Atualizar um perfil de utilizador

Pode utilizar as seguintes informações para atualizar o perfil de um utilizador:

- [Portal do Azure](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

### <a name="reset-a-password-for-a-user"></a>Redefinir uma palavra-passe para um utilizador

Pode utilizar as seguintes informações para redefinir a palavra-passe de um utilizador:

- [Portal do Azure](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

## <a name="guest-user"></a>Utilizador convidado

Pode convidar utilizadores externos para o seu inquilino como utilizador convidado. Um cenário típico para convidar um utilizador convidado para o seu inquilino Azure AD B2C é partilhar responsabilidades administrativas. Para um exemplo de utilização de uma conta de hóspedes, consulte [propriedades de um utilizador de colaboração Azure Ative Directory B2B](../active-directory/external-identities/user-properties.md).

Quando convida um utilizador convidado para o seu inquilino, fornece o endereço de e-mail do destinatário e uma mensagem descrevendo o convite. O link de convite leva o utilizador à página de consentimento. Se uma caixa de entrada não estiver anexada ao endereço de e-mail, o utilizador pode navegar para a página de consentimento indo para uma página da Microsoft usando as credenciais convidadas. O utilizador é então obrigado a resgatar o convite da mesma forma que clicar no link no e-mail. Por exemplo: `https://myapps.microsoft.com/B2CTENANTNAME`.

Também pode utilizar a [API do Microsoft Graph](/graph/api/invitation-post?view=graph-rest-beta) para convidar um utilizador convidado.

## <a name="consumer-user"></a>Consumidor

O utilizador do consumidor pode iniciar seducação em aplicações protegidas pelo Azure AD B2C, mas não consegue aceder a recursos Azure, como o portal Azure. O consumidor pode utilizar uma conta local ou contas federadas, como o Facebook ou o Twitter. Uma conta de consumo é criada utilizando um [fluxo de utilizador de inscrição ou de entrada,](user-flow-overview.md)utilizando a API do Microsoft Graph ou utilizando o portal Azure.

Pode especificar os dados recolhidos quando uma conta de utilizador de consumo é criada. Para obter mais informações, consulte [Adicionar os atributos do utilizador e personalizar a entrada do utilizador.](configure-user-input.md)

Para obter mais informações sobre a gestão das contas dos consumidores, consulte [as contas de utilizadores do Manage AD B2C com o Microsoft Graph](./microsoft-graph-operations.md).

### <a name="migrate-consumer-user-accounts"></a>Migrar contas de utilizadores de consumidores

Poderá ter a necessidade de migrar as contas de utilizadores de consumidores existentes de qualquer fornecedor de identidade para Azure AD B2C. Para obter mais informações, veja [Migrar utilizadores para o AAD B2C](user-migration.md).