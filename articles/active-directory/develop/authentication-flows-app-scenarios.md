---
title: Cenários de autenticação para a plataforma Microsoft Identity | Azure
description: Saiba mais sobre fluxos de autenticação e cenários de aplicativos para a plataforma Microsoft Identity. Saiba mais sobre os diferentes tipos de aplicativo que podem autenticar identidades, adquirir tokens e chamar APIs protegidas.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 946eb0343c2858e275e40b7a581fd48e1713accd
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853518"
---
# <a name="authentication-flows-and-application-scenarios"></a>Fluxos de autenticação e cenários de aplicativos

O ponto de extremidade da plataforma Microsoft Identity (v 2.0) dá suporte à autenticação para uma variedade de arquiteturas de aplicativo modernas, todas elas baseadas em protocolos padrão do setor [OAuth 2,0 ou OpenID Connect](active-directory-v2-protocols.md).  Usando as [bibliotecas de autenticação](reference-v2-libraries.md), os aplicativos autenticam identidades e adquirem tokens para acessar APIs protegidas. Este artigo descreve os diferentes fluxos de autenticação e os cenários de aplicativo em que eles são usados.  Este artigo também fornece listas de [cenários de aplicativos e fluxos de autenticação com suporte](#scenarios-and-supported-authentication-flows) e [cenários de aplicativos e plataformas com suporte](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Categorias de aplicativo

Tokens podem ser adquiridos de vários tipos de aplicativos: Aplicativos Web, aplicativos móveis ou de área de trabalho, APIs da Web e aplicativos em execução em dispositivos que não têm um navegador (ou iOT). Os aplicativos podem ser categorizados pelo seguinte:

- [Recursos protegidos vs. aplicativos cliente](#protected-resources-vs-client-applications). Alguns cenários são sobre a proteção de recursos (aplicativos Web ou APIs Web) e outros são sobre a aquisição de um token de segurança para chamar uma API Web protegida.
- [Com usuários ou sem usuários](#with-users-or-without-users). Alguns cenários envolvem um usuário conectado, enquanto outros não envolvem um usuário (cenários de daemon).
- [Aplicativos de página única, aplicativos cliente públicos e aplicativos cliente confidenciais](#single-page-applications-public-client-applications-and-confidential-client-applications). Essas são três grandes categorias de tipos de aplicativos. As bibliotecas e os objetos usados para manipulá-los serão diferentes.
- [Público-alvo](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types). Alguns fluxos de autenticação não estão disponíveis para determinados públicos-alvo. Alguns fluxos estão disponíveis apenas para contas corporativas ou de estudante e alguns estão disponíveis para contas corporativas ou de estudante e contas pessoais da Microsoft. O público permitido depende dos fluxos de autenticação.
- [Fluxos OAuth 2,0 com suporte](#scenarios-and-supported-authentication-flows).  Os fluxos de autenticação são usados para implementar os cenários de aplicativo que solicitam tokens.  Não há um mapeamento de um para um entre cenários de aplicativo e fluxos de autenticação.
- [Plataformas com suporte](#scenarios-and-supported-platforms-and-languages). Nem todos os cenários de aplicativo estão disponíveis para cada plataforma.

### <a name="protected-resources-vs-client-applications"></a>Recursos protegidos vs. aplicativos cliente

Os cenários de autenticação envolvem duas atividades:

- **Aquisição** de tokens de segurança para uma API Web protegida. A Microsoft recomenda que você use [bibliotecas de autenticação](reference-v2-libraries.md#microsoft-supported-client-libraries) para adquirir tokens, especialmente a família de bibliotecas de autenticação da Microsoft (MSAL)
- **Protegendo uma API Web** (ou um aplicativo Web). Um dos desafios de proteger um recurso (aplicativo Web ou API Web) é validar o token de segurança. A Microsoft oferece, em algumas plataformas, [bibliotecas de middleware](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Com usuários ou sem usuários

A maioria dos cenários de autenticação adquire tokens em nome de um **usuário**(conectado).

![cenários com usuários](media/scenarios/scenarios-with-users.svg)

No entanto, também há cenários (aplicativos de daemon), em que os aplicativos adquirirão tokens em nome de si mesmos (sem usuário).

![aplicativos de daemon](media/scenarios/daemon-app.svg)

### <a name="single-page-applications-public-client-applications-and-confidential-client-applications"></a>Aplicativos de página única, aplicativos cliente públicos e aplicativos cliente confidenciais

Os tokens de segurança podem ser adquiridos de vários tipos de aplicativos. Os aplicativos tendem a ser separados em três categorias:

- **Aplicativos de página única** (SPA) são uma forma de aplicativos Web em que os tokens são adquiridos do aplicativo em execução no navegador (escrito em JavaScript ou typescript). Muitos aplicativos modernos têm um front-end de aplicativo de página única que é basicamente escrito em JavaScript. Geralmente, o aplicativo é escrito usando uma estrutura como angular, reagir ou Vue. MSAL. js é a única biblioteca de autenticação da Microsoft que oferece suporte a aplicativos de página única.

- Os **aplicativos cliente públicos** sempre entram em usuários. Esses aplicativos são:
  - Aplicativos de desktop chamando APIs da Web em nome do usuário conectado.
  - Aplicativos móveis.
  - Uma terceira categoria de aplicativos, em execução em dispositivos que não têm um navegador (aplicativos sem navegador, em execução no iOT, por exemplo).

  Eles são representados pela classe MSAL chamada [PublicClientApplication](msal-client-applications.md).

- **Aplicativos cliente confidenciais**
  - Aplicativos Web chamando uma API da Web
  - APIs da Web chamando uma API da Web
  - Aplicativos daemon (mesmo quando implementados como um serviço de console como um daemon no Linux ou um serviço do Windows)
 
  Esses tipos de aplicativos usam o [ConfidentialClientApplication](msal-client-applications.md)

## <a name="application-scenarios"></a>Cenários de aplicações

O ponto de extremidade da plataforma de identidade da Microsoft dá suporte à autenticação para uma variedade de arquiteturas de aplicativo: aplicativos de página única, aplicativos Web, APIs da Web, aplicativos móveis e nativos, e daemons e aplicativos do lado do servidor.  Os aplicativos usam os vários fluxos de autenticação para conectar usuários e obter tokens para chamar APIs protegidas.

### <a name="single-page-application"></a>Aplicação de página única

Muitos aplicativos Web modernos são criados como aplicativos de página única do lado do cliente escritos usando JavaScript ou uma estrutura SPA como angular, Vue. js e reagir. js. Esses aplicativos são executados em um navegador da Web e têm características de autenticação diferentes dos aplicativos Web tradicionais do lado do servidor. A plataforma de identidade da Microsoft permite que aplicativos de página única conectem usuários e obtenham tokens para acessar serviços de back-end ou APIs da Web.

![Aplicação de página única](media/scenarios/spa-app.svg)

Para obter mais informações, leia [aplicativos de página única](scenario-spa-overview.md).

### <a name="web-application-signing-in-a-user"></a>Assinatura de aplicativo Web-em um usuário

![Usuários do aplicativo Web](media/scenarios/scenario-webapp-signs-in-users.svg)

Para **proteger um aplicativo Web** (entrando no usuário), você usará:

- No mundo do .NET, ASP.NET ou ASP.NET Core com o middleware ASP.NET Open ID Connect. Nos bastidores, proteger um recurso envolve validar o token de segurança, que é feito pela biblioteca [de extensões do IdentityModel para .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , e não por bibliotecas MSAL

- Se você desenvolver no node. js, você usará o Passport. js.

Para obter mais informações, leia [aplicativo Web que faz logon de usuários](scenario-web-app-sign-user-overview.md).

### <a name="web-application-signing-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Assinatura de aplicativo Web-em um usuário e chamando uma API da Web em nome do usuário

![Aplicativo Web chama APIs da Web](media/scenarios/web-app.svg)

No aplicativo Web, para **chamar a API da Web** em nome do usuário, use MSAL `ConfidentialClientApplication`. Você usará o fluxo de código de autorização, armazenando o token adquirido no cache de token. Em seguida, o controlador adquirirá tokens silenciosamente do cache, quando necessário. O MSAL atualiza o token, se necessário.

Para obter mais informações, leia [aplicativos Web chamadas APIs da Web](scenario-web-app-call-api-overview.md).

### <a name="desktop-application-calling-a-web-api-on-behalf-of-the-signed-in-user"></a>Aplicativo de desktop chamando uma API da Web em nome do usuário conectado

Para chamar uma API da Web de um aplicativo de área de trabalho que conecta usuários, use os métodos de aquisição de tokens interativos do MSAL PublicClientApplication's. Esses métodos interativos permitem que você controle a experiência de interface do usuário de entrada. Para habilitar essa interação, o MSAL aproveita um navegador da Web.

![Desktop](media/scenarios/desktop-app.svg)

Para aplicativos hospedados do Windows em execução em computadores ingressados em um domínio do Windows ou ingressado no AAD, há outra possibilidade. Esses aplicativos podem adquirir um token silenciosamente usando a [autenticação integrada do Windows](https://aka.ms/msal-net-iwa).

Os aplicativos em execução em um dispositivo sem um navegador ainda poderão chamar uma API em nome de um usuário. Para autenticar, o usuário precisará entrar em outro dispositivo que tenha um navegador da Web. Para habilitar esse cenário, você precisará usar o [fluxo de código do dispositivo](https://aka.ms/msal-net-device-code-flow)

![Fluxo de código do dispositivo](media/scenarios/device-code-flow-app.svg)

Por fim, embora não seja recomendado, você pode usar o [nome de usuário/senha](https://aka.ms/msal-net-up) em aplicativos cliente públicos. Esse fluxo ainda é necessário em alguns cenários (como DevOps), mas lembre-se de que usá-lo irá impor restrições em seu aplicativo. Por exemplo, os aplicativos que usam esse fluxo não poderão entrar em um usuário que precise executar a autenticação multifator (acesso condicional). Ele não permitirá que seu aplicativo se beneficie de um logon único. A autenticação com nome de usuário/senha vai contra os princípios de autenticação moderna e é fornecida somente por motivos herdados.

Em aplicativos de área de trabalho, se você quiser que o cache de token seja persistente, você deve [Personalizar a serialização de cache de token](https://aka.ms/msal-net-token-cache-serialization). Você pode até mesmo habilitar caches de Tokens compatíveis com versões anteriores e posteriores com gerações antigas de bibliotecas de autenticação (ADAL.NET 3. x e 4. x) implementando a [serialização de cache de token duplo](https://aka.ms/msal-net-dual-cache-serialization).

Para obter mais informações, leia [aplicativo de área de trabalho que chama APIs da Web](scenario-desktop-overview.md).

### <a name="mobile-application-calling-a-web-api-on-behalf-of-the-user-whos-signed-in-interactively"></a>Aplicativo móvel chamando uma API da Web em nome do usuário que está conectado interativamente

Semelhante aos aplicativos da área de trabalho, um aplicativo móvel usará os métodos de aquisição de token do PublicClientApplication's Interactive do MSAL para adquirir um token para chamar uma API da Web.

![móvel](media/scenarios/mobile-app.svg)

O MSAL iOS e o MSAL Android, por padrão, usam o navegador da Web do sistema. No entanto, você também pode direcioná-lo para usar a exibição da Web inserida. Existem especificidades, dependendo da plataforma móvel: (UWP, iOS, Android).

Alguns cenários, envolvendo o acesso condicional relacionado à ID do dispositivo ou um dispositivo que está sendo registrado exigem que um [agente](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) seja instalado em um dispositivo. Exemplos de agentes são o portal da empresa da Microsoft (no Android), Microsoft Authenticator (Android e iOS). O MSAL agora é capaz de interagir com agentes.

> [!NOTE]
> Seu aplicativo móvel (usando MSAL. iOS, MSAL. Android ou MSAL.NET/Xamarin) podem ter políticas de proteção de aplicativo aplicadas a ele (por exemplo, impedir que o usuário Copie um texto protegido). Isso é [gerenciado pelo Intune](https://docs.microsoft.com/intune/app-sdk) e reconhecido pelo Intune como um aplicativo gerenciado. O [SDK do Intune](https://docs.microsoft.com/intune/app-sdk-get-started) é separado das bibliotecas do MSAL e se comunica com o AAD por conta própria.

Para obter mais informações, leia [aplicativo móvel que chama APIs da Web](scenario-mobile-overview.md).

### <a name="protected-web-api"></a>API Web protegida

Você pode usar o ponto de extremidade da plataforma de identidade da Microsoft para proteger serviços Web, como a API Web RESTful do seu aplicativo. Uma API Web protegida é chamada com um token de acesso para proteger seus dados e autenticar solicitações de entrada. O chamador de uma API da Web acrescenta um token de acesso no cabeçalho de autorização de uma solicitação HTTP. Se você quiser proteger sua API Web ASP.NET ou ASP.NET Core, será necessário validar o token de acesso. Para isso, você usará o middleware JWT ASP.NET. Nos bastidores, a validação é feita pela biblioteca [de extensões IdentityModel para .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , não MSAL.net

Para obter mais informações, leia [API Web protegida](scenario-protected-web-api-overview.md).

### <a name="web-api-calling-another-downstream-web-api-on-behalf-of-the-user-for-whom-it-was-called"></a>API Web chamando outra API Web downstream em nome do usuário para o qual ele foi chamado

Se, além disso, você quiser que seu ASP.NET ou ASP.NET Core API da Web protegida chame outra API da Web em nome do usuário, o aplicativo precisará adquirir um token para a API Web downstream usando o método do ConfidentialClientApplication adquirindo um token em [nome de um usuário](https://aka.ms/msal-net-on-behalf-of). Isso também é chamado de serviço para chamadas de serviços.
As APIs da Web que chamam outra API da Web também precisarão fornecer uma serialização de cache Personalizada

  ![API Web](media/scenarios/web-api.svg)

Para obter mais informações, leia [API Web que chama APIs da Web](scenario-web-api-call-api-overview.md).

### <a name="desktopservice-or-web-daemon-application-calling-web-api-without-a-user-in-its-own-name"></a>Aplicativo de desktop/serviço ou Web daemon chamando a API da Web sem um usuário (em seu próprio nome)

Aplicativos que têm processos de execução longa ou que operam sem interação do usuário também precisam de uma maneira de acessar APIs da Web protegidas. Esses aplicativos podem autenticar e obter tokens usando a identidade do aplicativo, em vez da identidade delegada de um usuário. Eles provam sua identidade usando um segredo ou certificado do cliente.
Você pode escrever tais aplicativos (daemon app) adquirindo um token para o aplicativo no topo usando os métodos de aquisição de [credenciais de cliente](https://aka.ms/msal-net-client-credentials) do CONFIDENTIALCLIENTAPPLICATION'S da MSAL. Isso pressupõe que o aplicativo registrou anteriormente um segredo (senha de aplicativo ou certificado ou asserção de cliente) com o Azure AD, que ele compartilha com essa chamada.

![Aplicações daemon](media/scenarios/daemon-app.svg)

Para obter mais informações, leia o [aplicativo daemon que chama APIs da Web](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Cenários e fluxos de autenticação com suporte

Os cenários que envolvem a aquisição de tokens também são mapeados para os fluxos de autenticação do OAuth 2,0 descritos em detalhes nos [protocolos da plataforma Microsoft Identity](active-directory-v2-protocols.md)

|Cenário | Orientação detalhada do cenário | Fluxo/concessão do OAuth 2,0 | Audiência |
|--|--|--|--|
| [![Aplicativo de página única](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | [Aplicação de página única](scenario-spa-overview.md) | [Localiza](v2-oauth2-implicit-grant-flow.md) | Contas corporativas ou de estudante e contas pessoais, B2C
| [![Aplicativo Web que faz logon de usuários](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | [Aplicativo Web que assina usuários](scenario-web-app-sign-user-overview.md) | [Código de autorização](v2-oauth2-auth-code-flow.md) | Contas corporativas ou de estudante e contas pessoais, B2C |
| [![Aplicativo Web que chama APIs da Web](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | [Aplicativo Web que chama APIs da Web](scenario-web-app-call-api-overview.md) | [Código de autorização](v2-oauth2-auth-code-flow.md) | Contas corporativas ou de estudante e contas pessoais, B2C |
| [![Aplicativo de desktop que chama APIs da Web](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) | [Aplicação de ambiente de trabalho que chama APIs Web](scenario-desktop-overview.md)| Interativo ([código de autorização](v2-oauth2-auth-code-flow.md) com PKCE) | Contas corporativas ou de estudante e contas pessoais, B2C |
| | | Windows integrado | Contas corporativas ou de estudante |
| | | [Senha do proprietário do recurso](v2-oauth-ropc.md)  | Contas corporativas ou de estudante, B2C |
| ![Fluxo de código do dispositivo](media/scenarios/device-code-flow-app.svg)| [Aplicação de ambiente de trabalho que chama APIs Web](scenario-desktop-overview.md) | [Código do dispositivo](v2-oauth2-device-code.md)  | Contas corporativas ou de estudante * |
| [![Aplicativo móvel que chama APIs da Web](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | [Aplicativo móvel que chama APIs da Web](scenario-mobile-overview.md) | Interativo ([código de autorização](v2-oauth2-auth-code-flow.md) com PKCE)  |   Contas corporativas ou de estudante e contas pessoais, B2C
| | | Senha do proprietário do recurso  | Contas corporativas ou de estudante, B2C |
| [![Aplicativo daemon](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | [Aplicativo daemon](scenario-daemon-overview.md) | [Credenciais de cliente](v2-oauth2-client-creds-grant-flow.md)  |   Permissões somente do aplicativo (sem usuário) somente em organizações do AAD
| [![API Web que chama APIs da Web](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | [API Web que chama APIs da Web](scenario-web-api-call-api-overview.md)| [Em nome de](v2-oauth2-on-behalf-of-flow.md) | Contas corporativas ou de estudante e contas pessoais |

## <a name="scenarios-and-supported-platforms-and-languages"></a>Cenários e plataformas com suporte e linguagens

Nem todo tipo de aplicativo está disponível em todas as plataformas. Você também pode usar várias linguagens para compilar seus aplicativos. As bibliotecas de autenticação da Microsoft dão suporte a várias **plataformas** (JavaScript, .NET Framework, .NET Core, Windows 10/UWP, Xamarin. Ios, Xamarin. Android, Ios nativo, Android nativo, Java, Python)

|Cenário  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Aplicação de página única](scenario-spa-overview.md) <br/>[![Aplicativo de página única](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js
| [Aplicativo Web que assina usuários](scenario-web-app-sign-user-overview.md) <br/>[![Aplicativo Web que faz logon de usuários](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET](media/sample-v2-code/logo_NET.png)</br> ASP.NET ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core
| [Aplicativo Web que chama APIs da Web](scenario-web-app-call-api-overview.md) <br/> [![Aplicativo Web que chama APIs da Web](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET](media/sample-v2-code/logo_NET.png) </br> ASP.NET + MSAL.NET </br> ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Flask + MSAL Python
| [Aplicação de ambiente de trabalho que chama APIs Web](scenario-desktop-overview.md) <br/> [Aplicativo de desktop que chama o fluxo de código de dispositivo de APIs Web ![](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![](media/scenarios/device-code-flow-app.svg) | ![MSAL.NET](media/sample-v2-code/logo_NET.png)  MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python
| [Aplicativo móvel que chama APIs da Web](scenario-mobile-overview.md) <br/> [![Aplicativo móvel que chama APIs da Web](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/logo_xamarin.png) MSAL.NET | | | ![iOS/objetivo C ou Swift](media/sample-v2-code/logo_iOS.png) MSAL. iOS | ![Android](media/sample-v2-code/logo_Android.png) MSAL. Android
| [Aplicativo daemon](scenario-daemon-overview.md) <br/> [![Aplicativo daemon](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET](media/sample-v2-code/logo_NET.png) MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python
| [API Web que chama APIs da Web](scenario-web-api-call-api-overview.md) <br/> [![API Web que chama APIs da Web](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![.NET](media/sample-v2-code/logo_NET.png) <br/> ASP.NET + MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png) <br/> ASP.NET Core + MSAL.NET| ![.NET Core](media/sample-v2-code/logo_NETcore.png) <br/> ASP.NET Core + MSAL.NET| ![.NET Core](media/sample-v2-code/logo_NETcore.png)<br/> ASP.NET Core + MSAL.NET

Consulte também [bibliotecas com suporte da Microsoft por sistema operacional/idioma](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [noções básicas de autenticação](authentication-scenarios.md) e tokens de [acesso](access-tokens.md).
