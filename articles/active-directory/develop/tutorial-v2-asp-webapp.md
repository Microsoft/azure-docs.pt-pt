---
title: Adicionar entrada ao aplicativo Web ASP.NET da plataforma de identidade da Microsoft
titleSuffix: Microsoft identity platform
description: Implementar o acesso à Microsoft numa solução ASP.NET utilizando uma aplicação tradicional baseada no navegador web e padrão OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 50eb88373b05d979d7f4b67b317e98c2a944459b
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701335"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Adicione o sessão à Microsoft a uma aplicação web ASP.NET

Este guia demonstra como implementar o início de sessão na Microsoft através de uma solução ASP.NET MVC utilizando uma aplicação tradicional baseada no navegador web e OpenID Connect.

Quando tiver concluído este guia, a sua candidatura poderá aceitar inscrições de contas pessoais de outlook.com e live.com. Além disso, as contas corporativas e de estudante de qualquer empresa ou organização integrada à plataforma Microsoft Identity poderão entrar em seu aplicativo.

> Este guia requer o Microsoft Visual Studio 2019.  Não o tem?  [Baixe o Visual Studio 2019 gratuitamente.](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Mostra como o aplicativo de exemplo gerado por este tutorial funciona](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

A aplicação de amostra seletiva baseia-se num cenário em que utiliza o navegador para aceder a um site ASP.NET que leva um utilizador a autenticar através de um botão de entrada. Neste cenário, a maior parte do trabalho para compor a página Web ocorre do lado do servidor.

## <a name="libraries"></a>Bibliotecas

Este guia utiliza as seguintes bibliotecas:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware que permite que uma aplicação utilize o OpenIdConnect para autenticação|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware que permite que uma aplicação mantenha uma sessão de utilizador usando cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware que permite que aplicações baseadas em OWIN funcionam em Serviços de Informação da Internet (IIS) utilizando o pipeline de pedido de ASP.NET|

## <a name="set-up-your-project"></a>Configurar seu projeto

Esta secção descreve como instalar e configurar o gasoduto de autenticação através do middleware OWIN num projeto ASP.NET utilizando o OpenID Connect.

> Prefere baixar o projeto do Estúdio Visual desta amostra? [Faça o download](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) de um projeto e salte para o Registo a [sua aplicação](#register-your-application) para configurar a amostra de código antes de ser executada.

### <a name="create-your-aspnet-project"></a>Crie o seu projeto ASP.NET

1. Em Visual Studio: Vá a **File** > **New** > **Project**.
2. Em **Visual C#\Web**,selecione **Aplicação Web ASP.NET (.NET Framework)** .
3. Dê um nome à aplicação e selecione **OK**.
4. Selecione **Empty**e, em seguida, selecione a caixa de verificação para adicionar referências **mVC.**

## <a name="add-authentication-components"></a>Adicionar componentes de autenticação

1. No Estúdio Visual: Vá a **Ferramentas** > **Nuget Package Manager** > **Consola de Gestor de Pacotes**.
2. Adicione *Pacotes NuGet de middleware OWIN*, escrevendo o seguinte na janela da Consola do Gestor de Pacotes:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Sobre estas bibliotecas
> Estas bibliotecas permitem um único sinal (SSO) utilizando o OpenID Connect através da autenticação baseada em cookies. Depois de a autenticação estar concluída e o token que representa o utilizador ser enviado para a sua aplicação, o middleware OWIN cria um cookie de sessão. O navegador utiliza este cookie em pedidos posteriores para que o utilizador não tenha de reescrever a palavra-passe, não sendo necessária qualquer verificação adicional.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configurar o gasoduto de autenticação

Os seguintes passos são usados para criar uma classe Startup de middleware OWIN para configurar a autenticação OpenID Connect. Esta aula é executada automaticamente quando o seu processo IIS começa.

> [!TIP]
> Se o projeto não tiver um ficheiro `Startup.cs` na pasta raiz:
> 1. Clique na pasta raiz do projeto e, em seguida, selecione **Adicionar** > **Novo Item** > **classe OWIN Startup**.<br/>
> 2. **Diga-lhe Startup.cs.**
>
>> Certifique-se de que a classe selecionada é C# uma classe OWIN Startup e não uma classe padrão. Confirme-o verificando se vê [montagem: OwinStartup(tipo de({NameSpace}. Arranque)] acima do espaço de nome.

1. Adicione referências *OWIN* e *Microsoft.IdentityModel* a Startup.cs:

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

2. Substitua a classe de Arranque pelo código seguinte:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Microsoft identity platform endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
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
                        ValidateIssuer = false // This is a simplification
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

> [!NOTE]
> A configuração `ValidateIssuer = false` é uma simplificação para este guia de início rápido. Em aplicações reais, deve validar o emitente.
> Veja as amostras para aprender a fazer isso.

<!--start-collapse-->
> ### <a name="more-information"></a>Mais informações
> Os parâmetros que você fornece no *OpenIDConnectAuthenticationOptions* servem como coordenadas para o aplicativo se comunicar com a plataforma de identidade da Microsoft. Uma vez que o middleware OpenID Connect utiliza cookies em segundo plano, também deve configurar a autenticação de cookies como o código anterior mostra. O valor ValidaDor de *Problemas* diz ao OpenIdConnect para não restringir o acesso a uma organização específica.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Adicione um controlador para lidar com pedidos de sessão e de inscrição

Para criar um novo controlador para expor métodos de inscrição e de inscrição, siga estes passos:

1.  Clique na pasta **controladora** e selecione **Adicionar** > **Controlador**.
2.  Selecione **Controlador MVC (versão .NET) – Vazio**.
3.  Selecione **Adicionar**.
4.  Nomeie-o **HomeController** e, em seguida, **selecione Adicionar**.
5.  Adicione referências OWIN à classe:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Adicione os dois seguintes métodos para lidar com o início do sessão e a inscrição no seu controlador, dando início a um desafio de autenticação:

    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Crie a página inicial da aplicação para o início de sessão do utilizador

No Estúdio Visual, crie uma nova vista para adicionar o botão de iniciar sessão e para mostrar informações do utilizador após a autenticação:

1.  Clique com o botão direito do rato na pasta **Vistas\Início** e selecione **Adicionar Vista**.
2.  Nomeie a nova vista **Index**.
3.  Adicione o seguinte HTML, que inclui o botão de início de sessão, ao ficheiro:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Mais informações
> Esta página adiciona um botão de início de sessão no formato SVG com um fundo preto:<br/>![Inscreva-se na Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Para obter mais botões de sessão, vá às [diretrizes](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Diretrizes de imagem corporativa")de Branding .
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Adicione um controlador para exibir as reclamações do utilizador
Este controlador demonstra as utilizações do atributo `[Authorize]` para proteger um controlador. Este atributo restringe o acesso ao controlador, permitindo apenas utilizadores autenticados. O seguinte código utiliza o atributo para apresentar as alegações do utilizador que foram recuperadas como parte do início de sessão:

1.  Clique na pasta **controladora** e, em seguida, selecione **Adicionar** > **Controlador**.
2.  Selecione **Controlador MVC {versão} – Vazio**.
3.  Selecione **Adicionar**.
4.  Dê-lhe o nome **ClaimsController**.
5.  Substitua o código da sua classe controladora pelo seguinte código. Isto adiciona o atributo `[Authorize]` à classe:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Mais informações
> Devido à utilização do atributo `[Authorize]`, todos os métodos deste controlador só podem ser executados se o utilizador for autenticado. Se o utilizador não for autenticado e tentar aceder ao controlador, o OWIN inicia um desafio de autenticação e obriga o utilizador a autenticar. O código anterior analisa a lista de reclamações para atributos específicos do utilizador incluídos no token id do utilizador. Estes atributos incluem o nome completo do utilizador e o nome de utilizador, bem como o assunto do identificador de utilizador global. Também contém o *ID de inquilino*, que representa o ID da organização do utilizador. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Criar uma vista para exibir as reclamações do utilizador

No Visual Studio, crie uma nova vista para apresentar as afirmações do utilizador numa página Web:

1.  Clique à direita na pasta **Views\Claims** e, em seguida, **selecione Adicionar Ver**.
2.  Nomeie a nova vista **Index**.
3.  Adicione o seguinte HTML ao ficheiro:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>Registar a sua aplicação

Para registar a sua candidatura e adicionar as informações de registo de inscrição à sua solução, tem duas opções:

### <a name="option-1-express-mode"></a>Opção 1: Modo expresso

Para registar rapidamente a sua candidatura, siga estes passos:

1. Vá para o novo painel de [registros de aplicativo de portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) .
1. Introduza um nome para a sua aplicação e xelecione **Registar**.
1. Siga as instruções para descarregar e configure automaticamente a sua nova aplicação num único clique.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo avançado

Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:

1. Open Visual Studio, e depois:
   1. no Solution Explorer, selecione o projeto e veja a janela Propriedades (se não vir uma janela Propriedades, prima F4).
   1. Alterar o SSL habilitado a `True`.
   1. Clique no projeto no Estúdio Visual, selecione **Propriedades**e, em seguida, selecione o separador **Web.** Na secção **Servidores,** altere a definição de Url do **Projeto** para o **URL SSL**.
   1. Copie o URL SSL. Você adicionará este URL à lista de URLs Redirecionamento na lista de URLs redirecionados do portal de registo no próximo passo.<br/><br/>![Propriedades do projeto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou usando uma conta Microsoft pessoal.
1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a sua conta no canto superior direito e marque a sua sessão de portal para o inquilino da AD Azure que deseja.
1. Acesse a página da plataforma de identidade da Microsoft para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecione **novo registro**.
1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   1. Na secção **Nome,** introduza um nome de aplicação significativo que será exibido aos utilizadores da app, como **aspnet-tutorial**.
   1. Adicione o URL SSL copiado do Visual Studio no passo 1 (por exemplo, `https://localhost:44368/`) no **URL de resposta,** e selecione **Register**.
1. Selecione o menu **de Autenticação,** selecione **fichas de ID** sob A **subvenção implícita,** e, em seguida, selecione **Guardar**.
1. Adicione o seguinte no ficheiro web.config, localizado na pasta raiz na secção `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Substitua `ClientId` com o ID de inscrição que acabou de registar.
1. Substitua `redirectUri` pelo URL SSL do seu projeto.

## <a name="test-your-code"></a>Testar seu código

Para testar a sua aplicação no Estúdio Visual, prima F5 para executar o seu projeto. O navegador abre-se<span></span>para a localização local http://:{port} e vê o **'Sign in' com** o botão Microsoft. Selecione o botão para iniciar o processo de início.

Quando estiver pronto para executar o seu teste, utilize uma conta Azure AD (conta de trabalho ou escola) ou uma conta pessoal da Microsoft<span>(ao vivo).</span> com ou <span>perspetiva.</span> com) para iniciar sessão.

![Inscreva-se na Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Iniciar sessão na sua conta da Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Permissões e consentimento no ponto de extremidade da plataforma Microsoft Identity
>  Os aplicativos que se integram à plataforma Microsoft Identity seguem um modelo de autorização que oferece aos usuários e administradores o controle sobre como os dados podem ser acessados. Depois que um usuário é autenticado com a plataforma de identidade da Microsoft para acessar esse aplicativo, ele será solicitado a consentir as permissões solicitadas pelo aplicativo ("exibir seu perfil básico" e "manter o acesso aos dados aos quais você concedeu acesso"). Depois de aceitar estas permissões, o utilizador continuará com os resultados da aplicação. No entanto, o utilizador pode ser solicitado com uma página de **consentimento da Administração Need** se algum dos seguintes ocorrer:
>  > - O desenvolvedor da aplicação adiciona quaisquer permissões adicionais que requeiram **o consentimento do Administrador.**
>  > - Ou o inquilino está configurado (em **Aplicações Empresariais -> Definições**de Utilizador) onde os utilizadores não podem consentir em aplicações que acedam aos dados da empresa em seu nome.
>
> Para mais informações, consulte [permissões e consentimento no ponto final da plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent).
<!--end-collapse-->

#### <a name="view-application-results"></a>Exibir resultados do aplicativo

Depois de iniciar sessão, o utilizador é redirecionado para a página inicial do seu website. A página inicial é o URL HTTPS especificado na informação de registo de aplicação no Portal de Registo de Aplicações da Microsoft. A página inicial inclui uma mensagem de boas-vindas *"Hello \<user>",* um link para assinar e um link para ver as reclamações do utilizador. O link para as reclamações do utilizador liga-se ao controlador Reivindicações que criou anteriormente.

### <a name="view-the-users-claims"></a>Ver as reclamações do utilizador

Para visualizar as afirmações do utilizador, selecione o link para navegar na vista do controlador que está disponível apenas para utilizadores autenticados.

#### <a name="view-the-claims-results"></a>Ver os resultados das reclamações

Depois de navegar para a vista do controlador, deve ver uma tabela que contenha as propriedades básicas para o utilizador:

|Propriedade |Valor |Descrição |
|---|---|---|
|**Nome** |Nome completo do utilizador | O nome próprio e apelido do utilizador
|**Nome de Utilizador** |<span>@domain.comde</span> utilizadores | O nome de utilizador que é usado para identificar o utilizador|
|**Assunto** |Assunto |Uma cadeia que identifica exclusivamente o utilizador através da web|
|**ID do locatário** |GUID | Um **guia** que representa exclusivamente a organização azure ad do utilizador|

Além disso, deve consultar uma tabela de todas as reclamações que estão no pedido de autenticação. Para obter mais informações, consulte a [lista de declarações que estão em um token de ID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Teste de acesso a um método que tem um atributo Autorizado (opcional)

Para testar o acesso como utilizador anónimo a um controlador protegido pelo atributo `Authorize`, siga estes passos:

1. Selecione o link para assinar o utilizador e complete o processo de inscrição.
2. No seu navegador,<span></span>escreva http:// localhost:{port}/claims para aceder ao seu controlador protegido pelo atributo `Authorize`.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Resultados esperados após o acesso a um controlador protegido

É-lhe pedido que autenticasse para usar a visão do controlador protegido.

## <a name="advanced-options"></a>Opções avançadas

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Proteja todo o seu site
Para proteger todo o seu website, no ficheiro **Global.asax,** adicione o atributo `AuthorizeAttribute` ao filtro `GlobalFilters` no método `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Restrinja quem pode iniciar sessão na sua candidatura

Por padrão, quando você cria o aplicativo criado por este guia, seu aplicativo aceitará entradas de contas pessoais (incluindo outlook.com, live.com e outros), bem como contas corporativas e de estudante de qualquer empresa ou organização integrada ao Plataforma de identidade da Microsoft. Esta é uma opção recomendada para aplicações SaaS.

Para restringir o acesso ao utilizador para a sua aplicação, existem várias opções disponíveis.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opção 1: Restringir os utilizadores de apenas uma instância de Diretório Ativo de uma organização para iniciar sessão na sua aplicação (inquilino único)

Esta opção é frequentemente utilizada para *aplicações LOB*: Se pretender que a sua candidatura aceite inscrições apenas a partir de contas que pertençam a uma instância específica do Azure AD (incluindo *contas de hóspedes* desse caso), siga estes passos:

1. No ficheiro web.config, altere o valor do parâmetro `Tenant` de `Common` para o nome de inquilino da organização, como `contoso.onmicrosoft.com`.
2. Na sua aula de [Startup OWIN,](#configure-the-authentication-pipeline)detete o argumento `ValidateIssuer` para `true`.

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Opção 2: Restringir o acesso aos utilizadores numa lista específica de organizações

Pode restringir o acesso ao acesso a apenas às contas de utilizador que se encontram numa organização da AD Azure que está na lista de organizações permitidas:
1. Na sua aula de [Startup OWIN,](#configure-the-authentication-pipeline)detete o argumento `ValidateIssuer` para `true`.
2. Defina o valor do parâmetro `ValidIssuers` na lista de organizações permitidas.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opção 3: Utilize um método personalizado para validar os emitentes

Você pode implementar um método personalizado para validar os emissores usando o parâmetro **IssuerValidator** . Para obter mais informações sobre como utilizar este parâmetro, consulte a [classe TokenValidaParameters](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Passos seguintes

Saiba como as aplicações web podem ligar para a web APIs.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>Saiba como criar a aplicação utilizada neste guia de arranque rápido

Saiba mais sobre aplicações Web ligando para a web APIs com a plataforma de identidade da Microsoft:

> [!div class="nextstepaction"]
> [Aplicativos web chamando APIs web](scenario-web-app-sign-user-overview.md)

Saiba como construir aplicações Web chamando Microsoft Graph:

> [!div class="nextstepaction"]
> [Tutorial de ASP.NET do Microsoft Graph](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que pensa ao concluir um inquérito de duas perguntas:

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
