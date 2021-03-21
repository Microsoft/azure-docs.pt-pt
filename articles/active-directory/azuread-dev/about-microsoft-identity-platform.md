---
title: Evolução da plataforma de identidade da Microsoft - Azure
description: Conheça a plataforma de identidade da Microsoft, uma evolução do serviço de identidade Eazure Ative Directory (Azure AD).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv, marsma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ab0057f55ddb5bf76f6fa74b0211b5ea5af24288
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96608219"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Evolução da plataforma de identidade da Microsoft

A [plataforma de identidade da Microsoft](../develop/index.yml) é uma evolução da plataforma de desenvolvimento Azure Ative Directory (Azure AD). Permite que os desenvolvedores construam aplicações que assinam nos utilizadores, obter fichas para chamar APIs, como Microsoft Graph, ou APIs que os desenvolvedores construíram. Consiste num serviço de autenticação, bibliotecas de código aberto, registo de aplicações e configuração (através de um portal de programação e aplicação API), documentação completa do desenvolvedor, amostras de arranque rápido, amostras de código, tutoriais, guias de como fazer e outros conteúdos do desenvolvedor. A plataforma de identidades da Microsoft suporta protocolos norma da indústria, tais como OAuth 2.0 e o OpenID Connect.

Muitos desenvolvedores já trabalharam com a plataforma Azure AD v1.0 para autenticar contas de trabalho e escola (a provisionadas pela Azure AD) solicitando fichas do ponto final AD v1.0 do Azure, utilizando a Azure AD Authentication Library (ADAL), o portal Azure para registo e configuração de aplicações, e a configuração de aplicação programática da Microsoft Graph API.

Com a plataforma de identidade unificada da Microsoft (v2.0), pode escrever código uma vez e autenticar qualquer identidade da Microsoft na sua aplicação. Para várias plataformas, a Biblioteca de Autenticação microsoft (MSAL) totalmente suportada é recomendada para utilização contra os pontos finais da plataforma de identidade. O MSAL é simples de usar, fornece grandes experiências de sso (SSO) para os seus utilizadores, ajuda-o a alcançar uma elevada fiabilidade e desempenho, e é desenvolvido usando o Microsoft Secure Development Lifecycle (SDL). Ao ligar para as APIs, pode configurar o seu pedido para tirar partido do consentimento incremental, o que lhe permite atrasar o pedido de consentimento para âmbitos mais invasivos até que o uso da aplicação o justifique no tempo de execução.  A MSAL também suporta o Azure Ative Directory B2C, pelo que os seus clientes utilizam as suas identidades sociais, empresariais ou de conta local preferidas para obter um único acesso de acesso às suas aplicações e APIs.

Com a plataforma de identidade da Microsoft, expanda o seu alcance para este tipo de utilizadores:

- Contas de trabalho e escolas (contas azure ad)
- Contas pessoais (como Outlook.com ou Hotmail.com)
- Os seus clientes que trazem o seu próprio e-mail ou identidade social (como LinkedIn, Facebook, Google) via MSAL e Azure AD B2C

Pode utilizar o portal Azure para registar e configurar a sua aplicação e utilizar a API do Microsoft Graph para configuração de aplicações programáticas.

Atualize a sua aplicação ao seu próprio ritmo. As aplicações construídas com bibliotecas ADAL continuam a ser apoiadas. São também apoiadas carteiras de aplicações mistas, que consistem em aplicações construídas com ADAL e aplicações construídas com bibliotecas MSAL. Isto significa que as aplicações que utilizam o mais recente ADAL e o mais recente MSAL fornecerão SSO em todo o portefólio, fornecido pela cache simbólica partilhada entre estas bibliotecas. As aplicações atualizadas de ADAL para MSAL manterão o estado de inscrição do utilizador após a atualização.

## <a name="microsoft-identity-platform-experience"></a>Experiência da plataforma de identidade da Microsoft

O diagrama seguinte mostra a experiência de identidade da Microsoft a um nível elevado, incluindo a experiência de registo de aplicações, SDKs, pontos finais e identidades suportadas.

![A plataforma de identidade da Microsoft hoje](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Experiência de registo de aplicativos

A experiência de **[registos de registos do](https://go.microsoft.com/fwlink/?linkid=2083908)** portal Azure é a experiência do portal único para gerir todas as aplicações que integrou com a plataforma de identidade da Microsoft. Se tiver vindo a utilizar o Portal de Registo de Aplicações, comece a utilizar a experiência de registo de aplicações do portal Azure.

Para integração com a Azure AD B2C (ao autenticar identidades sociais ou locais), terá de registar a sua candidatura num inquilino Azure AD B2C. Esta experiência também faz parte do portal Azure.

Utilize a [API de aplicação](/graph/api/resources/application) para configurar programáticamente as suas aplicações integradas na plataforma de identidade da Microsoft para autenticar qualquer identidade da Microsoft.

### <a name="msal-libraries"></a>Bibliotecas MSAL

Pode utilizar a biblioteca MSAL para construir aplicações que autenticam todas as identidades da Microsoft. As bibliotecas MSAL em .NET e JavaScript estão geralmente disponíveis. As bibliotecas MSAL para iOS e Android estão em pré-visualização e adequadas para serem usadas num ambiente de produção. Fornecemos o mesmo suporte ao nível de produção para bibliotecas MSAL em pré-visualização como fazemos para versões de MSAL e ADAL que estão geralmente disponíveis.

Também pode utilizar as bibliotecas MSAL para integrar a sua aplicação com a Azure AD B2C.

### <a name="microsoft-identity-platform-endpoint"></a>Ponto final da plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft (v2.0) é certificada pela OIDC. Funciona com as Bibliotecas de Autenticação da Microsoft (MSAL) ou qualquer outra biblioteca compatível com padrões. Implementa âmbitos legíveis humanos, de acordo com as normas da indústria.

## <a name="next-steps"></a>Passos seguintes

Saiba mais na documentação da plataforma de identidade da [Microsoft.](../develop/index.yml)
