---
title: Evolução da plataforma de identidade do Microsoft - Azure
description: Saiba mais sobre a plataforma de identidade da Microsoft, uma evolução da plataforma de serviço e desenvolvedor de identidade do Azure Active Directory (Azure AD).
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
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03a4702ac0f70e3fc280501cd9524ccbfb528678
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546255"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Evolução da plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft é uma evolução da plataforma para programadores e do serviço de identidade do Azure Active Directory (Azure AD). Ele permite que os desenvolvedores criem aplicativos que iniciar sessão dos utilizadores, obtenha tokens para chamar as APIs, como o Microsoft Graph, ou de APIs que os desenvolvedores criaram. Esta é composta por um serviço de autenticação, bibliotecas de código-fonte aberto, registo de aplicação e configuração (por meio de um portal do programador e a aplicação API), documentação de programador completa, exemplos de início rápido, exemplos de código, tutoriais, guias de procedimentos, e outros conteúdos programador. A plataforma de identidade da Microsoft suporta protocolos padrão da indústria, tais como OAuth 2.0 e OpenID Connect.

Até agora, a maioria dos desenvolvedores já trabalhou com a plataforma de versão 1.0 do Azure AD para autenticar o trabalho contas escolares ou profissionais (aprovisionadas pelo Azure AD) ao solicitar tokens do ponto de extremidade da versão 1.0 do Azure AD, com o Azure AD Authentication Library (ADAL), portal do Azure para registo de aplicação e a configuração e o Azure AD Graph API para configuração da aplicação programática.

Com a plataforma de identidade da Microsoft (v2.0), expanda o seu alcance para estes tipos de utilizadores:

- Contas profissionais e escolares (contas do Azure AD aprovisionado)
- Contas pessoais (por exemplo, Outlook.com ou Hotmail.com)
- Os clientes que tragam os seus próprios e-mail ou a identidade de redes sociais (por exemplo, o LinkedIn, Facebook, Google) através da oferta do Azure AD B2C

Com a plataforma de identidade Microsoft unificada, pode escrever código uma vez e autenticar qualquer identidade da Microsoft na sua aplicação. Para várias plataformas, há uma biblioteca de código aberto totalmente suportada, chamada Microsoft Authentication Library (MSAL). A MSAL é fácil de utilizar, fornece experiências de ótimo início de sessão único (SSO) para os seus utilizadores, ajuda a alcançar a elevada fiabilidade e desempenho e for desenvolvido utilizando o Microsoft Secure Development Lifecycle (SDL). Ao chamar APIs, pode configurar a sua aplicação para tirar partido de consentimento incremental, o que permite-lhe atrasar a solicitação de consentimento para âmbitos invasivas mais até que a utilização do aplicativo garante isso em tempo de execução.

Pode utilizar o portal do Azure para registar e configurar a sua aplicação e utilizar o Microsoft Graph API para configuração de aplicação programática.

Atualize a sua aplicação no seu próprio ritmo. As aplicações criadas com bibliotecas ADAL continuam a ser suportados. Portfólios de aplicativos misto, que consistem as aplicações criadas com o ADAL e aplicativos criados com bibliotecas MSAL, também são suportados. Isso significa que aplicações que utilizam a ADAL mais recente e a MSAL mais recente irão fornecer SSO em todo o portefólio, fornecido pela cache de token partilhado entre essas bibliotecas. Aplicativos atualizados da ADAL para MSAL manterá o estado de sessão do utilizador após a atualização.

## <a name="microsoft-identity-platform-experience"></a>Experiência da plataforma de identidade da Microsoft

O diagrama seguinte mostra a experiência de identidade da Microsoft a um nível elevado, incluindo a experiência de registo de aplicações, SDKs, pontos finais e identidades suportadas.

![A plataforma de identidade da Microsoft hoje](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Experiência de registo de aplicação

O portal do Azure **[registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908)** experiência é uma experiência de portal para gerir todas as aplicações que já integrado com a plataforma de identidade do Microsoft. Se tiver utilizado o Portal de registo de aplicação, começando com a experiência de registo de aplicação do portal do Azure em vez disso.

Para a integração com o Azure AD B2C (quando está a autenticar identidades sociais ou locais), terá de registar a sua aplicação num inquilino do B2C. Esta experiência também é parte do portal do Azure.

O **aplicação API no Microsoft Graph** está atualmente em pré-visualização. Utilize esta API para configurar programaticamente os seus aplicativos integrados com a plataforma de identidade da Microsoft para autenticar qualquer identidade da Microsoft. No entanto, até que esta API alcança a disponibilidade geral, deve utilizar a API do Azure AD Graph 1.6 e o manifesto do aplicativo.

### <a name="msal-libraries"></a>Bibliotecas MSAL

Pode utilizar a biblioteca MSAL para criar aplicativos que autenticar todas as identidades da Microsoft. As bibliotecas MSAL no .NET estão geralmente disponíveis. As bibliotecas MSAL para JavaScript, iOS e Android estão em pré-visualização e adequadas para utilização num ambiente de produção. Fornecemos o mesmo suporte de nível de produção para bibliotecas MSAL em pré-visualização, como fazemos para versões de MSAL e ADAL que estão disponíveis em geral.

Também pode utilizar as bibliotecas MSAL para integrar a aplicação com o Azure AD B2C.

As bibliotecas do lado do servidor para criar aplicações web e web APIs estão em disponibilidade geral: [ASP.NET](https://docs.microsoft.com/aspnet/overview) e [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Ponto final de plataforma de identidade do Microsoft

O ponto de extremidade do Microsoft identity platform (v2.0) agora é OIDC certificada. Ele funciona com as bibliotecas de autenticação da Microsoft (MSAL) ou qualquer outra biblioteca compatíveis com os padrões. Ele implementa âmbitos legíveis humanos, de acordo com padrões da indústria.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre v1.0 e v2.0.

* [Visão geral do Microsoft identity platform (v2.0)](v2-overview.md)
* [O Azure Active Directory para descrição geral de programadores (versão 1.0)](v1-overview.md)