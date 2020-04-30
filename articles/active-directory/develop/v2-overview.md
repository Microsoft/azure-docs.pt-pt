---
title: Visão geral da plataforma de identidade da Microsoft (v2.0) - Azure
description: Conheça a plataforma e plataforma de fim da plataforma da plataforma da plataforma da plataforma da plataforma da plataforma da plataforma da plataforma da plataforma da microsoft (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 2e5bbbd311d71f2925e86ae756b36de7194aa9fb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80886250"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Visão geral da plataforma de identidade da Microsoft (v2.0)

A plataforma de identidade da Microsoft é uma evolução da plataforma de programador do Azure Active Directory (Azure AD). Permite que os desenvolvedores construam aplicações que assinam todas as identidades da Microsoft e que liguem para as APIs da Microsoft, como o Microsoft Graph, ou APIs que os desenvolvedores construíram. A plataforma de identidade da Microsoft consiste em:

- **OAuth 2.0 e OpenID Connect serviço de autenticação padrão** que permite aos desenvolvedores autenticar qualquer identidade da Microsoft, incluindo:
  - Trabalho ou contas escolares (provisionados através da AD Azure)
  - Contas pessoais da Microsoft (como Skype, Xbox e Outlook.com)
  - Contas sociais ou locais (via Azure AD B2C)
- **Bibliotecas de código aberto**: Bibliotecas de Autenticação da Microsoft (MSAL) e suporte a outras bibliotecas compatíveis com padrões
- Portal de gestão de **aplicações**: Uma experiência de registo e configuração construída no portal Azure, juntamente com todas as suas outras capacidades de gestão Azure.
- **Configuração de aplicação API e PowerShell**: que permite a configuração programática das suas aplicações através do Microsoft Graph API e PowerShell, para que possa automatizar as suas tarefas DevOps.
- **Conteúdo do desenvolvedor**: documentação conceptual e de referência, amostras de arranque rápido, amostras de código, tutoriais e guias de como fazer.

Para os desenvolvedores, a plataforma de identidade da Microsoft oferece uma integração perfeita em inovações no espaço de identidade e segurança, tais como autenticação sem palavras-passe, autenticação step-up e Acesso Condicional.  Não é preciso implementar essa funcionalidade por si mesmo: aplicações integradas na plataforma de identidade da Microsoft aproveitam-se de tais inovações.

Com a plataforma de identidade da Microsoft, pode escrever código uma vez e contactar qualquer utilizador. Pode construir uma aplicação uma vez e fazê-la funcionar em muitas plataformas, ou construir uma app que funcione como cliente, bem como uma aplicação de recursos (API).

## <a name="getting-started"></a>Introdução

Trabalhar com a identidade não tem de ser tão difícil. 

Veja um vídeo da [plataforma de identidade da Microsoft](identity-videos.md) para saber o básico. 

Escolha um [cenário](authentication-flows-app-scenarios.md) que se aplique a si: cada caminho de cenário tem um início rápido e uma página de visão geral para fazê-lo funcionar em minutos:

- [Criar uma aplicação de página única](scenario-spa-overview.md)
- [Criar uma aplicação Web que inicia a sessão dos utilizadores](scenario-web-app-sign-user-overview.md)
- [Criar uma aplicação Web que chama APIs Web](scenario-web-app-call-api-overview.md)
- [Criar uma API Web protegida](scenario-protected-web-api-overview.md)
- [Construa uma API web que chama APIs web](scenario-web-api-call-api-overview.md)
- [Construa uma aplicação para desktop](scenario-desktop-overview.md)
- [Construa uma app daemon](scenario-daemon-overview.md)
- [Criar uma aplicação móvel](scenario-mobile-overview.md)

O gráfico seguinte descreve cenários comuns de aplicações de autenticação – use-a como referência ao integrar a plataforma de identidade da Microsoft com a sua aplicação.

[![Cenários de aplicação na plataforma de identidade da Microsoft](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre conceitos de autenticação core, recomendamos que comece com estes tópicos:

- [Fluxos de autenticação e cenários de aplicação](authentication-flows-app-scenarios.md)
- [Noções básicas sobre autenticação](authentication-scenarios.md)
- [Diretores de aplicação e serviço](app-objects-and-service-principals.md)
- [Público](v2-supported-account-types.md)
- [Permissões e consentimento](v2-permissions-and-consent.md)
- [Fichas de identificação](id-tokens.md) e [fichas](access-tokens.md) de acesso

Construa uma aplicação rica em dados que chama [Microsoft Graph](https://docs.microsoft.com/graph/overview).

Quando estiver pronto para lançar a sua app num ambiente de **produção,** reveja estas boas práticas:

- [Ative](msal-logging.md) o registo na sua aplicação.
- Ativar a telemetria na sua aplicação.
- Ative [proxies e personalize clientes HTTP.](msal-net-provide-httpclient.md)
- Teste a sua integração seguindo a lista de verificação de [integração](identity-platform-integration-checklist.md)da plataforma de identidade da Microsoft .

## <a name="learn-more"></a>Saiba mais

Se está a planear construir uma aplicação virada para o cliente que assina em identidades sociais e locais, consulte a visão geral do [Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
