---
title: Evolução da plataforma de identidade da Microsoft - Azure
description: Conheça a plataforma de identidade da Microsoft, uma evolução do serviço de identidade Azure Ative Directory (Azure AD) e plataforma de desenvolvimento.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.openlocfilehash: 8714b7a96197cb4a59b29bada31b5559961bf8e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78300218"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Evolução da plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft é uma evolução da plataforma de programador do Azure Active Directory (Azure AD). Permite que os desenvolvedores construam aplicações que assinam nos utilizadores, obter fichas para chamar APIs, como o Microsoft Graph, ou APIs que os desenvolvedores construíram. É composto por um serviço de autenticação, bibliotecas de código aberto, registo de aplicações e configuração (através de um portal de desenvolvimento e aplicação API), documentação completa do desenvolvedor, amostras de arranque rápido, amostras de código, tutoriais, guias de como fazer, outros conteúdos de desenvolvedor. A plataforma de identidades da Microsoft suporta protocolos norma da indústria, tais como OAuth 2.0 e o OpenID Connect.

Até agora, a maioria dos desenvolvedores tem trabalhado com a plataforma Azure AD v1.0 para autenticar trabalho e contas escolares (provisionadas pela Azure AD) solicitando fichas do ponto final da AD Azure v1.0, utilizando a Biblioteca de Autenticação Azure AD (ADAL), portal Azure para registo e configuração de aplicações e a API do Microsoft Graph para configuração programática de aplicações.

Com a plataforma de identidade unificada da Microsoft (v2.0), pode escrever código uma vez e autenticar qualquer identidade da Microsoft na sua aplicação. Para várias plataformas, a Biblioteca de Autenticação Microsoft (MSAL) totalmente suportada é recomendada para utilização contra os pontos finais da plataforma de identidade. O MSAL é simples de usar, fornece grandes experiências de inscrição única (SSO) para os seus utilizadores, ajuda-o a alcançar alta fiabilidade e desempenho, e é desenvolvido usando o Microsoft Secure Development Lifecycle (SDL). Ao ligar para APIs, pode configurar a sua aplicação para tirar partido do consentimento incremental, o que lhe permite atrasar o pedido de consentimento para âmbitos mais invasivos até que o uso da aplicação o justifique no prazo de execução.  A MSAL também suporta o Azure Ative Directory B2C, pelo que os seus clientes utilizam as suas identidades de conta social, empresarial ou local preferenciais para obter acesso único às suas aplicações e APIs.

Com a plataforma de identidade da Microsoft, expanda o seu alcance para este tipo de utilizadores:

- Contas de trabalho e escola (contas ads do Azure)
- Contas pessoais (tais como Outlook.com ou Hotmail.com)
- Os seus clientes que trazem o seu próprio e-mail ou identidade social (como LinkedIn, Facebook, Google) via MSAL e Azure AD B2C

Pode utilizar o portal Azure para registar e configurar a sua aplicação e utilizar a API do Microsoft Graph para configuração programática de aplicações.

Atualize a sua aplicação ao seu ritmo. As aplicações construídas com bibliotecas ADAL continuam a ser apoiadas. São também apoiadas as carteiras mistas de aplicações, que consistem em aplicações construídas com ADAL e aplicações construídas com bibliotecas MSAL. Isto significa que as aplicações que utilizam o mais recente ADAL e o mais recente MSAL irão entregar SSO em todo o portfólio, fornecido pela cache simbólica partilhada entre estas bibliotecas. As aplicações atualizadas da ADAL para a MSAL manterão o estado de inscrição do utilizador após a atualização.

## <a name="microsoft-identity-platform-experience"></a>Experiência da plataforma de identidade da Microsoft

O diagrama seguinte mostra a experiência de identidade da Microsoft a um nível elevado, incluindo a experiência de registo de aplicações, SDKs, pontos finais e identidades suportadas.

![A plataforma de identidade da Microsoft hoje](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Experiência de registo de aplicativos

A experiência de **[registos](https://go.microsoft.com/fwlink/?linkid=2083908)** do portal Azure App é a experiência de um portal para gerir todas as aplicações que integrou com a plataforma de identidade da Microsoft. Se tiver vindo a utilizar o Portal de Registo de Aplicações, comece a utilizar a experiência de registo de aplicações do portal Azure.

Para integração com o Azure AD B2C (ao autenticar identidades sociais ou locais), terá de registar a sua candidatura num inquilino Azure AD B2C. Esta experiência também faz parte do portal Azure.

Utilize a API da [aplicação](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0) para configurar programáticamente as suas aplicações integradas na plataforma de identidade da Microsoft para autenticar qualquer identidade da Microsoft.

### <a name="msal-libraries"></a>Bibliotecas MSAL

Pode utilizar a biblioteca MSAL para construir aplicações que autenticam todas as identidades da Microsoft. As bibliotecas MSAL em .NET e JavaScript estão geralmente disponíveis. As bibliotecas MSAL para iOS e Android estão em pré-visualização e adequadas para serem usadas num ambiente de produção. Fornecemos o mesmo suporte de nível de produção para bibliotecas MSAL em pré-visualização como fazemos para versões de MSAL e ADAL que estão geralmente disponíveis.

Também pode utilizar as bibliotecas MSAL para integrar a sua aplicação com o Azure AD B2C.

Bibliotecas do lado do servidor para a construção de aplicações web e APIs web estão geralmente disponíveis: [ASP.NET](https://docs.microsoft.com/aspnet/overview) e [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Ponto final da plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft (v2.0) endpoint está agora certificada pela OIDC. Funciona com as Bibliotecas de Autenticação da Microsoft (MSAL) ou com qualquer outra biblioteca compatível com padrões. Implementa âmbitos legíveis humanos, de acordo com os padrões da indústria.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre v1.0 e v2.0.

* [Visão geral da plataforma de identidade da Microsoft (v2.0)](v2-overview.md)
* [Diretório Ativo Azure para programadores (v1.0) visão geral](../azuread-dev/v1-overview.md)
