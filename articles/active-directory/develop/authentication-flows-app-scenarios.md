---
title: A autenticação da plataforma de identidade da Microsoft flui & cenários de aplicações | Rio Azure
description: Conheça os cenários de aplicação para a plataforma de identidade da Microsoft, incluindo a autenticação de identidades, a aquisição de fichas e chamadas de APIs protegidas.
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
ms.openlocfilehash: ffa52805a5e2680d534b2b24a210465cb3fc7cac
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100557871"
---
# <a name="authentication-flows-and-application-scenarios"></a>Fluxos de autenticação e cenários de aplicação

A plataforma de identidade da Microsoft suporta a autenticação para diferentes tipos de arquiteturas de aplicações modernas. Todas as arquiteturas são baseadas nos protocolos padrão da indústria [OAuth 2.0 e OpenID Connect.](active-directory-v2-protocols.md) Ao utilizar as [bibliotecas de autenticação da plataforma de identidade microsoft, as](reference-v2-libraries.md)aplicações autenticam identidades e adquirem fichas para aceder a APIs protegidas.

Este artigo descreve fluxos de autenticação e os cenários de aplicação em que são usados.

## <a name="application-categories"></a>Categorias de candidaturas

Os tokens podem ser adquiridos a partir de vários tipos de aplicações, incluindo:

- Web Apps
- Aplicações móveis
- Desktop apps (Aplicações de ambiente de trabalho)
- APIs da Web

Os tokens também podem ser adquiridos por apps que executam em dispositivos que não têm um browser ou estão a correr na Internet das Coisas (IoT).

As seguintes secções descrevem as categorias de aplicações.

### <a name="protected-resources-vs-client-applications"></a>Recursos protegidos vs. aplicações de clientes

Os cenários de autenticação envolvem duas atividades:

- **Aquisição de fichas de segurança para uma API web protegida:** Recomendamos que utilize a [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md), desenvolvida e suportada pela Microsoft.
- **Proteger uma API web ou uma aplicação web**: Um dos desafios de proteger estes recursos é validar o token de segurança. Em algumas plataformas, a Microsoft oferece [bibliotecas de middleware.](reference-v2-libraries.md)

### <a name="with-users-or-without-users"></a>Com utilizadores ou sem utilizadores

A maioria dos cenários de autenticação adquirem fichas em nome de utilizadores inscritos.

![Cenários com os utilizadores](media/scenarios/scenarios-with-users.svg)

No entanto, existem também aplicações daemon. Nestes cenários, as aplicações adquirem fichas em nome de si mesmas sem utilizador.

![Cenários com apps daemon](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Aplicações de clientes individuais, cliente público e confidenciais

As fichas de segurança podem ser adquiridas por vários tipos de aplicações. Estas aplicações tendem a ser separadas nas três categorias seguintes. Cada um é usado com diferentes bibliotecas e objetos.

- **Aplicações de página única**: Também conhecidas como SPAs, estas são aplicações web nas quais os tokens são adquiridos por uma aplicação JavaScript ou TypeScript em execução no navegador. Muitas aplicações modernas têm uma aplicação de uma página na parte frontal que está escrita principalmente no JavaScript. A aplicação usa frequentemente uma estrutura como Angular, React ou Vue. MSAL.js é a única biblioteca de autenticação da Microsoft que suporta aplicações de uma só página.

- **Aplicações de clientes públicos**: Aplicações nesta categoria, como os seguintes tipos, assinam sempre nos utilizadores:
  - Aplicativos de desktop que chamam APIs web em nome de utilizadores inscritos
  - Aplicações móveis
  - Aplicativos em execução em dispositivos que não têm um navegador, como aqueles que estão correndo em IoT

- **Aplicações confidenciais para clientes**: As aplicações nesta categoria incluem:
  - Aplicativos web que chamam uma API web
  - APIs web que chamam uma API web
  - Aplicações Daemon, mesmo quando implementadas como um serviço de consola como um daemon Linux ou um serviço Windows

### <a name="sign-in-audience"></a>Público-alvo de início de sessão

Os fluxos de autenticação disponíveis diferem consoante o público de entrada. Alguns fluxos estão disponíveis apenas para contas de trabalho ou escola. Outros estão disponíveis tanto para contas de trabalho ou escolas como para contas pessoais da Microsoft.

Para obter mais informações, consulte [os tipos de conta suportados.](v2-supported-account-types.md#account-type-support-in-authentication-flows)

## <a name="application-scenarios"></a>Cenários de aplicações

A plataforma de identidade da Microsoft suporta a autenticação para estas arquiteturas de aplicações:

- Aplicações de página única
- Web Apps
- APIs da Web
- Aplicações móveis
- Aplicações nativas
- Aplicações daemon
- Aplicativos do lado do servidor

As aplicações utilizam os diferentes fluxos de autenticação para assinar nos utilizadores e obter fichas para chamar APIs protegidas.

### <a name="single-page-application"></a>Aplicação de página única

Muitas aplicações web modernas são construídas como aplicações de página única do lado do cliente. Estas aplicações utilizam JavaScript ou uma estrutura como Angular, Vue e React. Estas aplicações são executadas num navegador web.

As aplicações de uma só página diferem das aplicações web tradicionais do lado do servidor em termos de características de autenticação. Ao utilizar a plataforma de identidade da Microsoft, as aplicações de uma página podem iniciar símis nos utilizadores e obter fichas para aceder a serviços de back-end ou APIs web. A plataforma de identidade da Microsoft oferece dois tipos de subvenções para aplicações JavaScript:

| MSAL.js (2.x) | MSAL.js (1.x) |
|---|---|
| ![Um auth de aplicação de uma página](media/scenarios/spa-app-auth.svg) | ![Uma aplicação de uma página implícita](media/scenarios/spa-app.svg) |

### <a name="web-app-that-signs-in-a-user"></a>Aplicação web que assina em um utilizador

![Uma aplicação web que assina em um utilizador](media/scenarios/scenario-webapp-signs-in-users.svg)

Para ajudar a proteger uma aplicação web que assina num utilizador:

- Se desenvolver em .NET, utilize ASP.NET ou ASP.NET Core com o middleware openID connect ASP.NET. Proteger um recurso envolve validar o token de segurança, que é feito pelas [extensões IdentityModel para bibliotecas .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) e não MSAL.

- Se se desenvolver em Node.js, utilize [o nó MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) ou [Passport.js](https://github.com/AzureAD/passport-azure-ad).

Para obter mais informações, consulte [a aplicação Web que assina nos utilizadores.](scenario-web-app-sign-user-overview.md)

### <a name="web-app-that-signs-in-a-user-and-calls-a-web-api-on-behalf-of-the-user"></a>Aplicação web que assina em um utilizador e chama uma API web em nome do utilizador

![Uma aplicação web chamando APIs web](media/scenarios/web-app.svg)

Para ligar para uma API web a partir de uma aplicação web em nome de um utilizador, use o fluxo de código de autorização e guarde os tokens adquiridos na cache simbólica. Quando necessário, a MSAL atualiza fichas e o controlador adquire silenciosamente fichas da cache.

Para obter mais informações, consulte [a aplicação Web que chama APIs web](scenario-web-app-call-api-overview.md).

### <a name="desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user"></a>Aplicativo de desktop que chama uma API web em nome de um utilizador inscrito

Para uma aplicação de ambiente de trabalho para chamar uma API web que assina nos utilizadores, utilize os métodos de aquisição de token interativos da MSAL. Com estes métodos interativos, você pode controlar a experiência de UI de inscrição. A MSAL utiliza um navegador web para esta interação.

![Uma aplicação de desktop chamando uma API web](media/scenarios/desktop-app.svg)

Existe outra possibilidade para aplicações hospedadas no Windows em computadores unidos quer a um domínio Windows quer por Azure Ative Directory (Azure AD). Estas aplicações podem adquirir silenciosamente um símbolo utilizando a [Autenticação Integrada do Windows.](https://aka.ms/msal-net-iwa)

As aplicações que executam num dispositivo sem navegador ainda podem ligar para uma API em nome de um utilizador. Para autenticar, o utilizador deve iniciar sôms em outro dispositivo que tenha um navegador web. Este cenário requer que utilize o fluxo de código do [dispositivo](https://aka.ms/msal-net-device-code-flow).

![Fluxo de código do dispositivo](media/scenarios/device-code-flow-app.svg)

Embora não recomendemos que o utilize, o [username/password flow](scenario-desktop-acquire-token.md#username-and-password) está disponível em aplicações de clientes públicos. Este fluxo ainda é necessário em alguns cenários como DevOps.

A utilização do nome de utilizador/fluxo de senha limita as suas aplicações. Por exemplo, as aplicações não podem assinar num utilizador que precise de utilizar a autenticação multifactor ou a ferramenta de Acesso Condicional em AD Azure. As suas aplicações também não beneficiam de uma única inscrição. A autenticação com o nome de utilizador/fluxo de senha vai contra os princípios da autenticação moderna e é fornecida apenas por razões antigas.

Nas aplicações para desktop, se quiser que o cache simbólico persista, pode personalizar [a serialização](scenario-desktop-acquire-token.md#file-based-token-cache)da cache simbólica . Ao implementar [a serialização de cache de dois símbolos,](scenario-desktop-acquire-token.md#dual-token-cache-serialization-msal-unified-cache--adal-v3)pode utilizar caches token compatíveis com retro-compatível e compatíveis com a frente. Estes tokens suportam gerações anteriores de bibliotecas de autenticação. Bibliotecas específicas incluem Biblioteca de Autenticação AD Azure para a versão 3 (ADAL.NET) e a versão 4.

Para obter mais informações, consulte [a aplicação Desktop que chama APIs web](scenario-desktop-overview.md).

### <a name="mobile-app-that-calls-a-web-api-on-behalf-of-an-interactive-user"></a>Aplicativo móvel que chama uma API web em nome de um utilizador interativo

Semelhante a uma aplicação de desktop, uma aplicação móvel chama os métodos interativos de aquisição de fichas da MSAL para adquirir um símbolo para chamar uma API web.

![Uma aplicação móvel chamando uma API web](media/scenarios/mobile-app.svg)

MSAL iOS e MSAL Android usam o navegador web do sistema por padrão. No entanto, pode direcioná-los para a utilização da vista web incorporada. Existem especificidades que dependem da plataforma móvel: Universal Windows Platform (UWP), iOS ou Android.

Alguns cenários, como os que envolvem Acesso Condicional relacionado com um ID do dispositivo ou uma inscrição de dispositivo, exigem que seja instalado um corretor no dispositivo. Exemplos de corretores são o Portal da Microsoft Company para Android e Microsoft Authenticator para Android e iOS. A MSAL pode agora interagir com corretores. Para obter mais informações sobre corretores, consulte [os corretores De alavancagem no Android e iOS.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)

Para mais informações, consulte [a aplicação Mobile que chama APIs web](scenario-mobile-overview.md).

> [!NOTE]
> Uma aplicação móvel que usa MSAL.iOS, MSAL. Android, ou MSAL.NET em Xamarin pode ter políticas de proteção de aplicações aplicadas ao mesmo. Por exemplo, as políticas podem impedir um utilizador de copiar texto protegido. A aplicação móvel é gerida pela Intune e é reconhecida pela Intune como uma aplicação gerida. Para obter mais informações, consulte [a visão geral da App SDK da Microsoft Intune](/intune/app-sdk).
>
> A [Aplicação Intune SDK](/intune/app-sdk-get-started) é separada das bibliotecas MSAL e interage com a Azure AD por si só.

### <a name="protected-web-api"></a>API web protegida

Pode utilizar o ponto final da plataforma de identidade da Microsoft para garantir serviços web como a API web RESTful da sua aplicação. Uma API web protegida é chamada através de um token de acesso. O token ajuda a proteger os dados da API e a autenticar pedidos de entrada. O chamador de uma API web anexa um token de acesso no cabeçalho de autorização de um pedido HTTP.

Se quiser proteger o seu ASP.NET ou ASP.NET Core web API, valide o token de acesso. Para esta validação, utilize o middleware JWT ASP.NET. A validação é feita pelas [extensões IdentityModel para biblioteca .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) e não por MSAL.NET.

Para mais informações, consulte [a API da web Protegida.](scenario-protected-web-api-overview.md)

### <a name="web-api-that-calls-another-web-api-on-behalf-of-a-user"></a>Web API que chama outra API web em nome de um utilizador

Para que a sua API web protegida ligue para outra API web em nome de um utilizador, a sua aplicação precisa de adquirir um símbolo para a API web a jusante. Tais chamadas são por vezes referidas como chamadas *de serviço para serviço.* As APIs web que chamam outras APIs web precisam fornecer serialização personalizada de cache.

![Uma API web chamando outra API web](media/scenarios/web-api.svg)

Para obter mais informações, consulte [a API web que chama APIs web](scenario-web-api-call-api-overview.md).

### <a name="daemon-app-that-calls-a-web-api-in-the-daemons-name"></a>Daemon app que chama uma API web em nome do daemon

As aplicações que têm processos de longa duração ou que operam sem interação do utilizador também precisam de uma forma de aceder a APIs web seguras. Tal aplicação pode autenticar e obter fichas usando a identidade da aplicação. A aplicação comprovam a sua identidade utilizando um segredo ou certificado do cliente.

Você pode escrever tais aplicações daemon que adquirem um símbolo para a app de chamada usando os métodos de aquisição [credenciais](scenario-daemon-acquire-token.md#acquiretokenforclient-api) do cliente em MSAL. Estes métodos requerem um segredo de cliente que adiciona ao registo da aplicação no Azure AD. A aplicação partilha então o segredo com o chamado Daemon. Exemplos de tais segredos incluem senhas de aplicação, afirmação de certificado e afirmação do cliente.

![Uma app daemon chamada por outras apps e APIs](media/scenarios/daemon-app.svg)

Para mais informações, consulte a [aplicação Daemon que chama APIs web](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Cenários e fluxos de autenticação suportados

Utiliza fluxos de autenticação para implementar os cenários de aplicação que estão a solicitar fichas. Não há um mapeamento um-para-um entre cenários de aplicação e fluxos de autenticação.

Os cenários que envolvem a aquisição de fichas também mapeiam para os fluxos de autenticação OAuth 2.0. Para obter mais informações, consulte [os protocolos OAuth 2.0 e OpenID Connect na plataforma de identidade da Microsoft.](active-directory-v2-protocols.md)

<table>
 <thead>
  <tr><th>Scenario</th> <th>Cenário detalhado</th> <th>OAuth 2.0 fluxo e concessão</th> <th>Audiência</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Auth code" src="media/scenarios/spa-app-auth.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Aplicação de página única</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Código de autorização</a> com PKCE</td>
   <td>Contas de trabalho ou escola, contas pessoais e Diretório Ativo Azure B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Implicit" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Aplicação de página única</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implícito</a></td>
   <td>Contas de trabalho ou escola, contas pessoais e Diretório Ativo Azure B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Aplicação web que inicia a sessão de utilizadores</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Código de autorização</a></td>
   <td>Contas de trabalho ou escola, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that calls web APIs" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Aplicação Web que chama as APIs Web</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Código de autorização</a></td>
   <td>Contas de trabalho ou escola, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Aplicação de ambiente de trabalho que chama APIs Web</a></td>
   <td>Interativo utilizando <a href="v2-oauth2-auth-code-flow.md">código de autorização</a> com PKCE</td>
   <td>Contas de trabalho ou escola, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td>Autenticação Integrada do Windows.</td>
   <td>Contas escolares ou profissionais</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Senha do proprietário de recursos</a></td>
   <td>Contas de trabalho ou escola e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Código do dispositivo</a></td>
   <td>Contas de trabalho ou escola, contas pessoais e Azure AD B2C</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Aplicação móvel que chama as APIs Web</a></td>
   <td>Interativo utilizando <a href="v2-oauth2-auth-code-flow.md">código de autorização</a> com PKCE</td>
   <td>Contas de trabalho ou escola, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Senha do proprietário de recursos</a></td>
   <td>Contas de trabalho ou escola e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">App Daemon que chama APIs web</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Credenciais de cliente</a></td>
   <td>Permissões só para aplicações que não têm utilizador e são usadas apenas em organizações AD Azure</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">API Web que chama APIs Web</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Em nome de</a></td>
   <td>Contas de trabalho ou escola e contas pessoais</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Cenários e plataformas e línguas apoiadas

As bibliotecas de autenticação da Microsoft suportam várias plataformas:

- .NET Core
- .NET Framework
- Java
- JavaScript
- macOS
- Android nativo
- IOS nativo
- Node.js
- Python
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android

Também pode usar vários idiomas para construir as suas aplicações.

> [!NOTE]
> Alguns tipos de aplicações não estão disponíveis em todas as plataformas.

Na coluna do Windows da tabela seguinte, cada vez que é mencionado .NET Core, o Quadro .NET também é possível. Este último é omitido para evitar desarrumar a mesa.

|Scenario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Aplicação de página única](scenario-spa-overview.md) <br/>[![App Auth de uma página única](media/scenarios/spa-app-auth.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Aplicação de página única](scenario-spa-overview.md) <br/>[![App de uma página única implícita](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Aplicação web que inicia a sessão de utilizadores](scenario-web-app-sign-user-overview.md) <br/>[![Aplicação web que assina utilizadores](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>
| [Aplicação Web que chama as APIs Web](scenario-web-app-call-api-overview.md) <br/> <br/>[![Aplicação Web que chama as APIs Web](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![Pitão MSAL](media/sample-v2-code/small_logo_python.png)<br/>Balão + MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![Pitão MSAL](media/sample-v2-code/small_logo_python.png)<br/>Balão + MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![Pitão MSAL](media/sample-v2-code/small_logo_python.png)<br/>Balão + MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>
| [Aplicação de ambiente de trabalho que chama APIs Web](scenario-desktop-overview.md) <br/> <br/>[ ![ Aplicativo de desktop que chama APIs](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![ web Fluxo de código do dispositivo](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![Pitão MSAL](media/sample-v2-code/small_logo_python.png)<br/>Pitão MSAL ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![Pitão MSAL](media/sample-v2-code/small_logo_python.png)<br/>Pitão MSAL ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![Pitão MSAL](media/sample-v2-code/small_logo_python.png)<br/>Pitão MSAL <br/> ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/> ![iOS / Objetivo C ou rápido](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [Aplicação móvel que chama as APIs Web](scenario-mobile-overview.md) <br/> [![Aplicação móvel que chama as APIs Web](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Objetivo C ou rápido](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) O MSAL. Android
| [Aplicações daemon](scenario-daemon-overview.md) <br/> [![Aplicações daemon](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![Pitão MSAL](media/sample-v2-code/small_logo_python.png)<br/>Pitão MSAL ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![Pitão MSAL](media/sample-v2-code/small_logo_python.png)<br/>Pitão MSAL ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![Pitão MSAL](media/sample-v2-code/small_logo_python.png)<br/>Pitão MSAL ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>
| [API Web que chama APIs Web](scenario-web-api-call-api-overview.md) <br/><br/> [![API Web que chama APIs Web](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![Pitão MSAL](media/sample-v2-code/small_logo_python.png)<br/>Pitão MSAL ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![Pitão MSAL](media/sample-v2-code/small_logo_python.png)<br/>Pitão MSAL ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![Pitão MSAL](media/sample-v2-code/small_logo_python.png)<br/>Pitão MSAL ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>

Para obter mais informações, consulte [as bibliotecas de autenticação da plataforma de identidade da Microsoft.](reference-v2-libraries.md)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [os básicos de autenticação](./authentication-vs-authorization.md) e [os tokens de acesso na plataforma de identidade da Microsoft.](access-tokens.md)
* Saiba mais sobre [garantir o acesso a aplicações IoT.](/azure/architecture/example-scenario/iot-aad/iot-aad)
