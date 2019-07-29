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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12027581ae62588550407c8350f3b74f1e743561
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601837"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Visão geral da plataforma Microsoft Identity (v 2.0)

A plataforma de identidade da Microsoft é uma evolução da plataforma de programador do Azure Active Directory (Azure AD). Ele permite que os desenvolvedores criem aplicativos que se conectam a todas as identidades da Microsoft e obtenham tokens para chamar APIs da Microsoft, como Microsoft Graph ou APIs que os desenvolvedores criaram. A plataforma de identidade da Microsoft consiste em:

- O **serviço de autenticação compatível com o OAuth 2,0 e o OpenID Connect Standard** permite que os desenvolvedores autentiquem qualquer identidade da Microsoft, incluindo:
  - Contas corporativas ou de estudante (provisionadas por meio do Azure AD)
  - Contas pessoais da Microsoft (como Skype, Xbox e Outlook.com)
  - Contas sociais ou locais (via Azure AD B2C)
- **Bibliotecas de código-fonte aberto**: MSAL (bibliotecas de autenticação da Microsoft) e suporte para outras bibliotecas em conformidade com os padrões
- **Portal de gerenciamento de aplicativos**: Uma experiência de registro e configuração criada no portal do Azure, juntamente com todos os outros recursos de gerenciamento do Azure.
- **API de configuração de aplicativo e PowerShell**: que permite a configuração programática de seus aplicativos por meio da API REST (Microsoft Graph e Azure Active Directory grafo 1,6) e do PowerShell, para que você possa automatizar suas tarefas de DevOps.
- **Conteúdo do desenvolvedor**: documentação conceitual e de referência, exemplos de início rápido, exemplos de código, tutoriais e guias de instruções.

Para os desenvolvedores, a plataforma de identidade da Microsoft oferece integração direta a inovações no espaço de segurança e identidade, como autenticação sem senha, autenticação de etapa para e acesso condicional.  Você não precisa implementar essa funcionalidade por conta própria: aplicativos integrados com a plataforma de identidade da Microsoft aproveitam de forma nativa essas inovações.

Com a plataforma de identidade da Microsoft, você pode escrever código uma vez e acessar qualquer usuário. Você pode criar um aplicativo uma vez e fazê-lo funcionar em várias plataformas ou criar um aplicativo que funcione como um cliente, bem como um aplicativo de recurso (API).

## <a name="getting-started"></a>Introdução

Trabalhar com a identidade não tem de ser tão difícil. Escolha um [cenário](authentication-flows-app-scenarios.md) que se aplica a você — cada caminho de cenário tem um início rápido e uma página de visão geral para colocar em funcionamento em minutos:

- [Criar um aplicativo de página única](scenario-spa-overview.md)
- [Criar um aplicativo Web que faz logon em usuários](scenario-web-app-sign-user-overview.md)
- [Compilar um aplicativo Web que chama APIs da Web](scenario-web-app-call-api-overview.md)
- [Criar uma API Web protegida](scenario-protected-web-api-overview.md)
- [Criar uma API Web que chama APIs da Web](scenario-web-api-call-api-overview.md)
- [Compilar um aplicativo de desktop](scenario-desktop-overview.md)
- [Criar um aplicativo daemon](scenario-daemon-overview.md)
- [Compilar um aplicativo móvel](scenario-mobile-overview.md)

O gráfico a seguir descreve os cenários de aplicativos de autenticação comuns – use-os como referência ao integrar a plataforma de identidade da Microsoft ao seu aplicativo.

[![Cenários de aplicativos na plataforma Microsoft Identity](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Passos Seguintes

Se você quiser saber mais sobre os principais conceitos de autenticação, recomendamos que comece com estes tópicos:

- [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
- [Noções básicas de autenticação](authentication-scenarios.md)
- [Entidades de serviço e aplicativo](app-objects-and-service-principals.md)
- [Compostos](v2-supported-account-types.md)
- [Permissões e consentimento](v2-permissions-and-consent.md)
- [Tokens de ID](id-tokens.md) e tokens de [acesso](access-tokens.md)

Crie um aplicativo com dados ricos que chama [Microsoft Graph](https://docs.microsoft.com/graph/overview).

Quando estiver pronto para iniciar seu aplicativo em um **ambiente de produção**, examine estas práticas recomendadas:

- [Habilite o registro em log](msal-logging.md) em seu aplicativo.
- Habilite a telemetria em seu aplicativo.
- Habilite [proxies e personalize clientes http](msal-net-provide-httpclient.md).
- Teste sua integração seguindo a [lista de verificação de integração da plataforma de identidade da Microsoft](identity-platform-integration-checklist.md).

## <a name="learn-more"></a>Saber mais

Se você estiver planejando criar um aplicativo voltado para o cliente que assine identidades sociais e locais, consulte a [visão geral de Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
