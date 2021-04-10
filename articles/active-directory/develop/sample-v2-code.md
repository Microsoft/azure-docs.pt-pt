---
title: Amostras de código para plataforma de identidade da Microsoft
description: Fornece um índice de amostras de código de plataforma de identidade disponíveis da Microsoft, organizado por cenário.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: dc740acb366212640015ca0fb527d34fade1097d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010767"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Amostras de código da plataforma de identidade da Microsoft (ponto final v2.0)

Pode utilizar a plataforma de identidade da Microsoft para:

- Adicione autenticação e autorização às suas aplicações web e APIs web.
- Requer um token de acesso para aceder a uma API web protegida.

Este artigo descreve e fornece-lhe links para amostras para a plataforma de identidade da Microsoft. Estas amostras mostram-lhe como é feito, e também fornecem códigos que pode usar nas suas aplicações. Na página de amostra de código, você encontrará tópicos de leitura detalhadas que ajudam com requisitos, instalação e configuração. Os comentários dentro do código ajudam-no a compreender as secções críticas.

Para compreender o cenário básico de cada tipo de amostra, consulte [os tipos de aplicações para a plataforma de identidade da Microsoft.](v2-app-types.md)

Também pode contribuir para as amostras no GitHub. Para saber como, consulte [as amostras e documentação do Microsoft Azure Ative Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplicações de uma página única

Estas amostras mostram como escrever uma aplicação de uma página protegida com a plataforma de identidade da Microsoft. Estas amostras usam um dos sabores do MSAL.js.

| Plataforma | Description | Ligação |
| -------- | --------------------- | -------- |
| ![Esta imagem mostra o logótipo JavaScript ](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA chama Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Esta imagem mostra o logótipo JavaScript ](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA chama Microsoft Graph usando Auth Code Flow c/ PKCE |[javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![Esta imagem mostra o logótipo JavaScript ](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA chama B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Esta imagem mostra o logótipo JavaScript ](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA chama B2C usando Auth Code Flow c/PKCE |[b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) |
| ![Esta imagem mostra o logótipo JavaScript ](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA chama API web personalizada que por sua vez chama Microsoft Graph  | [ms-identidade-javascript-tutorial-capítulo4-obo](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph) |
| ![Esta imagem mostra o logótipo angular ](media/sample-v2-code/logo_angular.png) [angular (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA chama Microsoft Graph  | [active-directy-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Esta imagem mostra o logótipo angular ](media/sample-v2-code/logo_angular.png) [angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA chama Microsoft Graph usando Auth Code Flow c/ PKCE | [ms-identidade-javascript-angular-spa](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa) |
| ![Esta imagem mostra o logótipo angular ](media/sample-v2-code/logo_angular.png) [angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA chama API web personalizada | [ms-identidade-javascript-angular-spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Esta imagem mostra o logótipo angular ](media/sample-v2-code/logo_angular.png) [angular (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA chama B2C |[active-directório-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Esta imagem mostra o logótipo angular ](media/sample-v2-code/logo_angular.png) [angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA chama API web personalizada com papéis de aplicações e grupos de segurança |[ms-identidade-javascript-angular-spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |
| ![Esta imagem mostra o logótipo ](media/sample-v2-code/logo_react.png) [Reagir (MSAL React)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| SPA chama Microsoft Graph usando Auth Code Flow c/ PKCE | [ms-identidade-javascript-react-spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) |
| ![Esta imagem mostra o logótipo ](media/sample-v2-code/logo_react.png) [Reagir (MSAL React)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| SPA chama a API web personalizada | [ms-identidade-javascript-react-tutorial](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api) |
| ![Esta imagem mostra o logótipo ](media/sample-v2-code/logo_react.png) [Reagir (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA chama API web personalizada que por sua vez chama Microsoft Graph  | [ms-identity-javascript-react-spa-dotnetcore-webapi-obo](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Esta imagem mostra o logótipo ](media/sample-v2-code/logo-blazor.png) [Blazor WebAssembly (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | Blazor WebAssembly Tutorial para utilizadores de inscrição e ligar para APIs com diretório ativo Azure |[ms-identidade-blazor-wasm](https://github.com/Azure-Samples/ms-identity-blazor-wasm) |

## <a name="web-applications"></a>Aplicações Web

As amostras que se seguem ilustram as aplicações web que assinam nos utilizadores. Algumas amostras também demonstram a aplicação chamada Microsoft Graph, ou a sua própria API web com a identidade do utilizador.

| Plataforma | Apenas sinais nos utilizadores | Sinais nos utilizadores e chamadas Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Esta imagem mostra o logotipo do Núcleo ASP.NET](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [tutorial de sinais de ASP.NET Core WebApp](https://aka.ms/aspnetcore-webapp-sign-in) | A mesma amostra na [aplicação web core ASP.NET chama](https://aka.ms/aspnetcore-webapp-call-msgraph) fase do Microsoft Graph</p>Amostra avançada [Aceder à cache simbólica do utilizador registado a partir de aplicações de fundo, APIs e serviços](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) |
| ![Esta imagem mostra o logótipo do Quadro de ASP.NET](media/sample-v2-code/logo_NETframework.png)</p>ASP.NET Core | [AD FS para Azure AD aplicação de migração para desenvolvedores](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) aprender a migrar de forma segura e segura suas aplicações integradas com Serviços da Federação de Diretório Ativo (AD FS) para Azure Ative Directory (Azure AD) | |
| ![Esta imagem mostra o logótipo do Quadro de ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET Quickstart](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restrito-âmbitos-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png)  |[Tutorial java Servlet - Capítulo 1.1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Inscreva-se com AAD| |
| ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png)  |[Java Servlet Tutorial - Capítulo 1.2](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Inscreva-se com B2C |
| ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png)  | | [Java Servlet Tutorial - Capítulo 2.1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Inscreva-se com AAD e ligue para o Graph|
| ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png)  |[Tutorial java Servlet - Capítulo 3.1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Iniciar sedução com AAD e aceder ao controlo com reivindicação de Funções| |
| ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png)  | | [Java Servlet Tutorial - Capítulo 3.2](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Inscreva-se com a AAD e controle o acesso com a reivindicação de Grupos|
| ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png) | |[Tutorial java Servlet - Capítulo 4.1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Implementar para o Serviço de Aplicações Azure|
| ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png)  | | [ms-identidade-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png)  | [ms-identidade-b2c-java-servlet-webapp-autenticação](https://github.com/Azure-Samples/ms-identity-b2c-java-servlet-webapp-authentication)|  |
| ![Esta imagem mostra o logótipo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (nó MSAL) | [Express web app signs-in utilizadores tutorial](https://github.com/Azure-Samples/ms-identity-node) | |
| ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png)  | [Tutorial de Balão python - Capítulo 1.1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Inscreva-se com AAD  |  |
| ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png)  | [Tutorial de Balão python - Capítulo 1.2](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Inscreva-se com B2C                    |  |
| ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png)  | | [Tutorial de Balão python - Capítulo 2.1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Inscreva-se com AAD e Gráfico de Chamada |
| ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png)  | |[Tutorial de Balão python - Capítulo 3.1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Implementar para o Serviço de Aplicações Azure  |
| ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png)  | [Tutorial python Django - Capítulo 1.1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)   Inscreva-se com AAD  | |
| ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png)  | [Tutorial python Django - Capítulo 1.2](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Inscreva-se com B2C                    |  |
| ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png)  | | [Tutorial python Django - Capítulo 2.1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)  Inscreva-se com AAD e Gráfico de Chamada|
| ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png)  | | [Tutorial python Django - Capítulo 3.1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Implementar para o Serviço de Aplicações Azure                    |
| ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png)  | | [Aplicação Web Python Flask](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Esta imagem mostra o logótipo da Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |
| ![Esta imagem mostra o logótipo blazor](media/sample-v2-code/logo-blazor.png)</p>Blazor Server | [Tutorial de sinais de aplicações do Blazor Server](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC) | [A aplicação blazor Server chama o Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph)</p>Tutorial chapterwise: [Aplicativo Blazor Server para utilizadores de inscrição e chamada APIs com diretório ativo Azure](https://github.com/Azure-Samples/ms-identity-blazor-server) |

## <a name="desktop-and-mobile-public-client-apps"></a>Aplicativos de cliente público móvel e desktop

As seguintes amostras mostram aplicações de clientes públicos (aplicações de desktop ou mobile) que acedem à API do Microsoft Graph, ou à sua própria API web em nome de um utilizador. Para além do *Desktop (Consola) com* a amostra WAM, todas estas aplicações de cliente utilizam a Biblioteca de Autenticação da Microsoft (MSAL).

| Aplicação ao cliente | Plataforma | Fluxo/subvenção | Chama o Microsoft Graph | Chama uma API web ASP.NET Core |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Tutorial de ambiente de trabalho (.NET Core) - Utilização opcional:</p>- a cache simbólica da plataforma transversal</p>- uI web personalizado | ![Esta imagem mostra o logótipo .NET/C#](media/sample-v2-code/logo_NETcore.png) | [Código de autorização](msal-authentication-flows.md#authorization-code)| [ms-identidade-dotnet-desktop-tutorial](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| Desktop (WPF)      | ![Esta imagem mostra o logótipo .NET desktop/C#](media/sample-v2-code/logo_NET.png) | [Código de autorização](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-nativo-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Desktop (Consola)   | ![Imagem que mostra o logótipo .NET/C# (Desktop)](media/sample-v2-code/logo_NET.png) | [Autenticação Integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Desktop (Consola)   | ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png) | [Autenticação Integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identidade-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Desktop (Consola)   | ![Este é o logótipo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Nome de utilizador/senha](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Desktop (Consola) com WAM  | ![Este é o logótipo para .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Interativo com [Gestor de Conta Web](/windows/uwp/security/web-account-manager) (WAM) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Desktop (Consola)   | ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png) | [Nome de utilizador/senha](msal-authentication-flows.md#usernamepassword) |[ms-identidade-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Desktop (Consola)   | ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png) | [Nome de utilizador/senha](msal-authentication-flows.md#usernamepassword) |[ms-identidade-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Desktop (Eletrão)   | ![Esta imagem mostra o logótipo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (nó MSAL) | [Código de autorização (PKCE)](msal-authentication-flows.md#authorization-code) |[ms-identidade-javascript-nodejs-desktop](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) |  |
| Mobile (Android, iOS, UWP)   | ![Esta imagem mostra o logótipo .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Código de autorização](msal-authentication-flows.md#authorization-code) |[xamarin-nativo-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobile (iOS)       | ![Esta imagem mostra iOS/Objective-C ou Swift](media/sample-v2-code/logo_iOS.png) | [Código de autorização](msal-authentication-flows.md#authorization-code) |[ios-swift-objc-nativo-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-nativo-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [Código de autorização](msal-authentication-flows.md#authorization-code) |[macOS-swift-objc-nativo-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android-Java)   | ![Esta imagem mostra o logótipo do Android](media/sample-v2-code/logo_Android.png) | [Código de autorização](msal-authentication-flows.md#authorization-code) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobile (Android-Kotlin)   | ![Esta imagem mostra o logótipo do Android](media/sample-v2-code/logo_Android.png) | [Código de autorização](msal-authentication-flows.md#authorization-code) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Aplicações de daemon

As amostras que se seguem mostram uma aplicação que acede à API do Microsoft Graph com a sua própria identidade (sem utilizador).

| Aplicação ao cliente | Plataforma | Fluxo/Subvenção | Chama o Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Consola | ![Esta imagem mostra o logótipo .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Credenciais de Cliente](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Aplicação Web | ![Screenshot que mostra o logótipo ASP.NET.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Credenciais de Cliente](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Consola | ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png) | [Credenciais de Cliente](msal-authentication-flows.md#client-credentials) | [ms-identidade-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Consola | ![Esta imagem mostra o logótipo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (nó MSAL)| [Credenciais de Cliente](msal-authentication-flows.md#client-credentials) | [ms-identidade-javascript-nodejs-consola](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) |
| Consola | ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png) | [Credenciais de Cliente](msal-authentication-flows.md#client-credentials) | [ms-identidade-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Aplicações sem cabeça

A amostra que se segue mostra uma aplicação de cliente público a decorrer num dispositivo sem um navegador web. A aplicação pode ser uma ferramenta de linha de comando, uma aplicação em execução em Linux ou Mac, ou uma aplicação IoT. A amostra apresenta uma aplicação que acede à API do Microsoft Graph, em nome de um utilizador que assina insuem interactivemente noutro dispositivo (como um telemóvel). Esta aplicação de cliente utiliza a Biblioteca de Autenticação da Microsoft (MSAL).

| Aplicação ao cliente | Plataforma | Fluxo/Subvenção | Chama o Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Desktop (Consola)   | ![Esta imagem mostra o logótipo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Desktop (Consola)   | ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[ms-identidade-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Desktop (Consola)   | ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[ms-identidade-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>Aplicações SaaS multi-arrendatários

As seguintes amostras mostram como configurar o seu pedido de aceitação de inscrições de qualquer inquilino do Azure Ative Directory (Azure AD). Configurar a sua aplicação como *multi-inquilino* significa que pode oferecer um Software como aplicação **de Serviço** (SaaS) a muitas organizações, permitindo que os seus utilizadores possam iniciar sing-in à sua aplicação após o consentimento.

| Plataforma | Description | Ligação |
| -------- | --------------------- | -------- |
| ![Esta imagem mostra o logótipo angular ](media/sample-v2-code/logo_angular.png) [angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA multi-inquilino chama Graph API |[ms-identidade-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![Esta imagem mostra o logótipo angular ](media/sample-v2-code/logo_angular.png) [angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA multi-inquilino chama a API web personalizada multi-arrendatário |[ms-identidade-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![Esta imagem mostra o ASP.NET Core ](media/sample-v2-code/logo_NETcore.png) [logo.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET aplicação web Core MVC chama API de gráfico |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![Esta imagem mostra o ASP.NET Core ](media/sample-v2-code/logo_NETcore.png) [logo.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Aplicação web Core MVC chama ASP.NET Core Web API |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>APIs da Web

As seguintes amostras mostram como proteger uma API web com a plataforma de identidade da Microsoft, e como chamar uma API a jusante da API web.

| Plataforma | Sample |
| -------- | ------------------- |
| ![Esta imagem mostra o logotipo do Núcleo ASP.NET](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core web API (serviço) de [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Esta imagem mostra o logótipo ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Web API (serviço) de [ms-identidade-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Esta imagem mostra o logótipo java](media/sample-v2-code/logo_java.png) | API Web (serviço) de [ms-identidade-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Esta imagem mostra o logótipo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Web API (serviço) de [active-directório-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Esta imagem mostra o logótipo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| B2C Web API (serviço) de [active-directório-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Funciona Azure como APIs web

As seguintes amostras mostram como proteger uma Função Azure usando httpTrigger e expondo uma API web com a plataforma de identidade microsoft, e como chamar uma API a jusante da API web.

| Plataforma | Sample |
| -------- | ------------------- |
| ![Esta imagem mostra o logotipo do Núcleo ASP.NET](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core web API (serviço) Azure Function of [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Esta imagem mostra o logótipo python](media/sample-v2-code/logo_python.png)</p>Python | Web API (serviço) de [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Esta imagem mostra o logótipo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Web API (serviço) de [Node.js e passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Esta imagem mostra o logótipo Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Web API (serviço) de [Node.js e passaporte-azure-ad usando em nome de](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Outras amostras do Microsoft Graph

Para conhecer [amostras](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) e tutoriais que demonstrem diferentes padrões de utilização para a API do Microsoft Graph, incluindo a autenticação com AZure AD, consulte [amostras da Comunidade de Gráficos do Microsoft & tutoriais](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Ver também

[Microsoft Graph API conceptual e referência](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta&preserve-view=true)
