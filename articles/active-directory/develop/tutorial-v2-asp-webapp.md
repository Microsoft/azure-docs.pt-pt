---
title: Introdução ao servidor Web ASP.NET do Azure AD v 2.0
titleSuffix: Microsoft identity platform
description: Implementando a entrada da Microsoft em uma solução ASP.NET usando um aplicativo baseado em navegador da Web tradicional e o OpenID Connect Standard
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: b66d2a9a958afd536dcffeca211a3fc56cf09ef8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803715"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Adicionar entrada à Microsoft a um aplicativo Web ASP.NET

Este guia demonstra como implementar a entrada na Microsoft por meio de uma solução MVC do ASP.NET usando um aplicativo tradicional baseado em navegador da Web e o OpenID Connect.

Quando você tiver concluído este guia, seu aplicativo poderá aceitar entradas de contas pessoais de gosta de outlook.com e live.com. Além disso, as contas corporativas e de estudante de qualquer empresa ou organização integrada com Azure Active Directory (Azure AD) poderão entrar em seu aplicativo.

> Este guia requer o Microsoft Visual Studio 2019.  Não o tem?  [Baixe o Visual Studio 2019 gratuitamente](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Mostra como o aplicativo de exemplo gerado por este tutorial funciona](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

O aplicativo de exemplo que você cria é baseado em um cenário em que você usa o navegador para acessar um site do ASP.NET que solicita que um usuário autentique por meio de um botão de entrada. Neste cenário, a maior parte do trabalho para compor a página Web ocorre do lado do servidor.

## <a name="libraries"></a>Bibliotecas

Este guia usa as seguintes bibliotecas:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware que permite que uma aplicação utilize o OpenIdConnect para autenticação|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware que permite que um aplicativo mantenha uma sessão de usuário usando cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware que permite que aplicativos baseados em OWIN sejam executados em Serviços de Informações da Internet (IIS) usando o pipeline de solicitação do ASP.NET|

## <a name="set-up-your-project"></a>Configurar seu projeto

Esta seção descreve como instalar e configurar o pipeline de autenticação por meio do middleware OWIN em um projeto ASP.NET usando o OpenID Connect.

> Prefere baixar este projeto do Visual Studio de exemplo? [Baixe um projeto](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) e pule para [registrar seu aplicativo](#register-your-application) para configurar o exemplo de código antes de executá-lo.

### <a name="create-your-aspnet-project"></a>Criar seu projeto do ASP.NET

1. No Visual Studio: Vá para **arquivo** > **novo** **projeto**de > .
2. Em **Visual C#\Web**,selecione **Aplicação Web ASP.NET (.NET Framework)** .
3. Dê um nome à aplicação e selecione **OK**.
4. Selecione **vazio**e marque a caixa de seleção para adicionar referências do **MVC** .

## <a name="add-authentication-components"></a>Adicionar componentes de autenticação

1. No Visual Studio: Vá para **ferramentas** > **Gerenciador de pacotes NuGet** > **console do Gerenciador de pacotes**.
2. Adicione *Pacotes NuGet de middleware OWIN*, escrevendo o seguinte na janela da Consola do Gestor de Pacotes:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Sobre essas bibliotecas
> Essas bibliotecas habilitam o SSO (logon único) usando o OpenID Connect por meio da autenticação baseada em cookie. Depois de a autenticação estar concluída e o token que representa o utilizador ser enviado para a sua aplicação, o middleware OWIN cria um cookie de sessão. O navegador usa esse cookie em solicitações subsequentes para que o usuário não precise digitar a senha novamente e nenhuma verificação adicional é necessária.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configurar o pipeline de autenticação

As etapas a seguir são usadas para criar uma classe de inicialização de middleware OWIN para configurar a autenticação do OpenID Connect. Essa classe é executada automaticamente quando o processo do IIS é iniciado.

> [!TIP]
> Se o projeto não tiver um ficheiro `Startup.cs` na pasta raiz:
> 1. Clique com o botão direito do mouse na pasta raiz do projeto e, em seguida, selecione **adicionar** > **novo item** > **classe de inicialização OWIN**.<br/>
> 2. Nomeie-o como **Startup.cs**.
>
>> Verifique se a classe selecionada é uma classe de inicialização OWIN e não uma C# classe padrão. Confirme isso verificando se você vê [assembly: OwinStartup (typeof ({NameSpace}. Inicialização))] acima do namespace.

1. Adicione as referências *OWIN* e *Microsoft. IdentityModel* a Startup.cs:

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
> A configuração `ValidateIssuer = false` é uma simplificação para este guia de início rápido. Em aplicativos reais, você deve validar o emissor.
> Consulte os exemplos para saber como fazer isso.

<!--start-collapse-->
> ### <a name="more-information"></a>Mais informações
> Os parâmetros que fornecer em *OpenIDConnectAuthenticationOptions* servem de coordenadas para a aplicação comunicar com o Azure AD. Como o middleware OpenID Connect usa cookies em segundo plano, você também deve configurar a autenticação de cookie conforme mostrado no código anterior. O valor *ValidateIssuer* informa OpenIdConnect para não restringir o acesso a uma organização específica.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Adicionar um controlador para manipular solicitações de entrada e saída

Para criar um novo controlador para expor os métodos de entrada e saída, siga estas etapas:

1.  Clique com o botão direito do mouse na pasta **controladores** e selecione **Adicionar** > **controlador**.
2.  Selecione **Controlador MVC (versão .NET) – Vazio**.
3.  Selecione **Adicionar**.
4.  Nomeie-o **HomeController** e, em seguida, selecione **Adicionar**.
5.  Adicione referências de OWIN à classe:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Adicione os dois métodos a seguir para manipular a entrada e a saída para seu controlador iniciando um desafio de autenticação:

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

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Criar o home page do aplicativo para entrada do usuário

No Visual Studio, crie uma nova exibição para adicionar o botão de entrada e exibir as informações do usuário após a autenticação:

1.  Clique com o botão direito do rato na pasta **Vistas\Início** e selecione **Adicionar Vista**.
2.  Nomeie o novo **índice**de exibição.
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
> Esta página adiciona um botão de entrada no formato SVG com um plano de fundo preto:<br/>![Entrar com a conta da Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Para obter mais botões de entrada, vá para as [diretrizes de identidade visual](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "BDiretrizes de randing ").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Adicionar um controlador para exibir as declarações do usuário
Este controlador demonstra as utilizações do atributo `[Authorize]` para proteger um controlador. Esse atributo restringe o acesso ao controlador, permitindo somente usuários autenticados. O código a seguir usa o atributo para exibir as declarações do usuário que foram recuperadas como parte da entrada:

1.  Clique com o botão direito do mouse na pasta **controladores** e selecione **Adicionar** > **controlador**.
2.  Selecione **Controlador MVC {versão} – Vazio**.
3.  Selecione **Adicionar**.
4.  Dê-lhe o nome **ClaimsController**.
5.  Substitua o código da classe do controlador pelo código a seguir. Isso adiciona o atributo `[Authorize]` à classe:

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
> Devido ao uso do atributo `[Authorize]`, todos os métodos desse controlador poderão ser executados somente se o usuário for autenticado. Se o usuário não estiver autenticado e tentar acessar o controlador, o OWIN iniciará um desafio de autenticação e forçará o usuário a se autenticar. O código anterior examina a lista de declarações para atributos de usuário específicos incluídos no token de ID do usuário. Estes atributos incluem o nome completo do utilizador e o nome de utilizador, bem como o assunto do identificador de utilizador global. Também contém o *ID de inquilino*, que representa o ID da organização do utilizador. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Criar uma exibição para exibir as declarações do usuário

No Visual Studio, crie uma nova vista para apresentar as afirmações do utilizador numa página Web:

1.  Clique com o botão direito do mouse na pasta **Views\Claims** e selecione **Adicionar exibição**.
2.  Nomeie o novo **índice**de exibição.
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

Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à sua solução, você tem duas opções:

### <a name="option-1-express-mode"></a>Opção 1: modo expresso

Para registrar rapidamente seu aplicativo, siga estas etapas:

1. Vá para o novo painel de [registros de aplicativo de portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) .
1. Introduza um nome para a sua aplicação e xelecione **Registar**.
1. Siga as instruções para baixar e configurar automaticamente seu novo aplicativo em um único clique.

### <a name="option-2-advanced-mode"></a>Opção 2: modo avançado

Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:

1. Abra o Visual Studio e, em seguida:
   1. em Gerenciador de Soluções, selecione o projeto e exiba o janela Propriedades (se você não vir uma janela Propriedades, pressione F4).
   1. Altere o SSL habilitado para `True`.
   1. Clique com o botão direito do mouse no projeto no Visual Studio, selecione **Propriedades**e, em seguida, selecione a guia **Web** . Na seção **servidores** , altere a configuração de **URL do projeto** para a **URL SSL**.
   1. Copie a URL do SSL. Você adicionará essa URL à lista de URLs de redirecionamento na lista do portal de registro de URLs de redirecionamento na próxima etapa.<br/><br/>![Propriedades do projeto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou usando uma conta Microsoft pessoal.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina a sessão do portal para o locatário do Azure AD que você deseja.
1. Acesse a página da plataforma de identidade da Microsoft para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecione **novo registro**.
1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   1. Na seção **nome** , insira um nome de aplicativo significativo que será exibido aos usuários do aplicativo, como **ASPNET-tutorial**.
   1. Adicione a URL SSL que você copiou do Visual Studio na etapa 1 (por exemplo, `https://localhost:44368/`) em **URL de resposta**e selecione **registrar**.
1. Selecione o menu **autenticação** , selecione **tokens de ID** em **concessão implícita**e, em seguida, selecione **salvar**.
1. Adicione o seguinte no arquivo Web. config, localizado na pasta raiz na seção `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Substitua `ClientId` pela ID do aplicativo que você acabou de registrar.
1. Substitua `redirectUri` pela URL SSL do seu projeto.

## <a name="test-your-code"></a>Testar seu código

Para testar seu aplicativo no Visual Studio, pressione F5 para executar seu projeto. O navegador é aberto no local<span></span>http://localhost: {Port} e você vê o botão **entrar com a conta da Microsoft** . Selecione o botão para iniciar o processo de entrada.

Quando você estiver pronto para executar seu teste, use uma conta do Azure AD (conta corporativa ou de estudante) ou um conta Microsoft pessoal (<span>ao vivo.</span> com ou <span>Outlook.</span> com) para entrar.

![Entrar com a conta da Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![entrar no seu conta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Permissões e consentimento no ponto de extremidade da plataforma Microsoft Identity
>  Os aplicativos que se integram à plataforma Microsoft Identity seguem um modelo de autorização que oferece aos usuários e administradores o controle sobre como os dados podem ser acessados. Depois que um usuário se autentica com o Azure AD para acessar esse aplicativo, ele será solicitado a consentir as permissões solicitadas pelo aplicativo ("exibir seu perfil básico" e "manter o acesso aos dados aos quais você concedeu acesso"). Depois de aceitar essas permissões, o usuário continuará a usar os resultados do aplicativo. No entanto, o usuário pode ser solicitado com uma página de **consentimento de administrador necessária** se ocorrer uma das seguintes ações:
>  > - O desenvolvedor do aplicativo adiciona permissões adicionais que exigem o **consentimento do administrador**.
>  > - Ou o locatário está configurado (em **aplicativos empresariais – > configurações de usuário**), em que os usuários não podem consentir os aplicativos que acessam os dados da empresa em seu nome.
>
> Para obter mais informações, consulte [permissões e consentimento no ponto de extremidade da plataforma Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent).
<!--end-collapse-->

#### <a name="view-application-results"></a>Exibir resultados do aplicativo

Depois de entrar, o usuário é redirecionado para a home page do seu site. O home page é a URL HTTPS especificada nas informações de registro do aplicativo no portal de registro de aplicativos da Microsoft. O home page inclui uma mensagem de boas-vindas de *"olá \<usuário >"* , um link para sair e um link para exibir as declarações do usuário. O link para as declarações do usuário se conecta ao controlador de declarações que você criou anteriormente.

### <a name="view-the-users-claims"></a>Exibir as declarações do usuário

Para exibir as declarações do usuário, selecione o link para navegar até a exibição do controlador que está disponível somente para usuários autenticados.

#### <a name="view-the-claims-results"></a>Exibir os resultados de declarações

Depois de navegar até a exibição do controlador, você deverá ver uma tabela que contém as propriedades básicas para o usuário:

|Propriedade |Valor |Descrição |
|---|---|---|
|**Nome** |Nome completo do usuário | O nome próprio e apelido do utilizador
|**Nome de Utilizador** |<span>@domain.com</span> do usuário | O nome de usuário que é usado para identificar o usuário|
|**Assunto** |Requerente |Uma cadeia de caracteres que identifica exclusivamente o usuário na Web|
|**ID do locatário** |GUID | Um **GUID** que representa exclusivamente a organização do Azure AD do usuário|

Além disso, você deve ver uma tabela de todas as declarações que estão na solicitação de autenticação. Para obter mais informações, consulte a [lista de declarações que estão em um token de ID do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testar o acesso a um método que tem um atributo autorizar (opcional)

Para testar o acesso como um usuário anônimo a um controlador protegido pelo atributo `Authorize`, siga estas etapas:

1. Selecione o link para desconectar o usuário e concluir o processo de logout.
2. No navegador, digite http://<span></span>localhost: {Port}/Claims para acessar seu controlador protegido pelo atributo `Authorize`.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Resultados esperados após o acesso a um controlador protegido

Você será solicitado a autenticar para usar a exibição do controlador protegido.

## <a name="advanced-options"></a>opções avançadas

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Proteger todo o seu site
Para proteger todo o site, no arquivo **global. asax** , adicione o atributo `AuthorizeAttribute` ao filtro `GlobalFilters` no método `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Restringir quem pode entrar em seu aplicativo

Por padrão, quando você cria o aplicativo criado por este guia, seu aplicativo aceitará entradas de contas pessoais (incluindo outlook.com, live.com e outros), bem como contas corporativas e de estudante de qualquer empresa ou organização integrada ao Azure AD. Essa é uma opção recomendada para aplicativos SaaS.

Para restringir o acesso de entrada do usuário para seu aplicativo, várias opções estão disponíveis.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opção 1: restringir os usuários da instância de Active Directory de apenas uma organização para entrar em seu aplicativo (locatário único)

Essa opção é usada frequentemente para *aplicativos LOB*: se você quiser que seu aplicativo aceite entradas somente de contas que pertencem a uma instância específica do Azure AD (incluindo *contas de convidado* dessa instância), siga estas etapas:

1. No arquivo Web. config, altere o valor do parâmetro `Tenant` de `Common` para o nome do locatário da organização, como `contoso.onmicrosoft.com`.
2. Na [classe de inicialização OWIN](#configure-the-authentication-pipeline), defina o argumento `ValidateIssuer` como `true`.

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Opção 2: restringir o acesso a usuários em uma lista específica de organizações

Você pode restringir o acesso de entrada somente às contas de usuário que estão em uma organização do Azure AD que está na lista de organizações permitidas:
1. Na [classe de inicialização OWIN](#configure-the-authentication-pipeline), defina o argumento `ValidateIssuer` como `true`.
2. Defina o valor do parâmetro `ValidIssuers` como a lista de organizações permitidas.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opção 3: usar um método personalizado para validar os emissores

Você pode implementar um método personalizado para validar os emissores usando o parâmetro **IssuerValidator** . Para obter mais informações sobre como usar esse parâmetro, consulte [classe TokenValidationParameters](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como os aplicativos Web podem chamar APIs da Web.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>Saiba como criar o aplicativo usado neste guia de início rápido

Saiba mais sobre os aplicativos Web chamando APIs da Web com a plataforma de identidade da Microsoft:

> [!div class="nextstepaction"]
> [Aplicativos Web chamando APIs da Web](scenario-web-app-sign-user-overview.md)

Saiba como criar aplicativos Web chamando Microsoft Graph:

> [!div class="nextstepaction"]
> [Tutorial do Microsoft Graph ASP.NET](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa de duas perguntas:

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
