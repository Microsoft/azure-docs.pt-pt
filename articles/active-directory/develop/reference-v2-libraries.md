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
ms.openlocfilehash: 228a15e9e9e27cbcfd71d4762db2f4ab9f6dfffe
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560151"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliotecas de autenticação de plataformas de identidade da Microsoft

As tabelas a seguir mostram o suporte da Microsoft Authentication Library para vários tipos de aplicações. Incluem links para o código fonte da biblioteca, onde obter o pacote para o projeto da sua aplicação, e se a biblioteca suporta o sign-in do utilizador (autenticação), acesso a APIs web protegidas (autorização), ou ambos.

A plataforma de identidade da Microsoft foi certificada pela OpenID Foundation como um [fornecedor certificado de OpenID.](https://openid.net/certification/) Se preferir utilizar uma biblioteca que não seja a Microsoft Authentication Library (MSAL) ou outra biblioteca suportada pela Microsoft, escolha uma com uma [implementação certificada do OpenID Connect](https://openid.net/developers/certified/).

Se optar por codificar manualmente a sua própria implementação ao nível do protocolo de [OAuth 2.0 ou OpenID Connect 1.0,](active-directory-v2-protocols.md)preste muita atenção às considerações de segurança na especificação de cada padrão e siga uma metodologia de ciclo de vida de desenvolvimento de software (SDL) como o [Microsoft SDL][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Aplicação de uma página única (SPA)

Uma aplicação de uma página única é inteiramente executado na superfície do navegador e recolhe dados de página (HTML, CSS e JavaScript) dinamicamente ou no tempo de carregamento da aplicação. Pode chamar APIs web para interagir com fontes de dados de back-end.

Como o código de um SPA funciona inteiramente no navegador, é considerado um *cliente público* que é incapaz de armazenar segredos de forma segura.

| Linguagem / enquadramento | Projeto em<br/>GitHub                                                                                                    | Pacote                                                                      | Ficando<br/>começou                             | Inscreva-se nos utilizadores                                         | Aceda a APIs web                                                 | Geralmente disponível (GA) *ou*<br/>Pré-visualização pública<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL Angular 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | Pré-visualização pública                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [Tutorial](tutorial-v2-angular.md)              | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | Pré-visualização pública                                               |
| JavaScript           | [MSAL.js 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Tutorial](tutorial-v2-javascript-auth-code.md) | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| JavaScript           | [MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)              | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)     | [Tutorial](tutorial-v2-javascript-spa.md) | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| React                | [Reação MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | Pré-visualização pública                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Os termos de utilização suplementares para as pré-visualizações do Microsoft Azure][preview-tos] aplicam-se às bibliotecas em *pré-visualização pública*.

## <a name="web-application"></a>Aplicação Web

Uma aplicação web executa código num servidor que gera e envia HTML, CSS e JavaScript para o navegador web de um utilizador a ser renderizado. A identidade do utilizador é mantida como uma sessão entre o navegador do utilizador (a extremidade frontal) e o servidor web (a parte traseira).

Como o código de uma aplicação web é executado no servidor web, é considerado um *cliente confidencial* que pode armazenar segredos de forma segura.

| Linguagem / enquadramento | Projeto em<br/>GitHub                                                                                     | Pacote                                                                                                    | Ficando<br/>começou                               | Inscreva-se nos utilizadores                                            | Aceda a APIs web                                                    | Geralmente disponível (GA) *ou*<br/>Pré-visualização pública<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![A biblioteca não pode solicitar fichas de identificação para o ser-in do utilizador.][n] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y]    | GA                                                           |
| ASP.NET Core         | [Segurança ASP.NET](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y]    | ![A biblioteca não pode solicitar o acesso a fichas de acesso para APIs web protegidas.][n] | GA                                                           |
| ASP.NET Core         | [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y]    | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [Início rápido](quickstart-v2-java-webapp.md)        | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y]    | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y]    | GA                                                           |
| Node.js              | [Nó MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-nó](https://www.npmjs.com/package/@azure/msal-node)                                                | [Início rápido](quickstart-v2-nodejs-webapp-msal.md) | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y]    | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y]    | GA                                               |
| Node.js              | [Passaporte Azure AD](https://github.com/AzureAD/passport-azure-ad)                                         | [passaporte-azure-ad](https://www.npmjs.com/package/passport-azure-ad)                                       | [Início rápido](quickstart-v2-nodejs-webapp.md)      | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y]    | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| Python               | [Pitão MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [Início rápido](quickstart-v2-python-webapp.md)      | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y]    | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Os termos de utilização suplementares para as pré-visualizações do Microsoft Azure][preview-tos] aplicam-se às bibliotecas em *pré-visualização pública*.

## <a name="desktop-application"></a>Aplicação para desktop

Uma aplicação para desktop é tipicamente binária (compilada) código que aparece numa interface de utilizador e destina-se a ser executada no ambiente de trabalho de um utilizador.

Como uma aplicação de desktop é executado no ambiente de trabalho do utilizador, é considerado um *cliente público* que é incapaz de armazenar segredos de forma segura.

| Linguagem / enquadramento | Projeto em<br/>GitHub                                                                                     | Pacote                                                                               | Ficando<br/>começou                        | Inscreva-se nos utilizadores                                         | Aceda a APIs web                                                 | Geralmente disponível (GA) *ou*<br/>Pré-visualização pública<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Eletrão             | [Nó MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | [Tutorial](tutorial-v2-nodejs-desktop.md)   | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| macOS (Swift/Obj-C)  | [MSAL para iOS e macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Tutorial](tutorial-v2-ios.md)             | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Tutorial](tutorial-v2-windows-uwp.md)     | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Tutorial](tutorial-v2-windows-desktop.md) | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Os termos de utilização suplementares para as pré-visualizações do Microsoft Azure][preview-tos] aplicam-se às bibliotecas em *pré-visualização pública*.

## <a name="mobile-application"></a>Aplicação móvel

Uma aplicação móvel é tipicamente um código binário (compilado) que aparece numa interface de utilizador e destina-se a funcionar no dispositivo móvel de um utilizador.

Como uma aplicação móvel funciona no dispositivo móvel do utilizador, é considerado um *cliente público* que não consegue armazenar segredos de forma segura.

| Plataforma          | Projeto em<br/>GitHub                                                                          | Pacote                                                                               | Ficando<br/>começou                    | Inscreva-se nos utilizadores                                         | Aceda a APIs web                                                 | Geralmente disponível (GA) *ou*<br/>Pré-visualização pública<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Início rápido](quickstart-v2-android.md) | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| iOS (Swift/Obj-C) | [MSAL para iOS e macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Tutorial](tutorial-v2-ios.md)         | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Os termos de utilização suplementares para as pré-visualizações do Microsoft Azure][preview-tos] aplicam-se às bibliotecas em *pré-visualização pública*.

## <a name="service--daemon"></a>Serviço / daemon

Os serviços e os daemons são geralmente utilizados para o servidor-a-servidor e outras comunicações sem vigilância (por vezes chamadas *de sem cabeça).* Como não há utilizador no teclado para introduzir credenciais ou consentimento para o acesso a recursos, estas aplicações autenticam-se como si mesmos, não como utilizador, ao solicitar o acesso autorizado aos recursos de uma API web.

Um serviço ou daemon que funciona num servidor é considerado um *cliente confidencial* que pode armazenar os seus segredos de forma segura.

| Linguagem / enquadramento | Projeto em<br/>GitHub                                                                 | Pacote                                                                                | Ficando<br/>começou                           | Inscreva-se nos utilizadores                                            | Aceda a APIs web                                                 | Geralmente disponível (GA) *ou*<br/>Pré-visualização pública<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Início rápido](quickstart-v2-netcore-daemon.md) | ![A biblioteca não pode solicitar fichas de identificação para o ser-in do utilizador.][n] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![A biblioteca não pode solicitar fichas de identificação para o ser-in do utilizador.][n] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| Nó               | [Nó MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-nó](https://www.npmjs.com/package/@azure/msal-node)  | [Início rápido](quickstart-v2-nodejs-console.md)  | ![A biblioteca não pode solicitar fichas de identificação para o ser-in do utilizador.][n] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA  |
| Python               | [Pitão MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | —  | ![A biblioteca não pode solicitar fichas de identificação para o ser-in do utilizador.][n] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Os termos de utilização suplementares para as pré-visualizações do Microsoft Azure][preview-tos] aplicam-se às bibliotecas em *pré-visualização pública*.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Biblioteca de Autenticação da Microsoft, consulte a [visão geral da Biblioteca de Autenticação da Microsoft (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
