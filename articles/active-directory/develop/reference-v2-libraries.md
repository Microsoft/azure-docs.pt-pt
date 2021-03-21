---
title: Bibliotecas de autenticação de plataforma de identidade da Microsoft | Rio Azure
description: Lista de bibliotecas de clientes e middleware compatíveis com a plataforma de identidade da Microsoft. Utilize estas bibliotecas para adicionar suporte para o acesso ao utilizador (autenticação) e acesso a API (autorização) protegido às suas aplicações.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 590e57d587c8e6e254811892b5c5e740b511c302
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104690658"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliotecas de autenticação de plataformas de identidade da Microsoft

As tabelas a seguir mostram o suporte da Microsoft Authentication Library para vários tipos de aplicações. Incluem links para o código fonte da biblioteca, onde obter o pacote para o projeto da sua aplicação, e se a biblioteca suporta o sign-in do utilizador (autenticação), acesso a APIs web protegidas (autorização), ou ambos.

A plataforma de identidade da Microsoft foi certificada pela OpenID Foundation como um [fornecedor certificado de OpenID.](https://openid.net/certification/) Se preferir utilizar uma biblioteca que não seja a Microsoft Authentication Library (MSAL) ou outra biblioteca suportada pela Microsoft, escolha uma com uma [implementação certificada do OpenID Connect](https://openid.net/developers/certified/).

Se optar por codificar manualmente a sua própria implementação ao nível do protocolo de [OAuth 2.0 ou OpenID Connect 1.0,](active-directory-v2-protocols.md)preste muita atenção às considerações de segurança na especificação de cada padrão e siga uma metodologia de ciclo de vida de desenvolvimento de software (SDL) como o [Microsoft SDL][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Aplicação de uma página única (SPA)

Uma aplicação de uma página única é inteiramente executado na superfície do navegador e recolhe dados de página (HTML, CSS e JavaScript) dinamicamente ou no tempo de carregamento da aplicação. Pode chamar APIs web para interagir com fontes de dados de back-end.

Como o código de um SPA funciona inteiramente no navegador, é considerado um *cliente público* que é incapaz de armazenar segredos de forma segura.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>Aplicação Web

Uma aplicação web executa código num servidor que gera e envia HTML, CSS e JavaScript para o navegador web de um utilizador a ser renderizado. A identidade do utilizador é mantida como uma sessão entre o navegador do utilizador (a extremidade frontal) e o servidor web (a parte traseira).

Como o código de uma aplicação web é executado no servidor web, é considerado um *cliente confidencial* que pode armazenar segredos de forma segura.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>Aplicação para desktop

Uma aplicação para desktop é tipicamente binária (compilada) código que aparece numa interface de utilizador e destina-se a ser executada no ambiente de trabalho de um utilizador.

Como uma aplicação de desktop é executado no ambiente de trabalho do utilizador, é considerado um *cliente público* que é incapaz de armazenar segredos de forma segura.

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>Aplicação móvel

Uma aplicação móvel é tipicamente um código binário (compilado) que aparece numa interface de utilizador e destina-se a funcionar no dispositivo móvel de um utilizador.

Como uma aplicação móvel funciona no dispositivo móvel do utilizador, é considerado um *cliente público* que não consegue armazenar segredos de forma segura.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>Serviço / daemon

Os serviços e os daemons são geralmente utilizados para o servidor-a-servidor e outras comunicações sem vigilância (por vezes chamadas *de sem cabeça).* Como não há utilizador no teclado para introduzir credenciais ou consentimento para o acesso a recursos, estas aplicações autenticam-se como si mesmos, não como utilizador, ao solicitar o acesso autorizado aos recursos de uma API web.

Um serviço ou daemon que funciona num servidor é considerado um *cliente confidencial* que pode armazenar os seus segredos de forma segura.

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Biblioteca de Autenticação da Microsoft, consulte a [visão geral da Biblioteca de Autenticação da Microsoft (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
