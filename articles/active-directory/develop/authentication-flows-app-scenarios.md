---
title: Cenários de autenticação para a plataforma Microsoft Identity | Azure
description: Saiba mais sobre fluxos de autenticação e cenários de aplicativos para a plataforma de identidade da Microsoft. Saiba mais sobre os diferentes tipos de aplicativos que podem autenticar identidades, adquirir tokens e chamar APIs protegidas.
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
ms.date: 09/27/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71557a2776bae36508beec8d5af9e00923393163
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72324731"
---
# <a name="authentication-flows-and-application-scenarios"></a>Fluxos de autenticação e cenários de aplicativos

O ponto de extremidade da plataforma Microsoft Identity (v 2.0) dá suporte à autenticação para diferentes tipos de arquiteturas de aplicativo modernas. Todas as arquiteturas são baseadas nos protocolos padrão do setor [OAuth 2,0 e OpenID Connect](active-directory-v2-protocols.md).  Usando as [bibliotecas de autenticação](reference-v2-libraries.md), os aplicativos autenticam identidades e adquirem tokens para acessar APIs protegidas.

Este artigo descreve os diferentes fluxos de autenticação e os cenários de aplicativo em que eles são usados. Este artigo também fornece listas de:
- [Cenários de aplicativos e fluxos de autenticação com suporte](#scenarios-and-supported-authentication-flows).
- [Cenários de aplicativos e plataformas e linguagens com suporte](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Categorias de aplicativo

Os tokens podem ser adquiridos de vários tipos de aplicativos, incluindo:

- Aplicações Web
- Aplicações Móveis
- Aplicativos da área de trabalho
- APIs da Web

Eles também podem ser adquiridos de aplicativos em execução em dispositivos que não têm um navegador ou que estão em execução no IoT.

Os aplicativos podem ser categorizados como na lista a seguir:

- [Recursos protegidos vs. aplicativos cliente](#protected-resources-vs-client-applications): alguns cenários são sobre a proteção de recursos, como aplicativos Web ou APIs Web. Outros cenários são sobre a aquisição de um token de segurança para chamar uma API Web protegida.
- [Com usuários ou sem usuários](#with-users-or-without-users): alguns cenários envolvem um usuário conectado, mas outros, como cenários de daemon, não envolvem um usuário.
- [Aplicativos cliente de página única, cliente público e confidencial](#single-page-public-client-and-confidential-client-applications): são três grandes categorias de tipos de aplicativos. Cada uma é usada com diferentes bibliotecas e objetos.
- [Público-alvo](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): os fluxos de autenticação disponíveis diferem de acordo com o público-alvo. Alguns fluxos estão disponíveis apenas para contas corporativas ou de estudante. E alguns estão disponíveis para contas corporativas ou de estudante e para contas pessoais da Microsoft. O público permitido depende dos fluxos de autenticação.
- [Fluxos OAuth 2,0 com suporte](#scenarios-and-supported-authentication-flows): os fluxos de autenticação são usados para implementar os cenários de aplicativo que estão solicitando tokens. Não há um mapeamento de um para um entre cenários de aplicativo e fluxos de autenticação.
- [Plataformas com suporte](#scenarios-and-supported-platforms-and-languages): nem todos os cenários de aplicativo estão disponíveis para cada plataforma.

### <a name="protected-resources-vs-client-applications"></a>Recursos protegidos versus aplicativos cliente

Os cenários de autenticação envolvem duas atividades:

- **Adquirindo tokens de segurança para uma API Web protegida**: a Microsoft recomenda que você use [bibliotecas de autenticação](reference-v2-libraries.md#microsoft-supported-client-libraries) para adquirir tokens, especialmente a família de MSAL (biblioteca de autenticação da Microsoft).
- **Proteger uma API da Web ou um aplicativo Web**: um desafio de proteger um recurso de aplicativo Web ou API da Web é validar o token de segurança. Em algumas plataformas, a Microsoft oferece [bibliotecas de middleware](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Com usuários ou sem usuários

A maioria dos cenários de autenticação adquire tokens em nome de usuários conectados.

![Cenários com usuários](media/scenarios/scenarios-with-users.svg)

No entanto, também há cenários de daemon-aplicativo, nos quais os aplicativos adquirem tokens em nome de si mesmos sem usuário.

![Cenários com aplicativos daemon](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Aplicativos cliente de página única, cliente público e confidencial

Os tokens de segurança podem ser adquiridos de vários tipos de aplicativos. Esses aplicativos tendem a ser separados em três categorias:

- **Aplicativos de página única**: também conhecidos como spas, são aplicativos Web nos quais os tokens são adquiridos de um aplicativo JavaScript ou TypeScript em execução no navegador. Muitos aplicativos modernos têm um front-end de aplicativo de página única que é escrito principalmente em JavaScript. O aplicativo geralmente usa uma estrutura como angular, reagir ou Vue. MSAL. js é a única biblioteca de autenticação da Microsoft que dá suporte a aplicativos de página única.

- **Aplicativos cliente públicos**: estes aplicativos sempre entram em usuários:
  - Aplicativos da área de trabalho chamando APIs da Web em nome do usuário conectado
  - Aplicações Móveis
  - Aplicativos em execução em dispositivos que não têm um navegador, como aqueles em execução na iOT

  Esses aplicativos são representados pela classe MSAL [PublicClientApplication](msal-client-applications.md) .

- **Aplicativos cliente confidenciais**:
  - Aplicativos Web chamando uma API Web
  - APIs da Web chamando uma API da Web
  - Aplicativos daemon, mesmo quando implementados como um serviço de console como um daemon do Linux ou um serviço do Windows
 
  Esses tipos de aplicativos usam a classe [ConfidentialClientApplication](msal-client-applications.md) .

## <a name="application-scenarios"></a>Cenários de aplicações

O ponto de extremidade da plataforma de identidade da Microsoft dá suporte à autenticação para diferentes tipos de arquiteturas de aplicativo:

- Aplicações de página única
- Aplicações Web
- APIs da Web
- Aplicações Móveis
- Aplicativos nativos
- Aplicações daemon
- Aplicativos do lado do servidor

Os aplicativos usam os diferentes fluxos de autenticação para conectar usuários e obter tokens para chamar APIs protegidas.

### <a name="a-single-page-application"></a>Um aplicativo de página única

Muitos aplicativos Web modernos são criados como aplicativos de página única do lado do cliente escritos usando JavaScript ou uma estrutura SPA como angular, Vue. js e reagir. js. Esses aplicativos são executados em um navegador da Web. Suas características de autenticação diferem das de aplicativos Web tradicionais do lado do servidor. Usando a plataforma de identidade da Microsoft, os aplicativos de página única podem conectar usuários e obter tokens para acessar serviços de back-end ou APIs da Web.

![Um aplicativo de página única](media/scenarios/spa-app.svg)

Para obter mais informações, consulte [aplicativos de página única](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>Um aplicativo Web que está entrando em um usuário

![Um aplicativo Web que assina um usuário](media/scenarios/scenario-webapp-signs-in-users.svg)

Para proteger um aplicativo Web que está entrando em um usuário:

- Se você desenvolver no .NET, use ASP.NET ou ASP.NET Core com o middleware ASP.NET Open ID Connect. A proteção de um recurso envolve validar o token de segurança, que é feito pela biblioteca [de extensões IdentityModel para .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) e não por bibliotecas MSAL.

- Se você desenvolver no node. js, use o Passport. js.

Para obter mais informações, consulte [aplicativo Web que entra em usuários](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Um aplicativo Web que assina um usuário e chama uma API da Web em nome do usuário

![Um aplicativo Web chamando APIs Web](media/scenarios/web-app.svg)

Para chamar uma API da Web de um aplicativo Web em nome de um usuário, use a classe MSAL **ConfidentialClientApplication** . Você usa o fluxo de código de autorização e armazena os tokens adquiridos no cache de token. Quando necessário, o MSAL atualiza os tokens e o controlador adquire silenciosamente os tokens do cache.

Para obter mais informações, consulte [um aplicativo Web chamando APIs da Web](scenario-web-app-call-api-overview.md).

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>Um aplicativo de desktop chamando uma API da Web em nome de um usuário conectado

Para um aplicativo de desktop chamar uma API Web que conecta usuários, use os métodos de aquisição de token interativo da classe MSAL **PublicClientApplication** . Com esses métodos interativos, você pode controlar a experiência de interface do usuário de entrada. O MSAL usa um navegador da Web para essa interação.

![Um aplicativo de desktop chamando uma API Web](media/scenarios/desktop-app.svg)

Há outra possibilidade de aplicativos hospedados no Windows em computadores ingressados em um domínio do Windows ou por Azure Active Directory (Azure AD). Esses aplicativos podem adquirir um token silenciosamente usando a [autenticação integrada do Windows](https://aka.ms/msal-net-iwa).

Os aplicativos em execução em um dispositivo sem um navegador ainda podem chamar uma API em nome de um usuário. Para autenticar, o usuário deve entrar em outro dispositivo que tenha um navegador da Web. Este cenário requer que você use o [fluxo de código do dispositivo](https://aka.ms/msal-net-device-code-flow).

![Fluxo de código do dispositivo](media/scenarios/device-code-flow-app.svg)

Embora não recomendemos que você o use, o [fluxo de nome de usuário/senha](https://aka.ms/msal-net-up) está disponível em aplicativos cliente públicos. Esse fluxo ainda é necessário em alguns cenários, como DevOps.

Mas o uso desse fluxo impõe restrições em seus aplicativos. Por exemplo, os aplicativos que usam esse fluxo não podem entrar em um usuário que precise executar a autenticação multifator ou o acesso condicional. Seus aplicativos também não se beneficiam do logon único.

A autenticação com o fluxo de nome de usuário/senha vai contra os princípios de autenticação moderna e é fornecida somente por motivos herdados.

Em aplicativos de desktop, se você quiser que o cache de token seja persistente, você deve [Personalizar a serialização de cache de token](https://aka.ms/msal-net-token-cache-serialization). Ao implementar a [serialização de cache de token duplo](https://aka.ms/msal-net-dual-cache-serialization), você pode usar caches de token compatíveis com versões anteriores e de encaminhamento com gerações antigas de bibliotecas de autenticação. Bibliotecas específicas incluem biblioteca de autenticação do AD do Azure para .NET (ADAL.NET) versão 3 e versão 4.

Para obter mais informações, consulte [aplicativo de área de trabalho que chama APIs da Web](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Um aplicativo móvel chamando uma API da Web em nome de um usuário interativo

Semelhante a um aplicativo de área de trabalho, um aplicativo móvel chama os métodos de aquisição de token interativo da classe MSAL **PublicClientApplication** para adquirir um token para chamar uma API da Web.

![Um aplicativo móvel chamando uma API Web](media/scenarios/mobile-app.svg)

MSAL iOS e MSAL Android usam o navegador da Web do sistema por padrão. No entanto, você pode direcioná-los para usar a exibição da Web inserida. Há especificidades que dependem da plataforma móvel: Plataforma Universal do Windows (UWP), iOS ou Android.

Alguns cenários, como aqueles que envolvem o acesso condicional relacionado a uma ID de dispositivo ou um registro de dispositivo, exigem que um [agente](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) seja instalado no dispositivo. Exemplos de agentes são o Microsoft Portal da Empresa no Android e o Microsoft Authenticator no Android e no iOS. Além disso, o MSAL agora pode interagir com os agentes.

> [!NOTE]
> Seu aplicativo móvel que usa MSAL. iOS, MSAL. O Android ou o MSAL.NET no Xamarin podem ter políticas de proteção de aplicativo aplicadas a ele. Por exemplo, as políticas podem impedir que um usuário Copie texto protegido. O aplicativo móvel é [gerenciado pelo Intune](https://docs.microsoft.com/intune/app-sdk) e reconhecido pelo Intune como um aplicativo gerenciado. O [SDK de aplicativos do Intune](https://docs.microsoft.com/intune/app-sdk-get-started) é separado das bibliotecas MSAL e interage com o Azure ad por conta própria.

Para obter mais informações, consulte [aplicativo móvel que chama APIs da Web](scenario-mobile-overview.md).

### <a name="a-protected-web-api"></a>Uma API Web protegida

Você pode usar o ponto de extremidade da plataforma de identidade da Microsoft para proteger serviços Web como a API Web RESTful do seu aplicativo. Uma API Web protegida é chamada com um token de acesso para proteger os dados da API e autenticar solicitações de entrada. O chamador de uma API da Web acrescenta um token de acesso no cabeçalho de autorização de uma solicitação HTTP.

Se você quiser proteger sua API Web do ASP.NET ou ASP.NET Core, será necessário validar o token de acesso. Para essa validação, você usa o middleware JWT ASP.NET. A validação é feita pelas [extensões IdentityModel para a biblioteca .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) e não por MSAL.net.

Para obter mais informações, consulte [Protected Web API](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Uma API Web que chama outra API da Web em nome de um usuário

Para seu ASP.NET ou ASP.NET Core API da Web protegida chamar outra API da Web em nome de um usuário, seu aplicativo precisa adquirir um token para a API da Web downstream. Ele faz isso chamando o método [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) da classe **ConfidentialClientApplication** . Essas chamadas também são denominadas chamadas serviço a serviços. As APIs da Web que chamam outras APIs da Web precisam fornecer serialização de cache Personalizada.

  ![Uma API Web chamando outra API Web](media/scenarios/web-api.svg)

Para obter mais informações, consulte [API Web que chama APIs da Web](scenario-web-api-call-api-overview.md).

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Um aplicativo daemon chamando uma API Web no nome do daemon

Aplicativos que têm processos de execução longa ou que operam sem interação do usuário também precisam de uma maneira de acessar APIs da Web seguras. Esse aplicativo pode autenticar e obter tokens usando a identidade do aplicativo em vez da identidade delegada de um usuário. O aplicativo comprova sua identidade usando um certificado ou segredo do cliente.

Você pode escrever esses aplicativos de daemon que adquirem um token para o aplicativo de chamada usando os métodos de aquisição de [credenciais de cliente](https://aka.ms/msal-net-client-credentials) da classe MSAL **ConfidentialClientApplication** . Esses métodos exigem que o aplicativo de chamada tenha registrado um segredo com o Azure AD. Em seguida, o aplicativo compartilha o segredo com o daemon chamado. Exemplos desses segredos incluem senhas de aplicativo, declaração de certificado ou asserção de cliente.

![Um aplicativo daemon chamado por outros aplicativos e APIs](media/scenarios/daemon-app.svg)

Para obter mais informações, consulte [daemon Application que chama APIs da Web](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Cenários e fluxos de autenticação com suporte

Os cenários que envolvem a aquisição de tokens também são mapeados para os fluxos de autenticação do OAuth 2,0, conforme detalhado nos [protocolos da plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Cenário</th> <th>Orientação detalhada do cenário</th> <th>Fluxo e concessão do OAuth 2,0</th> <th>Audiência</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Aplicação de página única</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Localiza</a></td>
   <td>Contas corporativas ou de estudante, contas pessoais e Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Um aplicativo Web que assina usuários</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Código de autorização</a></td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Um aplicativo Web que chama APIs da Web</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Código de autorização</a></td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Um aplicativo de desktop que chama APIs da Web</a></td>
   <td>Interativo usando <a href="v2-oauth2-auth-code-flow.md">código de autorização</a> com PKCE</td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td>Autenticação integrada do Windows</td>
   <td>Contas escolares ou profissionais</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Senha do proprietário do recurso</a></td>
   <td>Contas corporativas ou de estudante e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Código do dispositivo</a></td>
   <td>Contas escolares ou profissionais</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Um aplicativo móvel que chama APIs da Web</a></td>
   <td>Interativo usando <a href="v2-oauth2-auth-code-flow.md">código de autorização</a> com PKCE</td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Senha do proprietário do recurso</a></td>
   <td>Contas corporativas ou de estudante e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href=scenario-daemon-overview.md">Um aplicativo daemon que chama APIs da Web</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Credenciais de cliente</a></td>
   <td>Permissões somente de aplicativo sem usuário e usadas somente em organizações do Azure AD</td>
 </tr>

  <tr>
   <td><a href=scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href=scenario-web-api-call-api-overview.md">Uma API Web que chama APIs da Web</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Em nome de</a></td>
   <td>Contas corporativas ou de estudante e contas pessoais</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Cenários e plataformas com suporte e linguagens

As bibliotecas de autenticação da Microsoft dão suporte a várias plataformas:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- iOS Nativo
- macOS
- Android nativo
- Java
- Python

Você também pode usar várias linguagens para compilar seus aplicativos. Observe que alguns tipos de aplicativos não estão disponíveis em todas as plataformas.

Na coluna Windows da tabela a seguir, cada vez que o .NET Core é mencionado, .NET Framework também é possível. O último é omitido para evitar a aglomeração da tabela.

|Cenário  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Aplicação de página única](scenario-spa-overview.md) <br/>[Aplicativo de página ![Single](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Aplicativo Web que assina usuários](scenario-web-app-sign-user-overview.md) <br/>[![Web aplicativo que faz logon de usuários](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Aplicativo Web que chama APIs da Web](scenario-web-app-call-api-overview.md) <br/> <br/>[![Web aplicativo que chama APIs da Web](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [Aplicação de ambiente de trabalho que chama APIs Web](scenario-desktop-overview.md) <br/> <br/>[![Desktop aplicativo que chama APIs web](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![Device fluxo de código ](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> MSAL. ObjC |
| [Aplicativo móvel que chama APIs da Web](scenario-mobile-overview.md) <br/> [![Mobile aplicativo que chama APIs da Web](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/objetivo C ou Swift](media/sample-v2-code/small_logo_iOS.png) MSAL. ObjC | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Aplicativo daemon](scenario-daemon-overview.md) <br/> [![Daemon aplicativo](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [API Web que chama APIs da Web](scenario-web-api-call-api-overview.md) <br/><br/> [![Web API que chama APIs da Web](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Consulte também [bibliotecas com suporte da Microsoft por sistema operacional/idioma](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [noções básicas de autenticação](authentication-scenarios.md) e [tokens de acesso](access-tokens.md).
