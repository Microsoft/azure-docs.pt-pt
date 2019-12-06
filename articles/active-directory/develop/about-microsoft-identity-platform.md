---
title: Evolução da plataforma de identidade da Microsoft – Azure
description: Saiba mais sobre a plataforma de identidade da Microsoft, uma evolução do serviço de identidade do Azure Active Directory (Azure AD) e da plataforma de desenvolvedor.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 06/03/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca1b6cc6288b75dc7194ead916f2ecb468b053d2
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74845914"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Evolução da plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft é uma evolução da plataforma de programador do Azure Active Directory (Azure AD). Ele permite que os desenvolvedores criem aplicativos que conectam usuários, obtenham tokens para chamar APIs, como Microsoft Graph ou APIs que os desenvolvedores criaram. Ele consiste em um serviço de autenticação, bibliotecas de software livre, registro de aplicativo e configuração (por meio de um portal do desenvolvedor e API de aplicativo), documentação completa para desenvolvedores, exemplos de início rápido, exemplos de código, tutoriais, guias de instruções e outro conteúdo do desenvolvedor. A plataforma de identidades da Microsoft suporta protocolos norma da indústria, tais como OAuth 2.0 e o OpenID Connect.

Até agora, a maioria dos desenvolvedores trabalhou com a plataforma Azure AD v 1.0 para autenticar contas corporativas e de estudante (provisionadas pelo Azure AD) solicitando tokens do ponto de extremidade v 1.0 do Azure AD, usando a ADAL (biblioteca de autenticação do Azure AD), portal do Azure para registro e configuração do aplicativo e Azure AD API do Graph para configuração de aplicativo programática.

Com a plataforma de identidade da Microsoft (v 2.0), expanda seu alcance para esses tipos de usuários:

- Contas corporativas e de estudante (contas do Azure AD provisionadas)
- Contas pessoais (como Outlook.com ou Hotmail.com)
- Seus clientes que trazem seu próprio email ou identidade social (como LinkedIn, Facebook, Google) por meio da oferta de Azure AD B2C

Com a plataforma de identidade unificada da Microsoft, você pode escrever código uma vez e autenticar qualquer identidade da Microsoft em seu aplicativo. Para várias plataformas, há uma biblioteca de código aberto totalmente compatível chamada MSAL (biblioteca de autenticação da Microsoft). O MSAL é simples de usar, fornece excelentes experiências de SSO (logon único) para seus usuários, ajuda a alcançar alta confiabilidade e desempenho e é desenvolvido usando o SDL (ciclo de vida de desenvolvimento seguro da Microsoft). Ao chamar APIs, você pode configurar seu aplicativo para aproveitar o consentimento incremental, o que permite atrasar a solicitação de consentimento para mais escopos invasivos até que o uso do aplicativo garanta isso no tempo de execução.

Você pode usar o portal do Azure para registrar e configurar seu aplicativo e usar a API Microsoft Graph para configuração de aplicativo programática.

Atualize seu aplicativo em seu próprio ritmo. Os aplicativos criados com bibliotecas ADAL continuam com suporte. Os portfólios de aplicativos mistos, que consistem em aplicativos criados com a ADAL e com aplicativos criados com bibliotecas MSAL, também têm suporte. Isso significa que os aplicativos que usam a ADAL mais recente e o MSAL mais recente fornecerão o SSO pelo portfólio, fornecido pelo cache de token compartilhado entre essas bibliotecas. Os aplicativos atualizados do ADAL para o MSAL manterão o estado de entrada do usuário após a atualização.

## <a name="microsoft-identity-platform-experience"></a>Experiência da plataforma de identidade da Microsoft

O diagrama seguinte mostra a experiência de identidade da Microsoft a um nível elevado, incluindo a experiência de registo de aplicações, SDKs, pontos finais e identidades suportadas.

![A plataforma de identidade da Microsoft hoje](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Experiência de registro do aplicativo

A experiência de **[Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908)** portal do Azure é a experiência de um portal para gerenciar todos os aplicativos que você integrou à plataforma de identidades da Microsoft. Se você estiver usando o portal de registro de aplicativos, comece a usar a experiência de registro de aplicativo portal do Azure.

Para integração com o Azure AD B2C (ao autenticar identidades sociais ou locais), você precisará registrar seu aplicativo em um locatário Azure AD B2C. Essa experiência também faz parte do portal do Azure.

A **API do aplicativo no Microsoft Graph** está atualmente em visualização. Use essa API para configurar programaticamente seus aplicativos integrados à plataforma Microsoft Identity para autenticar qualquer identidade da Microsoft. No entanto, até que essa API atinja a disponibilidade geral, você deve usar a API do Azure AD Graph 1,6 e o manifesto do aplicativo.

### <a name="msal-libraries"></a>Bibliotecas MSAL

Você pode usar a biblioteca MSAL para criar aplicativos que autenticam todas as identidades da Microsoft. As bibliotecas MSAL no .NET e no JavaScript estão geralmente disponíveis. As bibliotecas do MSAL para iOS e Android estão em versão prévia e adequadas para uso em um ambiente de produção. Fornecemos o mesmo suporte de nível de produção para bibliotecas MSAL na visualização, como fazemos para versões do MSAL e ADAL que estão geralmente disponíveis.

Você também pode usar as bibliotecas MSAL para integrar seu aplicativo com Azure AD B2C.

As bibliotecas do lado do servidor para criar aplicativos Web e APIs Web estão geralmente disponíveis: [ASP.net](https://docs.microsoft.com/aspnet/overview) e [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Ponto de extremidade da plataforma Microsoft Identity

O ponto de extremidade da plataforma Microsoft Identity (v 2.0) agora é certificado OIDC. Ele funciona com as MSAL (bibliotecas de autenticação da Microsoft) ou qualquer outra biblioteca compatível com padrões. Ele implementa escopos legíveis humanos, de acordo com os padrões do setor.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre v1.0 e v2.0.

* [Visão geral da plataforma Microsoft Identity (v 2.0)](v2-overview.md)
* [Visão geral de Azure Active Directory para desenvolvedores (v 1.0)](v1-overview.md)
