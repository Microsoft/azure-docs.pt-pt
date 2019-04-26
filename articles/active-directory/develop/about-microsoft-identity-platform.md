---
title: Acerca da plataforma de identidade da Microsoft | Azure
description: Saiba mais sobre a plataforma de identidade da Microsoft, uma evolução da plataforma para programadores e do serviço de identidade do Azure Active Directory.
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
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7b3eee08c036862e6ce9f0c590a596f7b1d3fb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301030"
---
# <a name="about-microsoft-identity-platform"></a>Acerca da plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft é uma evolução da plataforma para programadores e do serviço de identidade do Azure Active Directory (Azure AD). Permite que os programadores compilem aplicações que iniciam sessão em todas as identidades da Microsoft, obtenham tokens para chamar o Microsoft Graph, outras APIs da Microsoft ou APIs compiladas pelos programadores. É uma plataforma completa composta por um serviço de autenticação, bibliotecas open source, configuração e registo de aplicações (através de um portal do programador e da API da aplicação), documentação completa para programadores, exemplos de código e outros conteúdos para programadores. A plataforma de identidade da Microsoft suporta protocolos padrão da indústria, tais como OAuth 2.0 e OpenID Connect.

Até agora, a maioria dos programadores já trabalharam com a plataforma Azure AD v1.0 para autenticar identidades do Azure AD (contas profissionais e escolares) ao pedir tokens do ponto final do Azure AD v1.0, com a Biblioteca de Autenticação do Azure AD (ADAL), o portal do Azure para a configuração e o registo de aplicações e a Graph API do Azure AD para a configuração programática de aplicações. A plataforma Azure AD v1.0 é uma oferta de plataforma madura que continuará a funcionar para aplicações empresariais.

Para expandir e desenvolver as capacidades da plataforma de identidade da Microsoft, pode agora autenticar um conjunto mais amplo de identidades da Microsoft (identidades do Azure AD, contas Microsoft (por exemplo, outlook.com e hotmail.com) e contas de redes sociais e locais através do Azure AD B2C) através daquilo que é conhecido como o ponto de final do Azure AD v2.0. Aqui, irá utilizar a Biblioteca de Autenticação da Microsoft (MSAL) ou qualquer biblioteca de OAuth2.0 ou OpenID Connect open source, o portal do Azure para a configuração e o registo de aplicações, e a Microsoft Graph API para a configuração programática de aplicações. A plataforma de identidade da Microsoft atualizada (em particular, as bibliotecas MSAL e a mais recente experiência de registo de aplicações do portal do Azure) evoluiu significativamente no último ano. Para finalizar esta versão, incentivamos os programadores a desenvolver e testar as suas aplicações com a mais recente plataforma de identidade da Microsoft.

As aplicações que utilizam a ADAL mais recente e a MSAL mais recente terão SSO entre si. As aplicações atualizadas da ADAL para a MSAL manterão o estado do início de sessão de utilizador. Os programadores podem optar por atualizar as suas aplicações para a MSAL como entenderem, uma vez que as aplicações criadas com a ADAL continuarão a funcionar e a ser suportadas.

## <a name="microsoft-identity-platform-experience"></a>Experiência da plataforma de identidade da Microsoft

O diagrama seguinte mostra a experiência de identidade da Microsoft a um nível elevado, incluindo a experiência de registo de aplicações, SDKs, pontos finais e identidades suportadas.

![A plataforma de identidade da Microsoft hoje](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

A plataforma de identidade da Microsoft tem dois pontos finais (v1.0 e v2.0) e dois conjuntos de bibliotecas de cliente para processar estes pontos finais. Ao desenvolver uma nova aplicação, considere as vantagens e o estado atual dos pontos finais e das bibliotecas de autenticação. Além disso, considere o seguinte:

* Plataformas suportadas

    * O [ADAL](active-directory-authentication-libraries.md) suporta .NET, JavaScript, iOS, Android, Java e Python
    * O [MSAL de Pré-visualização](reference-v2-libraries.md) suporta .NET, JavaScript, iOS e Android
    * Ambos os pontos finais suportam o middleware de servidor .NET e Node.js para proteger as APIs e o Início de sessão. 

* A maior parte da inovação, como o consentimento dinâmico e o consentimento incremental, está a acontecer no ponto final v2.0 e na MSAL enquanto continuamos a oferecer suporte de v1.0 e ADAL.

    No portal do Azure, anteriormente tinha de identificar estaticamente todos os âmbitos de que a sua aplicação precisava. Com o ponto final v2.0 e os portais associados a este ponto final, pode definir estaticamente os âmbitos tal como antes ou pode pedi-los dinamicamente à medida que a sua aplicação precisa de permissão. O consentimento dinâmico oferece mais uma capacidade opcional, o consentimento incremental. O consentimento incremental permite-lhe pedir um subconjunto de âmbitos de que precisa quando um utilizador faz a autenticação pela primeira vez e pedir âmbitos adicionais conforme forem necessários. 
    
    Por exemplo, ao utilizar uma aplicação de câmara num dispositivo móvel, é pedido ao utilizador que permita que a aplicação aceda à câmara e só depois de o utilizador ter dado consentimento é que a aplicação terá permissão para aceder à câmara e tirar uma fotografia.  Quando a aplicação estiver pronta para guardar a nova fotografia, poderá pedir permissão de leitura/escrita de fotografias. 

* Possíveis alterações interruptivas

    A MSAL é adequada para ser utilizada num ambiente de produção. Fornecemos o mesmo suporte de nível de produção para MSAL que fornecemos nas atuais bibliotecas de produção. Durante a pré-visualização, podemos fazer alterações à API, ao formato de cache interna e a outros mecanismos desta biblioteca, o que precisará de levar juntamente com as correções de erros ou as melhorias de funcionalidades. Isto pode afetar a sua aplicação. Por exemplo, uma alteração ao formato de cache pode afetar os seus utilizadores, como, por exemplo, exigir que voltem a iniciar sessão. Uma alteração à API pode exigir que atualize o seu código. Quando fornecermos a versão de disponibilidade geral (GA), iremos solicitar que faça a atualização para a versão GA no prazo de seis meses, uma vez que as aplicações escritas com uma versão de pré-visualização da biblioteca podem deixar de funcionar.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre v1.0 e v2.0.

* [Acerca do v1.0](v1-overview.md)
* [Acerca do v2.0](v2-overview.md)
