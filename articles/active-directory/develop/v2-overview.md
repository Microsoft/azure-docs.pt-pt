---
title: Microsoft identity (v2.0) visão geral da plataforma - Azure
description: Saiba mais sobre a plataforma e o ponto de extremidade do Microsoft identity platform (v2.0).
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: celested
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6aa0af1ff5a8600f43ab92a5f2534a8e3ff253c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067672"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Visão geral do Microsoft identity platform (v2.0)

A plataforma de identidade da Microsoft é uma evolução da plataforma para programadores e do serviço de identidade do Azure Active Directory (Azure AD). Ele permite que os desenvolvedores criem aplicativos que iniciar sessão em todas as identidades da Microsoft e obtém tokens para chamar as APIs da Microsoft, como o Microsoft Graph ou as APIs que os desenvolvedores criaram. A plataforma de identidade da Microsoft consiste em:

- **Serviço de autenticação compatíveis com o padrão de OAuth 2.0 e OpenID Connect** que permite aos desenvolvedores autenticar qualquer identidade da Microsoft, incluindo:
  - Contas escolares ou profissionais (aprovisionadas através do Azure AD)
  - Contas Microsoft pessoais (por exemplo, o Skype, Xbox e Outlook.com)
  - Contas de redes sociais ou locais (através do Azure AD B2C)
- **Bibliotecas de código-fonte aberto**: Bibliotecas de autenticação da Microsoft (MSAL) e suporte para outras bibliotecas compatíveis com padrões
- **Portal de gestão de aplicações**: Uma experiência de configuração e de registo criada no portal do Azure, juntamente com todas as suas outras funcionalidades de gestão do Azure.
- **Configuração da aplicação API e PowerShell**: que permite que a configuração programática das suas aplicações através de REST API (Microsoft Graph e do Azure Active Directory Graph 1.6) e o PowerShell, para que pode automatizar suas tarefas de DevOps.
- **Conteúdo para desenvolvedores**: conceitual e fazer referência a documentação, exemplos de início rápido, exemplos de código, tutoriais e guias de procedimentos.

Para os desenvolvedores, a plataforma de identidade da Microsoft oferece integração direta com as inovações no espaço de identidade e segurança, tais como autenticação de início, a autenticação Step-up e acesso condicional.  Não é necessário implementar essa funcionalidade por conta própria: aplicações integradas com a plataforma de identidade do Microsoft nativamente tirar partido dessas inovações.

Com a plataforma de identidade da Microsoft, pode escrever código uma vez e chegue a qualquer utilizador. Pode criar uma aplicação uma vez e fazer com que ele funciona em muitas plataformas ou criar uma aplicação que funciona como um cliente, bem como uma aplicação de recurso (API).

## <a name="getting-started"></a>Introdução

Trabalhar com a identidade não precisa ser difícil. Escolha um cenário que se aplica a si — cada caminho de cenário tem um início rápido e uma página de descrição geral para ajudá-lo em funcionamento em minutos:

- [Criar uma aplicação de página única](scenario-spa-overview.md)
- [Criar uma aplicação web que inicia sessão dos utilizadores](scenario-web-app-sign-user-overview.md)
- [Criar uma aplicação web que chama a APIs web](scenario-web-app-call-api-overview.md)
- [Criar uma API web protegida](scenario-protected-web-api-overview.md)
- [Compilar uma web API, que chama a APIs web](scenario-web-api-call-api-overview.md)
- [Criar uma aplicação de ambiente de trabalho](scenario-desktop-overview.md)
- [Criar uma aplicação de daemon](scenario-daemon-overview.md)
- [Crie uma aplicação móvel](scenario-mobile-overview.md)

<!--- We are making updates to the application scenarios chart. This is placeholder text for it.

The following chart outlines the full set of authentication scenarios and best practices – use it as a reference when integrating the Microsoft identity platform before shipping your app.

[![Application scenarios in Microsoft identity platform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

--->

## <a name="next-steps"></a>Passos Seguintes

Se gostaria de saber mais sobre conceitos de autenticação de principais, recomendamos que comece com estes tópicos:

- [Noções básicas de autenticação](authentication-scenarios.md)
- [Aplicação e os principais de serviço](app-objects-and-service-principals.md)
- [públicos-alvo](v2-supported-account-types.md)
- [Permissões e consentimento](v2-permissions-and-consent.md)
- [Tokens de ID](id-tokens.md) e [tokens de acesso](access-tokens.md)

Criar uma aplicação de dados sofisticados que chama [Microsoft Graph](https://docs.microsoft.com/graph/overview).

Quando estiver pronto para iniciar a sua aplicação para um **ambiente de produção**, reveja estas melhores práticas:

- [Ativar o registo](msal-logging.md) em seu aplicativo.
- Ative a telemetria na sua aplicação.
- Ativar [proxies e personalizar clientes HTTP](msal-net-provide-httpclient.md).
- Testar a sua integração ao seguir a [lista de verificação do Microsoft identity platform integração](identity-platform-integration-checklist.md).

## <a name="learn-more"></a>Saiba mais

Se tivesse de planeamento para criar uma aplicação do lado do cliente que inicia sessão identidades sociais e locais, veja a [descrição geral do Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).