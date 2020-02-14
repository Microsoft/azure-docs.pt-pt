---
title: Visão geral da plataforma Microsoft Identity (v 2.0) – Azure
description: Saiba mais sobre a plataforma e o ponto de extremidade da plataforma Microsoft Identity (v 2.0).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6280f3784247b671521a4994be1f233d4913d90c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77194505"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Visão geral da plataforma Microsoft Identity (v 2.0)

A plataforma de identidade da Microsoft é uma evolução da plataforma de programador do Azure Active Directory (Azure AD). Ele permite que os desenvolvedores criem aplicativos que se conectam a todas as identidades da Microsoft e obtenham tokens para chamar APIs da Microsoft, como Microsoft Graph ou APIs que os desenvolvedores criaram. A plataforma de identidade da Microsoft consiste em:

- **OAuth 2.0 e OpenID Connect serviço de autenticação padrão** que permite aos desenvolvedores autenticar qualquer identidade da Microsoft, incluindo:
  - Contas corporativas ou de estudante (provisionadas por meio do Azure AD)
  - Contas pessoais da Microsoft (como Skype, Xbox e Outlook.com)
  - Contas sociais ou locais (via Azure AD B2C)
- **Bibliotecas de código aberto**: Bibliotecas de Autenticação da Microsoft (MSAL) e suporte a outras bibliotecas compatíveis com padrões
- Portal de gestão de **aplicações**: Uma experiência de registo e configuração construída no portal Azure, juntamente com todas as suas outras capacidades de gestão Azure.
- **Configuração de aplicação API e PowerShell**: que permite configuração programática das suas aplicações através da REST API (Microsoft Graph e Azure Ative Directory Graph 1.6) e PowerShell, para que possa automatizar as suas tarefas DevOps.
- **Conteúdo do desenvolvedor**: documentação conceptual e de referência, amostras de arranque rápido, amostras de código, tutoriais e guias de como fazer.

Para os desenvolvedores, a plataforma de identidade da Microsoft oferece integração direta a inovações no espaço de segurança e identidade, como autenticação sem senha, autenticação de etapa para e acesso condicional.  Você não precisa implementar essa funcionalidade por conta própria: aplicativos integrados com a plataforma de identidade da Microsoft aproveitam de forma nativa essas inovações.

Com a plataforma de identidade da Microsoft, você pode escrever código uma vez e acessar qualquer usuário. Você pode criar um aplicativo uma vez e fazê-lo funcionar em várias plataformas ou criar um aplicativo que funcione como um cliente, bem como um aplicativo de recurso (API).

## <a name="getting-started"></a>Introdução

Trabalhar com a identidade não tem de ser tão difícil. 

Veja um vídeo da [plataforma de identidade da Microsoft](identity-videos.md) para saber o básico. 

Escolha um [cenário](authentication-flows-app-scenarios.md) que se aplique a si: cada caminho de cenário tem um início rápido e uma página de visão geral para fazê-lo funcionar em minutos:

- [Construa uma aplicação de uma página única](scenario-spa-overview.md)
- [Construa uma aplicação web que assina nos utilizadores](scenario-web-app-sign-user-overview.md)
- [Construa uma aplicação web que chama APIs web](scenario-web-app-call-api-overview.md)
- [Construir uma API web protegida](scenario-protected-web-api-overview.md)
- [Construa uma API web que chama APIs web](scenario-web-api-call-api-overview.md)
- [Construa uma aplicação para desktop](scenario-desktop-overview.md)
- [Construa uma app daemon](scenario-daemon-overview.md)
- [Construir uma aplicação móvel](scenario-mobile-overview.md)

O gráfico a seguir descreve os cenários de aplicativos de autenticação comuns – use-os como referência ao integrar a plataforma de identidade da Microsoft ao seu aplicativo.

[cenários de aplicação ![na plataforma de identidade da Microsoft](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Passos seguintes

Se você quiser saber mais sobre os principais conceitos de autenticação, recomendamos que comece com estes tópicos:

- [Fluxos de autenticação e cenários de aplicação](authentication-flows-app-scenarios.md)
- [Noções básicas de autenticação](authentication-scenarios.md)
- [Diretores de aplicação e serviço](app-objects-and-service-principals.md)
- [Público](v2-supported-account-types.md)
- [Permissões e consentimento](v2-permissions-and-consent.md)
- [Fichas de identificação](id-tokens.md) e [fichas](access-tokens.md) de acesso

Construa uma aplicação rica em dados que chama [Microsoft Graph](https://docs.microsoft.com/graph/overview).

Quando estiver pronto para lançar a sua app num ambiente de **produção,** reveja estas boas práticas:

- [Ative](msal-logging.md) o registo na sua aplicação.
- Habilite a telemetria em seu aplicativo.
- Ative [proxies e personalize clientes HTTP.](msal-net-provide-httpclient.md)
- Teste a sua integração seguindo a lista de verificação de [integração](identity-platform-integration-checklist.md)da plataforma de identidade da Microsoft .

## <a name="learn-more"></a>Saiba mais

Se está a planear construir uma aplicação virada para o cliente que assina em identidades sociais e locais, consulte a visão geral do [Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
