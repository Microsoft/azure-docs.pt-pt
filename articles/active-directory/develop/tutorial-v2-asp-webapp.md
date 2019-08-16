---
title: Introdução ao servidor Web ASP.NET do Azure AD v 2.0 | Microsoft Docs
description: Implementando a entrada da Microsoft em uma solução ASP.NET com um aplicativo tradicional baseado em navegador da Web usando o OpenID Connect Standard
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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: fce6ab2c5068d1628860356a9df0dd33c73948b3
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511969"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Adicionar entrada com a Microsoft a um aplicativo Web ASP.NET

Este guia demonstra como implementar a entrada com a Microsoft usando uma solução MVC ASP.NET com um aplicativo tradicional baseado em navegador da Web usando o OpenID Connect.

No final deste guia, seu aplicativo será capaz de aceitar entradas de contas pessoais, por exemplo, outlook.com, live.com e outros. Essas contas também incluem contas corporativas e de estudante de qualquer empresa ou organização integrada ao Azure Active Directory.

> Este guia requer o Visual Studio 2019.  Não o tem?  [Baixe o Visual Studio 2019 gratuitamente](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Mostra como o aplicativo de exemplo gerado por este tutorial funciona](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

O aplicativo de exemplo que você cria é baseado no cenário em que você usa o navegador para acessar um site ASP.NET que solicita que um usuário autentique por meio de um botão de entrada. Neste cenário, a maior parte do trabalho para compor a página Web ocorre do lado do servidor.

## <a name="libraries"></a>Bibliotecas

Este guia usa as seguintes bibliotecas:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware que permite que uma aplicação utilize o OpenIdConnect para autenticação|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware que permite que uma aplicação mantenha a sessão de utilizador com cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Permite que as aplicações com base em OWIN sejam executados nos IIS através do pipeline de pedidos do ASP.NET|

## <a name="set-up-your-project"></a>Configurar seu projeto

Esta seção mostra as etapas para instalar e configurar o pipeline de autenticação por meio do middleware OWIN em um projeto ASP.NET usando o OpenID Connect.

> Prefere baixar este projeto do Visual Studio de exemplo? [Baixe um projeto](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) e pule para a [etapa de configuração](#register-your-application) para configurar o exemplo de código antes de executar.

### <a name="create-your-aspnet-project"></a>Criar seu projeto do ASP.NET

1. No Visual Studio:`File` > `New` > `Project`
2. Em *Visual C#\Servidor*, selecione `ASP.NET Web Application (.NET Framework)`.
3. Nomeie seu aplicativo e clique em *OK*
4. Marque `Empty` e marque a caixa de seleção `MVC` para adicionar referências

## <a name="add-authentication-components"></a>Adicionar componentes de autenticação

1. No Visual Studio:`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Adicione *Pacotes NuGet de middleware OWIN*, escrevendo o seguinte na janela da Consola do Gestor de Pacotes:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Sobre essas bibliotecas
> As bibliotecas acima ativam o início de sessão único (SSO) com o OpenID Connect através da autenticação baseada em cookies. Depois de a autenticação estar concluída e o token que representa o utilizador ser enviado para a sua aplicação, o middleware OWIN cria um cookie de sessão. O navegador usa esse cookie em solicitações subsequentes para que o usuário não precise redigitar a senha e nenhuma verificação adicional é necessária.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configurar o pipeline de autenticação

As etapas a seguir são usadas para criar uma classe de inicialização de middleware OWIN para configurar a autenticação do OpenID Connect. Essa classe será executada automaticamente quando o processo do IIS for iniciado.

> [!TIP]
> Se o projeto não tiver um ficheiro `Startup.cs` na pasta raiz:
> 1. Clique com o botão direito do mouse na pasta raiz do projeto: >`Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Dê-lhe o nome `Startup.cs`
>
>> Certifique-se de que a classe selecionada é uma Classe de Arranque do OWIN e não uma classe C# padrão. Para confirmar, verifique se vê `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` por cima do espaço de nomes.

1. Adicione as referências *OWIN* e *Microsoft. IdentityModel* a `Startup.cs`:

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

2. Substitua a classe de inicialização pelo código abaixo:

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
> A `ValidateIssuer = false` configuração é uma simplificação para este guia de início rápido. Em aplicativos reais você precisa validar o emissor, consulte os exemplos para entender como fazer isso.

<!--start-collapse-->
> ### <a name="more-information"></a>Mais Informações
> Os parâmetros que fornecer em *OpenIDConnectAuthenticationOptions* servem de coordenadas para a aplicação comunicar com o Azure AD. Como o middleware OpenID Connect usa cookies em segundo plano, você também precisa configurar a autenticação de cookie conforme mostrado no código acima. O valor *ValidateIssuer* diz ao OpenIdConnect para não restringir o acesso a uma organização específica.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Adicionar um controlador para manipular solicitações de entrada e saída

Esta etapa mostra como criar um novo controlador para expor os métodos de entrada e saída.

1.  Clique com o `Controllers` botão direito do mouse na pasta e selecione`Add` > `Controller`
2.  Selecione `MVC (.NET version) Controller – Empty`.
3.  Clique em *Adicionar*
4.  Nomeie- `HomeController` o e clique em *Adicionar*
5.  Adicione referências de *OWIN* à classe:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. Adicione os dois métodos abaixo para manipular a entrada e a saída para seu controlador iniciando um desafio de autenticação por meio de código:
    
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

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Criar o home page do aplicativo para conectar usuários por meio de um botão de entrada

No Visual Studio, crie uma nova vista para adicionar o botão de início de sessão e apresentar informações do utilizador após a autenticação:

1.  Clique com o `Views\Home` botão direito do mouse na pasta e selecione`Add View`
2.  Dê-lhe o nome `Index`.
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
> ### <a name="more-information"></a>Mais Informações
> Esta página adiciona um botão de entrada no formato SVG com um plano de fundo preto:<br/>![Entrar com a conta da Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Para obter mais botões de entrada, acesse as(https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "diretrizes de identidade visual") [desta página].
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Adicionar um controlador para exibir as declarações do usuário
Este controlador demonstra as utilizações do atributo `[Authorize]` para proteger um controlador. Este atributo restringe o acesso ao controlador, permitindo apenas utilizadores autenticados. O código a seguir usa o atributo para exibir as declarações do usuário que foram recuperadas como parte da entrada.

1.  Clique com o `Controllers` botão direito do mouse na pasta:`Add` > `Controller`
2.  Selecione `MVC {version} Controller – Empty`.
3.  Clique em *Adicionar*
4.  Dê-lhe o nome `ClaimsController`
5.  Substitua o código da classe do controlador pelo código abaixo-isso adiciona o `[Authorize]` atributo à classe:

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
> ### <a name="more-information"></a>Mais Informações
> Devido à utilização do atributo `[Authorize]`, todos os métodos deste controlador só podem ser executados se o utilizador estiver autenticado. Se o usuário não estiver autenticado e tentar acessar o controlador, o OWIN iniciará um desafio de autenticação e forçará o usuário a se autenticar. O código acima examina a lista de declarações para atributos de usuário específicos incluídos no token de ID do usuário. Estes atributos incluem o nome completo do utilizador e o nome de utilizador, bem como o assunto do identificador de utilizador global. Também contém o *ID de inquilino*, que representa o ID da organização do utilizador. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Criar uma exibição para exibir as declarações do usuário

No Visual Studio, crie uma nova vista para apresentar as afirmações do utilizador numa página Web:

1.  Clique com o `Views\Claims` botão direito do mouse na pasta e:`Add View`
2.  Dê-lhe o nome `Index`.
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

### <a name="option-1-express-mode"></a>Opção 1: Modo expresso

Você pode registrar rapidamente seu aplicativo fazendo o seguinte:

1. Vá para o novo painel de [registros de aplicativo de portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) .
1. Introduza um nome para a sua aplicação e clique em **Registar**.
1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo avançado

Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:

1. Vá para o Visual Studio e:
   1. em Gerenciador de Soluções, selecione o projeto e examine a janela Propriedades (se você não vir uma janela Propriedades, pressione F4).
   1. Altere o SSL habilitado `True`para.
   1. Clique com o botão direito do mouse no projeto no Visual Studio, escolha **Propriedades**e a guia **Web** . Na seção *servidores* , altere a *URL do projeto* para a URL do SSL.
   1. Copie a URL do SSL. Você adicionará essa URL à lista de URLs de redirecionamento na lista do portal de registro de URLs de redirecionamento na próxima etapa:<br/><br/>![Propriedades do projeto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecione **novo registro**.
1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   1. Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `ASPNET-Tutorial`.
   1. Adicione a URL SSL que você copiou do Visual Studio na etapa 1 (por exemplo `https://localhost:44368/`) na **URL de resposta**e clique em **registrar**.
1. Selecione o menu **Autenticação**, defina os **tokens de ID** em **Concessão Implícita** e, em seguida, selecione **Guardar**.
1. Adicione o seguinte no `web.config` localizado na pasta raiz na seção: `configuration\appSettings`

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Substitua `ClientId` pela ID do aplicativo que você acabou de registrar.
1. Substitua `redirectUri` pela URL SSL do seu projeto.

## <a name="test-your-code"></a>Testar seu código

Para testar seu aplicativo no Visual Studio, pressione **F5** para executar seu projeto. O navegador é aberto no local<span></span>http://localhost: {Port} e você vê o botão **entrar com a conta da Microsoft** . Selecione o botão para iniciar o processo de entrada.

Quando você estiver pronto para executar seu teste, use uma conta do Microsoft Azure Active Directory (AD do Azure) (conta corporativa ou de estudante) ou uma conta Microsoft pessoal (<span>ao vivo.</span> com ou <span>Outlook.</span> com) para entrar.

![Entrar com a conta da Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Entre no seu conta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Exibir resultados do aplicativo

Depois de entrar, o usuário é redirecionado para a home page do seu site. O home page é a URL HTTPS especificada nas informações de registro do aplicativo no portal de registro de aplicativos da Microsoft. A Home Page inclui uma mensagem de boas-vindas *"Olá \<, > de usuário",* um link para sair e um link para exibir as declarações do usuário. O link para as declarações do usuário navega até o controlador de *declarações* que você criou anteriormente.

### <a name="browse-to-see-the-users-claims"></a>Procurar para ver as declarações do usuário

Para ver as declarações do usuário, selecione o link para navegar até a exibição do controlador que está disponível somente para usuários autenticados.

#### <a name="view-the-claims-results"></a>Exibir os resultados de declarações

Depois de navegar até a exibição do controlador, você deverá ver uma tabela que contém as propriedades básicas para o usuário:

|Propriedade |Value |Descrição |
|---|---|---|
|**Name** |Nome completo do usuário | O nome e sobrenome do usuário.
|**Nome de Utilizador** |usuário<span>@domain.com</span> | O nome de usuário que é usado para identificar o usuário.
|**Subject** |Subject |Uma cadeia de caracteres que identifica exclusivamente o usuário na Web.|
|**ID do locatário** |Guid | Um **GUID** que representa exclusivamente a organização do Azure AD do usuário.|

Além disso, você deve ver uma tabela de todas as declarações que estão na solicitação de autenticação. Para obter mais informações, consulte a [lista de declarações que estão em um token de ID do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testar o acesso a um método que tem um atributo autorizar (opcional)

Para testar o acesso como um usuário anônimo a um controlador protegido com `Authorize` o atributo, siga estas etapas:

1. Selecione o link para desconectar o usuário e concluir o processo de saída.
2. No navegador, digite http://<span></span>localhost: {porta}/declarações para acessar o controlador que está protegido com o `Authorize` atributo.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Resultados esperados após o acesso a um controlador protegido

Você será solicitado a autenticar para usar a exibição do controlador protegido.

## <a name="advanced-options"></a>Opções avançadas

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Proteger todo o seu site
Para proteger todo o site, no arquivo **global. asax** , adicione o `AuthorizeAttribute` atributo ao `GlobalFilters` filtro no `Application_Start` método:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Restringir quem pode entrar em seu aplicativo

Por padrão, quando você cria o aplicativo criado por este guia, seu aplicativo aceitará entradas de contas pessoais (incluindo outlook.com, live.com e outros), bem como contas corporativas e de estudante de qualquer empresa ou organização integrada ao Azure Active Directory. Essa é uma opção recomendada para aplicativos SaaS.

Para restringir o acesso de entrada do usuário para seu aplicativo, várias opções estão disponíveis:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opção 1: Restringir os utilizadores da instância do Active Directory de apenas uma organização para iniciar sessão na aplicação (inquilino único)

Essa opção é um cenário comum para *aplicativos LOB*: Se você quiser que seu aplicativo aceite entradas somente de contas que pertencem a uma instância específica de Azure Active Directory (incluindo *contas de convidado* dessa instância), faça o seguinte:

1. No arquivo **Web. config** , altere o valor `Tenant` do parâmetro de `Common` para o `contoso.onmicrosoft.com`nome do locatário da organização, como.
2. Na [classe de inicialização OWIN](#configure-the-authentication-pipeline), defina o `ValidateIssuer` argumento como `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Opção 2: Restringir o acesso ao seu aplicativo aos usuários em uma lista específica de organizações

Você pode restringir o acesso de entrada a apenas contas de usuário que estejam em uma organização do Azure AD que esteja na lista de organizações permitidas:
1. Na [classe de inicialização OWIN](#configure-the-authentication-pipeline), defina o `ValidateIssuer` argumento como `true`.
2. Defina o valor do `ValidIssuers` parâmetro para a lista de organizações permitidas.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opção 3: Usar um método personalizado para validar os emissores

Você pode implementar um método personalizado para validar os emissores usando o parâmetro **IssuerValidator** . Para obter mais informações sobre como usar esse parâmetro, leia sobre a [classe TokenValidationParameters](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os aplicativos Web chamando APIs da Web:.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Conheça os passos para criar a aplicação utilizada neste início rápido

> [!div class="nextstepaction"]
> [Aplicativos Web chamando APIs da Web]( https://aka.ms/msal-net-authorization-code)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)