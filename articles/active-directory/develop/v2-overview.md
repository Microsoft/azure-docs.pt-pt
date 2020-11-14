---
title: Visão geral da plataforma de identidade da Microsoft - Azure
titleSuffix: Microsoft identity platform
description: Conheça os componentes da plataforma de identidade da Microsoft e como podem ajudá-lo a construir suporte à gestão de identidade e acesso (IAM) nas suas aplicações.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40, contperfq2
ms.openlocfilehash: 3f18913bb0d05c45ae57152d438b229b8bcc1d48
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94627919"
---
# <a name="what-is-the-microsoft-identity-platform"></a>O que é a plataforma de identidades da Microsoft?

A plataforma de identidade da Microsoft ajuda-o a construir aplicações que os seus utilizadores e clientes podem iniciar sação para utilizar as suas identidades ou contas sociais da Microsoft, e fornecer acesso autorizado às suas próprias APIs ou APIs da Microsoft como o Microsoft Graph.

Existem vários componentes que compõem a plataforma de identidade da Microsoft:

- **OAuth 2.0 e OpenID Connect Serviço de autenticação padrão que** permite aos desenvolvedores autenticar vários tipos de identidade, incluindo:
  - Contas de trabalho ou escola, a provisionadas através da Azure AD
  - Conta pessoal da Microsoft, como Skype, Xbox e Outlook.com
  - Contas sociais ou locais, utilizando o Azure AD B2C
- **Bibliotecas de código aberto** : Bibliotecas de autenticação da Microsoft (MSAL) e suporte para outras bibliotecas compatíveis com padrões
- **Portal de gestão de aplicações** : Uma experiência de registo e configuração no portal Azure, juntamente com as outras capacidades de gestão do Azure.
- **Configuração de aplicação API e PowerShell** : Configuração programática das suas aplicações através da Microsoft Graph API e PowerShell para que possa automatizar as suas tarefas DevOps.
- **Conteúdo do programador** : Documentação técnica incluindo quickstarts, tutoriais, guias e amostras de código.

Para os desenvolvedores, a plataforma de identidade da Microsoft oferece a integração de inovações modernas no espaço de identidade e segurança, como autenticação sem palavras-passe, autenticação intensificada e Acesso Condicional. Não precisa de implementar essa funcionalidade por si mesmo: as aplicações integradas na plataforma de identidade da Microsoft aproveitam-se de forma nativa dessas inovações.

Com a plataforma de identidade da Microsoft, pode escrever código uma vez e chegar a qualquer utilizador. Pode construir uma aplicação uma vez e fazê-la funcionar em muitas plataformas, ou construir uma app que funcione como cliente, bem como uma aplicação de recursos (API).

## <a name="getting-started"></a>Introdução

Escolha o cenário de [candidatura](authentication-flows-app-scenarios.md) que gostaria de construir. Cada um destes caminhos de cenário começa com uma visão geral e liga-se a um arranque rápido para ajudá-lo a levantar-se e a correr:

- [Aplicação de página única (SPA)](scenario-spa-overview.md)
- [Aplicação web que inicia a sessão de utilizadores](scenario-web-app-sign-user-overview.md)
- [Aplicação Web que chama as APIs Web](scenario-web-app-call-api-overview.md)
- [API web protegida](scenario-protected-web-api-overview.md)
- [API Web que chama APIs Web](scenario-web-api-call-api-overview.md)
- [Aplicação de ambiente de trabalho](scenario-desktop-overview.md)
- [Aplicações daemon](scenario-daemon-overview.md)
- [Aplicação móvel](scenario-mobile-overview.md)

À medida que trabalha com a plataforma de identidade da Microsoft para integrar a autenticação e autorização nas suas apps, pode consultar esta imagem que descreve os cenários de aplicações mais comuns e os seus componentes de identidade. Selecione a imagem para vê-la em tamanho real.

[![Mapa do metrô mostrando vários cenários de aplicação na plataforma de identidade da Microsoft](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>Aprenda conceitos de autenticação

Saiba como a autenticação central e os conceitos AD do Azure se aplicam à plataforma de identidade da Microsoft neste conjunto recomendado de artigos:

- [Noções básicas sobre autenticação](./authentication-vs-authorization.md)
- [Diretores de aplicação e serviços](app-objects-and-service-principals.md)
- [Audiências](v2-supported-account-types.md)
- [Permissões e consentimento](v2-permissions-and-consent.md)
- [Tokens de ID](id-tokens.md)
- [Tokens de acesso](access-tokens.md)
- [Fluxos de autenticação e cenários de aplicação](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>Mais opções de gestão de identidade e acesso

[Azure AD B2C](../../active-directory-b2c/overview.md) - Construa aplicações voltadas para o cliente que os seus utilizadores podem iniciar súb9 para usar as suas contas sociais como facebook ou Google, ou usando um endereço de e-mail e senha.

[Azure AD B2B](../external-identities/what-is-b2b.md) - Convide os utilizadores externos para o seu inquilino Azure AD como utilizadores "convidados" e atribua permissões de autorização enquanto utilizam as suas credenciais existentes para autenticação.

[Azure Ative Directory para desenvolvedores (v1.0)](../azuread-dev/v1-overview.md) - Mostrado aqui para programadores com aplicações existentes que usam o ponto final v1.0 mais antigo. **Não** utilize v1.0 para novos projetos.

## <a name="next-steps"></a>Passos seguintes

Se você tem uma conta Azure você já tem acesso a um inquilino do Azure Ative Directory, mas a maioria dos desenvolvedores de plataformas de identidade da Microsoft precisam do seu próprio inquilino AZure AD para uso enquanto desenvolve aplicações, um "dev inquilino".

Saiba como criar o seu próprio inquilino para uso enquanto constrói as suas aplicações:

[Quickstart: Crie um inquilino AZure AD](quickstart-create-new-tenant.md)
