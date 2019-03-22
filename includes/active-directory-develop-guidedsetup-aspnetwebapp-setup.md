---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 701b08cafd2e6d38a68252ea5919c8f4cd7de9b4
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203657"
---
## <a name="set-up-your-project"></a>Configurar seu projeto

Esta secção mostra os passos para instalar e configurar o pipeline de autenticação por meio de middleware da OWIN num projeto do ASP.NET através do OpenID Connect.

> Prefere transferir o projeto do Visual Studio este exemplo em vez disso? [Transfira um projeto](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) e avance para o [passo de configuração](#register-your-application) para configurar o exemplo de código antes de executar.

### <a name="create-your-aspnet-project"></a>Criar o seu projeto ASP.NET

1. No Visual Studio: `File` > `New` > `Project`
2. Sob *Visual C# \Web*, selecione `ASP.NET Web Application (.NET Framework)`.
3. Nome da sua aplicação e clique em *OK*
4. Selecione `Empty` e selecione a caixa de verificação para adicionar `MVC` referências

## <a name="add-authentication-components"></a>Adicionar componentes de autenticação

1. No Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Adicione *Pacotes NuGet de middleware OWIN*, escrevendo o seguinte na janela da Consola do Gestor de Pacotes:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Sobre essas bibliotecas
> As bibliotecas acima ativam o início de sessão único (SSO) com o OpenID Connect através da autenticação baseada em cookies. Depois de a autenticação estar concluída e o token que representa o utilizador ser enviado para a sua aplicação, o middleware OWIN cria um cookie de sessão. O browser, em seguida, utiliza esse cookie em solicitações subsequentes para que o usuário não precise escrever novamente a palavra-passe, e nenhuma verificação adicional é necessária.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configurar o pipeline de autenticação
Os passos abaixo são utilizados para criar uma classe de inicialização para configurar a autenticação OpenID Connect middleware da OWIN. Essa classe será executada automaticamente quando o processo do IIS é iniciado.

> [!TIP]
> Se o projeto não tiver um ficheiro `Startup.cs` na pasta raiz:
> 1. Com o botão direito na pasta raiz do projeto: > `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Dê-lhe o nome `Startup.cs`
>
>> Certifique-se de que a classe selecionada é uma Classe de Arranque do OWIN e não uma classe C# padrão. Para confirmar, verifique se vê `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` por cima do espaço de nomes.

1. Adicione *OWIN* e *IdentityModel* referencia a `Startup.cs`:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Substitua a classe de inicialização com o código abaixo:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Mais Informações
> Os parâmetros que fornecer em *OpenIDConnectAuthenticationOptions* servem de coordenadas para a aplicação comunicar com o Azure AD. Uma vez que o middleware de OpenID Connect utiliza cookies em segundo plano, terá também de configurar a autenticação de cookie, como o código acima mostra. O valor *ValidateIssuer* diz ao OpenIdConnect para não restringir o acesso a uma organização específica.
<!--end-collapse-->
