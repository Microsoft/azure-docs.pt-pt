---
title: Visão geral das contas dos utilizadores no Diretório Ativo Azure B2C
description: Conheça os tipos de contas de utilizador que podem ser utilizadas no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9630e1f23b6595ca690ecafcf0c4b9bfff603f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185663"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Visão geral das contas dos utilizadores no Diretório Ativo Azure B2C

No Azure Ative Directory B2C (Azure AD B2C), existem vários tipos de contas que podem ser criadas. O Diretório Ativo Azure, o Diretório Ativo B2B e o Diretório Ativo B2C partilham os tipos de contas de utilizador que podem ser utilizadas.

Estão disponíveis os seguintes tipos de contas:

- **Conta** de trabalho - Uma conta de trabalho pode aceder a recursos num inquilino, e com uma função de administrador, pode gerir os inquilinos.
- Conta de **hóspedes** - Uma conta de hóspedes só pode ser uma conta Microsoft ou um utilizador do Azure Ative Directory que pode ser usado para aceder a aplicações ou gerir inquilinos.
- **Conta** de consumo - Uma conta de consumo é utilizada por um utilizador das aplicações que registou no Azure AD B2C. As contas dos consumidores podem ser criadas por:
  - O utilizador que passa por um fluxo de utilizador de inscrição numa aplicação Azure AD B2C
  - Usando a API do Microsoft Graph
  - Utilizar o portal do Azure

## <a name="work-account"></a>Conta de trabalho

Uma conta de trabalho é criada da mesma forma para todos os inquilinos com base em Azure AD. Para criar uma conta de trabalho, pode utilizar a informação em [Quickstart: Adicionar novos utilizadores ao Azure Ative Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). É criada uma conta de trabalho utilizando a nova escolha **do utilizador** no portal Azure.

Quando adicionar uma nova conta de trabalho, tem de considerar as seguintes definições de configuração:

- **Nome** e Nome do **Utilizador** - A propriedade **Nome** contém o dado e sobrenome do utilizador. O **nome Utilizador** é o identificador que o utilizador introduz para iniciar sessão. O nome do utilizador inclui o domínio completo. A porção de nome de domínio do nome do utilizador deve ser o nome de domínio padrão inicial *your-domain.onmicrosoft.com,* ou um nome de [domínio personalizado](../active-directory/fundamentals/add-custom-domain.md) verificado e não federado, como *contoso.com*.
- **Perfil** - A conta é configurada com um perfil de dados do utilizador. Tem a oportunidade de inserir um primeiro nome, apelido, cargo e nome de departamento. Pode editar o perfil após a criação da conta.
- **Grupos** - Utilize um grupo para executar tarefas de gestão, tais como a atribuição de licenças ou permissões a vários utilizadores ou dispositivos ao mesmo tempo. Você pode colocar a nova conta em um [grupo](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) existente no seu inquilino.
- Papel de **diretório** - Você precisa especificar o nível de acesso que a conta de utilizador tem aos recursos do seu inquilino. Estão disponíveis os seguintes níveis de permissão:

    - **Utilizador** - Os utilizadores podem aceder aos recursos atribuídos, mas não conseguem gerir a maioria dos recursos dos inquilinos.
    - **Administrador global** - Os administradores globais têm total controlo sobre todos os recursos dos inquilinos.
    - **Administrador limitado** - Selecione as funções ou funções administrativas para o utilizador. Para obter mais informações sobre as funções que podem ser selecionadas, consulte as funções de [administrador de atribuição no Diretório Ativo azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="create-a-work-account"></a>Criar uma conta de trabalho

Pode utilizar as seguintes informações para criar uma nova conta de trabalho:

- [Portal Azure](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Atualizar um perfil de utilizador

Pode utilizar as seguintes informações para atualizar o perfil de um utilizador:

- [Portal Azure](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Redefinir uma palavra-passe para um utilizador

Pode utilizar as seguintes informações para redefinir a palavra-passe de um utilizador:

- [Portal Azure](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Utilizador convidado

Pode convidar utilizadores externos para o seu inquilino como utilizador convidado. Um cenário típico para convidar um utilizador convidado para o seu inquilino Azure AD B2C é partilhar responsabilidades administrativas. Para um exemplo de utilização de uma conta de hóspedes, consulte [Propriedades de um utilizador de colaboração Azure Ative Directory B2B](../active-directory/b2b/user-properties.md).

Quando convida um utilizador convidado para o seu inquilino, fornece o endereço de e-mail do destinatário e uma mensagem descrevendo o convite. O link de convite leva o utilizador à página de consentimento onde é selecionado o botão **Get Started** e a revisão das permissões é aceite. Se uma caixa de entrada não estiver anexada ao endereço de e-mail, o utilizador pode navegar para a página de consentimento indo para uma página da Microsoft usando as credenciais convidadas. O utilizador é então obrigado a resgatar o convite da mesma forma que clicar no link no e-mail. Por exemplo: `https://myapps.microsoft.com/B2CTENANTNAME`.

Também pode utilizar a [API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) do Microsoft Graph para convidar um utilizador convidado.

## <a name="consumer-user"></a>Utilizador de consumo

O utilizador pode iniciar sessão em aplicações garantidas pelo Azure AD B2C, mas não pode aceder a recursos Azure, como o portal Azure. O utilizador pode utilizar uma conta local ou contas federadas, como facebook ou Twitter. Uma conta de consumo é criada utilizando um fluxo de utilizador de [inscrição ou de entrada,](user-flow-overview.md)utilizando a API do Microsoft Graph, ou utilizando o portal Azure.

Pode especificar os dados recolhidos quando uma conta de utilizador é criada utilizando atributos de utilizador personalizados. Para mais informações, consulte [Definir atributos personalizados no Diretório Ativo Azure B2C](user-flow-custom-attributes.md).

Para obter mais informações sobre a gestão das contas de consumo, consulte gerir as contas de [utilizador do AD B2C do Manage Azure com](manage-user-accounts-graph-api.md)o Microsoft Graph .

### <a name="migrate-consumer-user-accounts"></a>Migrar contas de utilizadores de consumo

Poderá ter a necessidade de migrar as contas de utilizador de consumo existentes de qualquer fornecedor de identidade para o Azure AD B2C. Para mais informações, consulte [os utilizadores migrate para O AD B2C](user-migration.md).
