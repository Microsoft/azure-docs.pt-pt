---
title: Permissões no Azure Active Directory | Microsoft docs
description: Saiba mais sobre as permissões no Azure Active Directory e como utilizá-las.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 6c0dc122-2cd8-4d70-be5a-3943459d308e
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jesakowi, justhu
ms.custom: aaddev
ms.openlocfilehash: c13f4cabfbd996c1d811a8a3e35e69893c51154f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700928"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v10-endpoint"></a>Permissões e consentimento no Diretório Ativo Azure v1.0 endpoint

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

O Azure Active Directory (Azure AD) utiliza intensivamente as permissões tanto para fluxos de OAuth, como de OpenID Connect (OICD). Quando a sua aplicação recebe um token de acesso do Azure AD, o token de acesso irá incluir afirmações que descrevem as permissões que a aplicação tem relativamente a um determinado recurso.

*As permissões*, também conhecidas como *âmbitos*, facilitam a autorização para o recurso, uma vez que o recurso apenas precisa de verificar se o token contém a permissão adequada para qualquer API que a app esteja a chamar.

## <a name="types-of-permissions"></a>Tipos de permissões

O Azure AD define dois tipos de permissões:

* **Permissões delegadas** - são utilizadas por aplicações que têm um utilizador com sessão iniciada presente. Nestas aplicações, ou o utilizador ou um administrador autoriza as permissões que a aplicação pede e é delegada a esta permissão para agir como o utilizador com sessão iniciada quando forem feitas chamadas para uma API. Dependendo da API, o utilizador pode não ser capaz de consentir diretamente com a API e, em vez disso, exigiria que [um administrador fornecesse "consentimento administrativo".](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)
* **Permissões de aplicação** - são utilizadas por aplicações que são executadas sem um utilizador com sessão iniciada presente; por exemplo, as aplicações que são executadas como serviços em segundo plano ou daemons. As permissões de aplicação só podem ser [consentidas pelos administradores](/azure/active-directory/develop/active-directory-v2-scopes#requesting-consent-for-an-entire-tenant) porque são tipicamente poderosas e permitem o acesso a dados através dos limites do utilizador, ou dados que de outra forma seriam restritos aos administradores. Os utilizadores que sejam definidos como proprietários da aplicação de recursos (ou seja, a API que publica as permissões) também estão autorizados a conceder permissões de pedido para as APIs que possuem.

As permissões efetivas são aquelas que a sua aplicação terá quando fizer pedidos para uma API. 

* Relativamente às permissões delegadas, as permissões efetivas da aplicação serão o ponto comum com menos privilégios das permissões delegadas que a aplicação concedeu (através do consentimento) e dos privilégios do utilizador com sessão iniciada atualmente. A aplicação nunca pode ter mais privilégios do que o utilizador com sessão iniciada. Nas organizações, os privilégios do utilizador com sessão iniciada podem ser determinados por uma política ou por associação a uma ou mais funções de administrador. Para saber quais as funções de administrador que podem consentir com permissões delegadas, consulte [permissões de funções do Administrador em Azure AD](../users-groups-roles/directory-assign-admin-roles.md).
    Por exemplo, suponha foi foi concedida à sua aplicação a permissão delegada `User.ReadWrite.All` no Microsoft Graph. Esta permissão concede nominalmente permissão à aplicação para ler e atualizar o perfil de todos os utilizadores de uma organização. Se o utilizador com sessão iniciada for administrador global, a aplicação poderá atualizar o perfil de todos os utilizadores da organização. No entanto, se o utilizador com sessão iniciada não tiver uma função de administrador, a aplicação só poderá atualizar o perfil desse utilizador. Não poderá atualizar os perfis dos outros utilizadores da organização porque o utilizador em cujo nome tem permissão para agir não tem esses privilégios.
* Relativamente às permissões da aplicação, as permissões efetivas da sua aplicação são o nível completo de privilégios implícitos nas permissões. Por exemplo, uma aplicação que tenha a permissão de aplicação `User.ReadWrite.All` pode atualizar o perfil de cada utilizador da organização.

## <a name="permission-attributes"></a>Atributos das permissões
As permissões no Azure AD têm várias propriedades que ajudam os utilizadores, os administradores ou os programadores de aplicações a tomar decisões informadas relativamente ao que é que a permissão concede acesso.

> [!NOTE]
> Pode utilizar o portal do Azure ou o PowerShell para ver as permissões que uma Aplicação do Azure AD ou Principal de Serviço expõe. Experimente este script para ver as permissões que o Microsoft Graph expõe.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Nome da propriedade | Descrição | Exemplo |
| --- | --- | --- |
| `ID` | É um valor de GUID que identifica exclusivamente esta permissão. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca |
| `IsEnabled` | Indica se esta permissão está disponível para utilização. | true |
| `Type` | Indica se esta permissão requer consentimento do utilizador ou consentimento do administrador. | Utilizador |
| `AdminConsentDescription` | É uma descrição que é apresentada aos administradores durante as experiências de consentimento de administrador | Permite que a aplicação leia e-mails nas caixas de correio do utilizador. |
| `AdminConsentDisplayName` | É o nome amigável que é apresentado aos administradores durante a experiência de consentimento de administrador. | Ler o correio do utilizador |
| `UserConsentDescription` | É uma descrição que é apresentada aos utilizadores durante uma experiência de consentimento de utilizador. |  Permite que a aplicação leia e-mails na sua caixa de correio. |
| `UserConsentDisplayName` | É o nome amigável que é apresentado aos utilizadores durante uma experiência de consentimento de utilizador. | Ler o seu correio |
| `Value` | É a cadeia que é utilizada para identificar a permissão durante os fluxos de autorização de OAuth 2.0. `Value` também pode ser combinada com a cadeia de URI de ID da Aplicação, de modo a formar um nome de permissão completamente qualificado. | `Mail.Read` |

## <a name="types-of-consent"></a>Tipos de consentimentos

As aplicações no Azure AD dependem de consentimento para ter acesso a recursos ou APIs necessárias. Para ter êxito, a sua aplicação poderá ter de conhecer vários tipos de consentimentos. Se estiver a definir permissões, também terá de saber como é que os utilizadores vão obter acesso à sua aplicação ou API.

* **Consentimento de utilizador estático** - ocorre automaticamente durante o [fluxo de autorização de OAuth 2.0](v1-protocols-oauth-code.md#request-an-authorization-code) quando especifica o recurso com o qual a aplicação quer interagir. No cenário de consentimento de utilizador estático, é necessário que a aplicação já tenha especificado todas as permissões de que precisa na respetiva configuração no portal do Azure. Se o utilizador (ou o administrador, conforme apropriado) não tiver concedido consentimento para esta aplicação, o Azure AD pedirá ao utilizador para dar consentimento neste momento. 

    Saiba mais sobre como registar uma aplicação do Azure AD que pede acesso a um conjunto estático de APIs.
* **Consentimento de utilizador dinâmico** - é uma funcionalidade do modelo de aplicações do Azure AD v2. Neste cenário, a aplicação pede um conjunto de permissões de que precisa no [fluxo de autorização de OAuth 2.0 para aplicações de v2](/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent). Se o utilizador ainda não tiver consentido, ser-lhe-á pedido que consinta neste momento. [Saiba mais sobre o consentimento dinâmico](/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent).

    > [!IMPORTANT]
    > O consentimento dinâmico pode ser prático, mas apresenta um desafio grande para as permissões que precisam de consentimento de administrador, uma vez que a experiência deste tipo de consentimento desconhece essas permissões no momento do consentimento. Se necessitar de permissões privilegiadas ou se a sua aplicação utilizar consentimento dinâmico, deve registar todas as permissões no portal Azure (e não apenas o subconjunto de permissões que requerem consentimento administrativo). Isto permite aos administradores inquilinos consentir em nome de todos os seus utilizadores.
  
* **Consentimento de administrador** - é necessário quando a aplicação precisa de aceder a determinadas permissões de privilégio elevado. O consentimento do administrador garante que os administradores têm alguns controlos adicionais antes de autorizarem as aplicações ou os utilizadores a aceder a dados com privilégios elevados da organização. [Saiba mais sobre como conceder o consentimento de administrador](/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Melhores práticas

### <a name="client-best-practices"></a>Melhores práticas de cliente

- Apenas pedidos de permissão exigidos pela sua aplicação. As aplicações com demasiadas permissões estão em risco de expor os dados dos utilizadores se ficarem comprometidas.
- Escolha entre permissões delegadas e permissões de aplicação, com base no cenário que suporta a sua aplicação.
    - Utilize sempre as permissões delegadas se a chamada estiver a ser realizada em nome de um utilizador.
    - Utilize permissões de aplicação apenas se a aplicação não for interativa e não realizar chamadas em nome de qualquer utilizador específico. As permissões de aplicação são altamente privilegiadas e só devem ser utilizadas quando for realmente preciso.
- Quando utilizar uma aplicação baseada no ponto final v2.0, defina sempre as permissões estáticas (as que são especificadas no seu registo da aplicação) para serem o superconjunto das permissões dinâmicas que pede no runtime (as que são especificadas no código e enviadas como parâmetros de consulta no seu pedido de autorização), para que os cenários, como o consentimento de administrador, funcionem corretamente.

### <a name="resourceapi-best-practices"></a>Melhores práticas dos recursos/API

- Os recursos que expõem as APIs devem definir as permissões que são específicas para os dados ou ações que estejam a proteger. Seguir esta melhor prática ajuda-o a garantir que os clientes não acabam por ficar com a permissão para acederem a dados que não precisam e que os utilizadores são bem informados sobre os dados a que deram consentimento.
- Os recursos devem definir explicitamente as permissões `Read` e `ReadWrite` em separado.
- Os recursos devem marcar todas as permissões que permitem acesso aos dados em limites do utilizador como permissões `Admin`.
- Os recursos devem seguir o padrão de nomenclatura `Subject.Permission[.Modifier]`, em que:
  - `Subject` corresponde ao tipo de dados disponíveis
  - `Permission` corresponde à ação que um utilizador pode tomar sobre esses dados
  - `Modifier` é usado opcionalmente para descrever especializações de outra permissão
    
    Por exemplo:
  - Mail.Read - permite aos utilizadores lerem correio.
  - Mail.ReadWrite - permite aos utilizadores lerem ou escreverem correio.
  - Mail.ReadWrite.All - permite que um administrador ou utilizador aceda a todo o correio da organização.
