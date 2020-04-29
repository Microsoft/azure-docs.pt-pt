---
title: A autenticação da plataforma de identidade da Microsoft flui & cenários de aplicações Azure
description: Conheça os cenários de aplicação da plataforma de identidade da Microsoft, incluindo autenticação de identidades, aquisição de fichas e chamadas de APIs protegidas.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: bf47a352542be0ad4467ed420cc38f62da0d47dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534892"
---
# <a name="authentication-flows-and-application-scenarios"></a>Fluxos de autenticação e cenários de aplicação

A plataforma de identidade da Microsoft (v2.0) suporta a autenticação para diferentes tipos de arquiteturas de aplicações modernas. Todas as arquiteturas são baseadas nos protocolos padrão da indústria [OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md).  Utilizando as bibliotecas de autenticação da [plataforma de identidade Microsoft,](reference-v2-libraries.md)as aplicações autenticam identidades e adquirem fichas para aceder a APIs protegidos.

Este artigo descreve fluxos de autenticação e os cenários de aplicação em que são usados:

- [Cenários de aplicação e fluxos de autenticação suportados.](#scenarios-and-supported-authentication-flows)
- [Cenários de aplicação e plataformas e idiomas suportados.](#scenarios-and-supported-platforms-and-languages)

## <a name="application-categories"></a>Categorias de candidaturas

Os tokens podem ser adquiridos a partir de vários tipos de aplicações, incluindo:

- Web Apps
- Aplicações móveis
- Desktop apps (Aplicações de ambiente de trabalho)
- APIs da Web

Tokens também podem ser adquiridos a partir de apps que executam em dispositivos que não têm um navegador ou estão a funcionar no IoT.

As candidaturas podem ser categorizadas como na seguinte lista:

- [Recursos protegidos vs. aplicações de clientes](#protected-resources-vs-client-applications): Alguns cenários são sobre a proteção de recursos como aplicações web ou APIs web. Outros cenários são sobre adquirir um símbolo de segurança para chamar uma API web protegida.
- [Com utilizadores ou sem utilizadores](#with-users-or-without-users): Alguns cenários envolvem um utilizador inscrito, mas outros, como cenários de daemon, não envolvem um utilizador.
- [Aplicações de cliente de página única, cliente público e clientes confidenciais](#single-page-public-client-and-confidential-client-applications): Estes tipos são três grandes categorias de aplicações. Cada um é usado com diferentes bibliotecas e objetos.
- [Audiência de entrada](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): Os fluxos de autenticação disponíveis diferem consoante o público de entrada. Alguns fluxos estão disponíveis apenas para contas de trabalho ou escolar. E alguns estão disponíveis tanto para contas de trabalho como de escola e para contas pessoais da Microsoft. O público permitido depende dos fluxos de autenticação.
- [Fluxos OAuth 2.0 suportados](#scenarios-and-supported-authentication-flows): Os fluxos de autenticação são utilizados para implementar os cenários de aplicação que estão a solicitar fichas. Não existe um mapeamento um-para-um entre cenários de aplicação e fluxos de autenticação.
- [Plataformas suportadas](#scenarios-and-supported-platforms-and-languages): Nem todos os cenários de aplicação estão disponíveis para todas as plataformas.

### <a name="protected-resources-vs-client-applications"></a>Recursos protegidos vs. aplicações de clientes

Os cenários de autenticação envolvem duas atividades:

- **Adquirir fichas**de segurança para uma Web API protegida : Recomendamos que utilize bibliotecas de clientes [suportadas pela Microsoft](reference-v2-libraries.md#microsoft-supported-client-libraries) para adquirir fichas, em particular a família Microsoft Authentication Library (MSAL).
- **Proteger uma API web ou uma aplicação web**: Um dos desafios de proteger um recurso web API ou web app é validar o token de segurança. Em algumas plataformas, a Microsoft oferece [bibliotecas de middleware.](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)

### <a name="with-users-or-without-users"></a>Com utilizadores ou sem utilizadores

A maioria dos cenários de autenticação adquirem fichas em nome dos utilizadores inscritos.

![Cenários com utilizadores](media/scenarios/scenarios-with-users.svg)

No entanto, existem também cenários de aplicações daemon, em que as aplicações adquirem fichas em nome de si mesmos sem utilizador.

![Cenários com apps daemon](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Aplicações de cliente de página única, cliente público e pedidos confidenciais de clientes

As fichas de segurança podem ser adquiridas a partir de vários tipos de aplicações. Estas aplicações tendem a ser separadas em três categorias:

- **Aplicações de página única**: Também conhecidas como SPAs, estas aplicações são aplicações web nas quais os tokens são adquiridos a partir de uma aplicação JavaScript ou TypeScript em execução no navegador. Muitas aplicações modernas têm uma primeira página de aplicação que está escrita principalmente no JavaScript. A aplicação usa frequentemente uma estrutura como Angular, Reagir ou Vue. MSAL.js é a única biblioteca de autenticação da Microsoft que suporta aplicações de uma página única.

- **Aplicações**de clientes públicos : Estas aplicações assinam sempre nos utilizadores:
  - Aplicativos de desktop que ligam para a web APIs em nome do utilizador inscrito
  - Aplicações móveis
  - Aplicativos em execução em dispositivos que não têm um navegador, como aqueles que estão em execução no iOT

  Estas aplicações são representadas pela classe MSAL [PublicClientApplication.](/dotnet/api/microsoft.identity.client.publicclientapplication) Para mais informações, consulte [o cliente público e aplicações confidenciais do cliente.](msal-client-applications.md)

- **Aplicações confidenciais do cliente:**
  - Aplicativos web chamando um API web
  - Apis web chamando uma Web API
  - Aplicativos Daemon, mesmo quando implementados como um serviço de consola como um daemon Linux ou um serviço Windows

  Este tipo de aplicações utilizam a classe [ConfidentialClientApplication.](/dotnet/api/microsoft.identity.client.confidentialclientapplication) Para mais informações, consulte [o cliente público e aplicações confidenciais do cliente.](msal-client-applications.md)

## <a name="application-scenarios"></a>Cenários de aplicações

O ponto final da plataforma de identidade da Microsoft suporta a autenticação para diferentes arquiteturas de aplicações:

- Aplicações de página única
- Web Apps
- APIs da Web
- Aplicações móveis
- Aplicações nativas
- Aplicações daemon
- Aplicativos do lado do servidor

As aplicações utilizam os diferentes fluxos de autenticação para iniciar sessão nos utilizadores e obter fichas para chamadas de APIs protegidas.

### <a name="a-single-page-application"></a>Uma aplicação de uma página única

Muitas aplicações web modernas são construídas como aplicações de página única do lado do cliente. Estas aplicações utilizam o JavaScript ou uma estrutura de aplicação de uma página única como Angular, Vue.js e React.js. Estas aplicações são executadas num navegador web.

As suas características de autenticação diferem das aplicações web tradicionais do lado do servidor. Ao utilizar a plataforma de identidade da Microsoft, as aplicações de uma só página podem iniciar sessão nos utilizadores e obter fichas para aceder a serviços de back-end ou APIs web.

![Uma aplicação de uma página única](media/scenarios/spa-app.svg)

Para mais informações, consulte [aplicações de página única](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>Uma aplicação web que está assinando em um utilizador

![Uma aplicação web que assina em um utilizador](media/scenarios/scenario-webapp-signs-in-users.svg)

Para proteger uma aplicação web que está a assinar num utilizador:

- Se desenvolver em .NET, utilize ASP.NET ou ASP.NET Core com o ASP.NET Open ID Connect. Proteger um recurso implica validar o símbolo de segurança, que é feito pelas [extensões do IdentityModel para](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) a biblioteca .NET e não bibliotecas MSAL.

- Se desenvolver no Node.js, use Passport.js.

Para mais informações, consulte a [aplicação Web que assina nos utilizadores](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Uma aplicação web que assina num utilizador e chama uma API web em nome do utilizador

![Uma aplicação web chamando APIs web](media/scenarios/web-app.svg)

Para ligar para uma Web API a partir de uma aplicação web em nome de um utilizador, utilize a classe MSAL **ConfidentialClientApplication.** Utiliza o fluxo de código de autorização e armazena as fichas adquiridas na cache simbólica. Quando necessário, a MSAL refresca tokens e o controlador adquire silenciosamente tokens da cache.

Para mais informações, consulte [Uma aplicação web que chama APIs web](scenario-web-app-call-api-overview.md).

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>Uma aplicação de desktop chamando uma API web em nome de um utilizador inscrito

Para uma aplicação de desktop para chamar uma API web que assina nos utilizadores, utilize os métodos interativos de aquisição de tokens da classe MSAL **PublicClientApplication.** Com estes métodos interativos, pode controlar a experiência de ui de inscrição. A MSAL utiliza um navegador web para esta interação.

![Uma aplicação de desktop chamando uma Web API](media/scenarios/desktop-app.svg)

Existe outra possibilidade de aplicações hospedadas no Windows em computadores que se juntam a um domínio Windows ou ao Azure Ative Directory (Azure AD). Estas aplicações podem adquirir silenciosamente um símbolo utilizando a [Autenticação Integrada do Windows.](https://aka.ms/msal-net-iwa)

As aplicações que estão a funcionar num dispositivo sem um browser ainda podem chamar uma API em nome de um utilizador. Para autenticar, o utilizador deve iniciar sessão noutro dispositivo que tenha um navegador web. Este cenário requer que utilize o [Fluxo de Código](https://aka.ms/msal-net-device-code-flow)do Dispositivo .

![Fluxo de código de dispositivo](media/scenarios/device-code-flow-app.svg)

Embora não o recomendemos, o [fluxo username/password](https://aka.ms/msal-net-up) está disponível nas aplicações do cliente público. Este fluxo ainda é necessário em alguns cenários como DevOps.

Mas usar este fluxo limita as suas aplicações. Por exemplo, as aplicações não podem inscrever-se num utilizador que precisa de usar a autenticação de vários fatores ou acesso condicional. As suas candidaturas também não beneficiam de um único sinal.

A autenticação com o fluxo username/password vai contra os princípios da autenticação moderna e é fornecida apenas por razões antigas.

Em aplicações de desktop, se quiser que a cache simbólica persista, personalize a [serialização token Cache](https://aka.ms/msal-net-token-cache-serialization). Ao implementar a serialização de cache de [dois tokens,](https://aka.ms/msal-net-dual-cache-serialization)pode utilizar caches de token compatíveis para trás e compatíveis para a frente. Estes símbolos apoiam gerações anteriores de bibliotecas de autenticação. Bibliotecas específicas incluem Biblioteca de Autenticação AD Azure para a versão 3 (ADAL.NET) e versão 4.

Para mais informações, consulte a [aplicação Desktop que chama APIs web](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Uma aplicação móvel que chama uma API web em nome de um utilizador interativo

Semelhante a uma aplicação de desktop, uma aplicação móvel chama os métodos interativos de aquisição de tokens da classe MSAL **PublicClientApplication** para adquirir um símbolo para chamar uma API web.

![Uma aplicação móvel chamando uma Web API](media/scenarios/mobile-app.svg)

MSAL iOS e MSAL Android usam o navegador web do sistema por padrão. No entanto, pode direcioná-los para a utilização da Web View incorporada. Existem especificidades que dependem da plataforma móvel: Universal Windows Platform (UWP), iOS ou Android.

Alguns cenários, como os que envolvem acesso condicional relacionadocom um ID de dispositivo ou uma inscrição de dispositivo, exigem que um corretor seja instalado no dispositivo. Exemplos de corretores são o Portal da Microsoft Company no Android e Microsoft Authenticator no Android e iOS. A MSAL pode agora interagir com corretores. Para mais informações, consulte [corretores de alavancagem no Android e iOS.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)

Para mais informações, consulte a [aplicação Mobile que liga para a web APIs](scenario-mobile-overview.md).

> [!NOTE]
> A sua aplicação móvel que utiliza MSAL.iOS, MSAL. Android, ou MSAL.NET em Xamarin pode ter políticas de proteção de aplicações aplicadas ao mesmo. Por exemplo, as políticas podem impedir um utilizador de copiar texto protegido. A aplicação móvel é gerida pela Intune e reconhecida pela Intune como uma aplicação gerida. Para mais informações, consulte a visão geral do [Microsoft Intune App SDK](https://docs.microsoft.com/intune/app-sdk).
>
> O [Intune App SDK](https://docs.microsoft.com/intune/app-sdk-get-started) é separado das bibliotecas MSAL e interage com a Azure AD por si só.

### <a name="a-protected-web-api"></a>Uma API web protegida

Pode utilizar o ponto final da plataforma de identidade da Microsoft para garantir serviços web como a API web RESTful da sua aplicação. Uma API web protegida é chamada usando um símbolo de acesso. O símbolo assegura os dados da API e autentica os pedidos de entrada. O autor de uma API web anexa um sinal de acesso no cabeçalho de autorização de um pedido HTTP.

Se quiser proteger o seu ASP.NET ou ASP.NET API web Core, tem de validar o token de acesso. Para esta validação, utiliza o ASP.NET jWT middleware. A validação é feita pelas [extensões Do IdentityModel para](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) a biblioteca .NET e não por MSAL.NET.

Para mais informações, consulte [API da web protegida](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Uma API web chamando outra Web API em nome de um utilizador

Para que a sua ASP.NET ou ASP.NET Core web protected API para ligar para outra Web API em nome de um utilizador, a sua aplicação precisa de adquirir um símbolo para a Web API a jusante. Para adquirir um símbolo, a sua aplicação chama o método [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) da classe **ConfidentialClientApplication.** Estas chamadas também são chamadas de *serviço a serviço.* As APIs web que chamam outras APIs web precisam fornecer serialização de cache personalizada.

  ![Um API web chamando outra Web API](media/scenarios/web-api.svg)

Para mais informações, consulte [a Web API que chama APIs web](scenario-web-api-call-api-overview.md).

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Uma aplicação daemon chamando uma API web em nome do daemon

As aplicações que tenham processos de longo prazo ou que funcionem sem interação do utilizador também precisam de uma forma de aceder a APIs web seguras. Tal aplicação pode autenticar e obter fichas usando a identidade da aplicação. A aplicação prova a sua identidade utilizando um segredo ou certificado de cliente.

Pode escrever tais aplicações daemon que adquirem um símbolo para a aplicação de chamadas utilizando os métodos de aquisição de [credenciais](https://aka.ms/msal-net-client-credentials) de cliente da classe MSAL **ConfidentialClientApplication.** Estes métodos exigem que a aplicação de chamadas tenha registado um segredo com a Azure AD. A aplicação partilha então o segredo com o chamado daemon. Exemplos de tais segredos incluem senhas de aplicação, afirmação de certificado ou afirmação do cliente.

![Uma aplicação daemon chamada por outras apps e APIs](media/scenarios/daemon-app.svg)

Para mais informações, consulte a [aplicação Daemon que chama APIs web](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Cenários e fluxos de autenticação suportados

Cenários que envolvem a aquisição de fichas também mapeiam para os fluxos de autenticação OAuth 2.0. Para mais informações, consulte os [protocolos OAuth 2.0 e OpenID Connect na plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Cenário</th> <th>Cenário detalhado walk-through</th> <th>OAuth 2.0 fluxo e concessão</th> <th>Audiência</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Aplicação de página única</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implícito</a></td>
   <td>Contas de trabalho ou escola, contas pessoais e Microsoft Azure Ative Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Uma aplicação web que assina nos utilizadores</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Código de Autorização</a></td>
   <td>Trabalho ou contas escolares, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Uma aplicação web que chama APIs web</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Código de Autorização</a></td>
   <td>Trabalho ou contas escolares, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Uma aplicação de desktop que chama APIs web</a></td>
   <td>Interativo através do Código de <a href="v2-oauth2-auth-code-flow.md">Autorização</a> com PKCE</td>
   <td>Trabalho ou contas escolares, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td>Windows Auth integrado</td>
   <td>Contas escolares ou profissionais</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Palavra-passe do proprietário de recursos</a></td>
   <td>Trabalho ou contas escolares e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Código do dispositivo</a></td>
   <td>Contas escolares ou profissionais</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Uma aplicação móvel que chama APIs web</a></td>
   <td>Interativo através do Código de <a href="v2-oauth2-auth-code-flow.md">Autorização</a> com PKCE</td>
   <td>Trabalho ou contas escolares, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Palavra-passe do proprietário de recursos</a></td>
   <td>Trabalho ou contas escolares e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Uma aplicação daemon que chama APIs web</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Credenciais de cliente</a></td>
   <td>Permissões apenas com aplicações sem utilizador e utilizadas apenas em organizações da AD Azure</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Um API web que chama APIs web</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Em nome de</a></td>
   <td>Trabalho ou contas escolares e contas pessoais</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Cenários e plataformas e línguas apoiadas

As bibliotecas de autenticação da Microsoft suportam várias plataformas:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- IOS nativo
- macOS
- Android nativo
- Java
- Python

Também pode usar várias línguas para construir as suas aplicações.

> [!NOTE]
> Alguns tipos de aplicações não estão disponíveis em todas as plataformas.

Na coluna Windows da tabela seguinte, cada vez que é mencionado .NET Core, .NET Framework também é possível. Este último é omitido para evitar desatar a mesa.

|Cenário  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Aplicação de página única](scenario-spa-overview.md) <br/>[![Aplicativo de página única](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Aplicação web que inicia a sessão de utilizadores](scenario-web-app-sign-user-overview.md) <br/>[![Aplicação web que inscreve utilizadores](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![Núcleo de ASP.NET](media/sample-v2-code/small_logo_NETcore.png)<br/>Núcleo de ASP.NET | ![Núcleo de ASP.NET](media/sample-v2-code/small_logo_NETcore.png)<br/>Núcleo de ASP.NET | ![Núcleo de ASP.NET](media/sample-v2-code/small_logo_NETcore.png)<br/>Núcleo de ASP.NET
| [Aplicação Web que chama as APIs Web](scenario-web-app-call-api-overview.md) <br/> <br/>[![Aplicação Web que chama as APIs Web](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![Núcleo de ASP.NET](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Balão + MSAL Python| ![Núcleo de ASP.NET](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Balão + MSAL Python| ![Núcleo de ASP.NET](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Balão + MSAL Python
| [Aplicação de ambiente de trabalho que chama APIs Web](scenario-desktop-overview.md) <br/> <br/>Aplicação de desktop que chama fluxo de código do dispositivo ![APIs web [ ![](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md)](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS / Objetivo C ou rápido](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [Aplicação móvel que chama as APIs Web](scenario-mobile-overview.md) <br/> [![Aplicação móvel que chama as APIs Web](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Objetivo C ou rápido](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Aplicações daemon](scenario-daemon-overview.md) <br/> [![Aplicações daemon](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [API Web que chama APIs Web](scenario-web-api-call-api-overview.md) <br/><br/> [![API Web que chama APIs Web](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![Núcleo de ASP.NET](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Para mais informações, consulte [bibliotecas apoiadas pela Microsoft por OS/idioma](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [fundamentos da Autenticação](authentication-scenarios.md) e os [tokens](access-tokens.md)de acesso à plataforma de identidade da Microsoft.
* Saiba mais sobre [garantir o acesso a aplicações IoT](/azure/architecture/example-scenario/iot-aad/iot-aad).
