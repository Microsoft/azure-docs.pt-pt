---
title: 'Tutorial: Criar uma aplicação web ASP.NET que utiliza a plataforma de identidade da Microsoft para autenticação | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você constrói uma aplicação web ASP.NET que utiliza a plataforma de identidade microsoft e o middleware OWIN para permitir o login do utilizador.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 38def2b5af3a5f0f9a32c2b681bd0ee95ca44086
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174687"
---
# <a name="tutorial-add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Tutorial: Adicione o sôs-in à Microsoft a uma aplicação web ASP.NET

Neste tutorial, você constrói uma aplicação web MVC ASP.NET que assina nos utilizadores utilizando o middleware Open Web Interface para .NET (OWIN) e a plataforma de identidade microsoft.

Quando tiver concluído este guia, a sua candidatura poderá aceitar inscrições de contas pessoais de outlook.com e live.com. Além disso, contas de trabalho e escola de qualquer empresa ou organização que esteja integrada com a plataforma de identidade da Microsoft poderão iniciar scontabilidade na sua app.

Neste tutorial:

> [!div class="checklist"]
> * Criar um projeto *de aplicação web ASP.NET* no Estúdio Visual
> * Adicione a Interface Web Aberta para componentes de middleware .NET (OWIN)
> * Adicione código para suportar a inscrição do utilizador e a s inscrição
> * Registe a app no portal Azure
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) com a ASP.NET e a carga de trabalho de **desenvolvimento web** instalada

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de amostras gerada por este guia

![Mostra como funciona a app de amostras gerada por este tutorial](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

A aplicação de amostra que cria baseia-se num cenário em que utiliza o navegador para aceder a um website ASP.NET que pede a um utilizador que autente a autenticar através de um botão de inscrição. Neste cenário, a maior parte do trabalho para compor a página Web ocorre do lado do servidor.

## <a name="libraries"></a>Bibliotecas

Este guia utiliza as seguintes bibliotecas:

|Biblioteca|Description|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware que permite que uma aplicação utilize o OpenIdConnect para autenticação|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware que permite uma aplicação para manter uma sessão de utilizador usando cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware que permite que aplicações baseadas no OWIN executem em Serviços de Informação da Internet (IIS) utilizando o pipeline ASP.NET request|

## <a name="set-up-your-project"></a>Configurar o seu projeto

Esta secção descreve como instalar e configurar o gasoduto de autenticação através do middleware OWIN num projeto ASP.NET utilizando o OpenID Connect.

> Prefere descarregar o projeto visual studio desta amostra? [Faça o download de um projeto](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) e salte para o Registo da sua [aplicação](#register-your-application) para configurar a amostra de código antes de executar.

### <a name="create-your-aspnet-project"></a>Crie o seu projeto de ASP.NET

1. In Visual Studio: Go to **File**  >  **New**  >  **Project**.
2. Em **Visual C#\Web**,selecione **Aplicação Web ASP.NET (.NET Framework)**.
3. Dê um nome à aplicação e selecione **OK**.
4. Selecione **Empty** e, em seguida, selecione a caixa de verificação para adicionar referências **MVC.**

## <a name="add-authentication-components"></a>Adicionar componentes de autenticação

1. In Visual Studio: Vá para **ferramentas**  >  **NuGet Package Manager** Package Manager  >  **Consola**.
2. Adicione *Pacotes NuGet de middleware OWIN*, escrevendo o seguinte na janela da Consola do Gestor de Pacotes:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

### <a name="about-these-libraries"></a>Sobre estas bibliotecas
Estas bibliotecas permitem um único sinal de insípido (SSO) utilizando o OpenID Connect através da autenticação baseada em cookies. Depois de a autenticação estar concluída e o token que representa o utilizador ser enviado para a sua aplicação, o middleware OWIN cria um cookie de sessão. O navegador utiliza então este cookie em pedidos posteriores para que o utilizador não tenha de reescrever a palavra-passe, não sendo necessária nenhuma verificação adicional.

## <a name="configure-the-authentication-pipeline"></a>Configure o gasoduto de autenticação

Os seguintes passos são usados para criar uma classe de startup de middleware OWIN para configurar a autenticação OpenID Connect. Esta classe é executada automaticamente quando o seu processo IIS começa.

> [!TIP]
> Se o projeto não tiver um ficheiro `Startup.cs` na pasta raiz:
> 1. Clique com o botão direito na pasta raiz do projeto e, em seguida, **selecione Adicionar** a classe Startup Add New  >  **Item**  >  **OWIN**.<br/>
> 2. Nomeie-o **Startup.cs**.
>
>> Certifique-se de que a classe selecionada é uma classe OWIN Startup e não uma classe C# padrão. Confirme-o verificando se vê [montagem: OwinStartup(typeof({NameSpace}. Startup))] acima do espaço de nomes.

1. Adicione *referências OWIN* e *Microsoft.IdentityModel* a .cs Startup:

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

        // Authority is the URL for authority, composed of the Microsoft identity platform and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
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
                    // ResponseType is set to request the code id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.CodeIdToken,
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
> A `ValidateIssuer = false` definição é uma simplificação para este arranque rápido. Em aplicações reais, deve validar o emitente.
> Veja as amostras para aprender a fazer isso.

### <a name="more-information"></a>Mais informações

Os parâmetros que fornece nas *Opções OpenIDConnectAuthentication* servem como coordenadas para a aplicação comunicar com a plataforma de identidade da Microsoft. Como o middleware OpenID Connect utiliza cookies em segundo plano, também deve configurar a autenticação de cookies como o código anterior mostra. O valor *ValidateIssuer* diz ao OpenIdConnect para não restringir o acesso a uma organização específica.

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Adicione um controlador para lidar com pedidos de inscrição e de assinatura

Para criar um novo controlador para expor métodos de inscrição e de assinatura, siga estes passos:

1.  Clique com o botão direito na pasta **controladores** e selecione  >  **Add Controller**.
2.  Selecione **Controlador MVC (versão .NET) – Vazio**.
3.  Selecione **Adicionar**.
4.  Nomeie-o **HomeController** e, em seguida, **selecione Adicionar**.
5.  Adicione referências OWIN à classe:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Adicione os dois métodos seguintes para lidar com o início da sing-in e a sua assinatura no seu controlador, iniciando um desafio de autenticação:

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

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Crie a página inicial da aplicação para o início do sessão

No Visual Studio, crie uma nova visão para adicionar o botão de iniciar sessão e para exibir as informações do utilizador após a autenticação:

1.  Clique com o botão direito do rato na pasta **Vistas\Início** e selecione **Adicionar Vista**.
2.  Nomeie o **novo índice de** vista .
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

### <a name="more-information"></a>Mais informações
 Esta página adiciona um botão de início de sessão no formato SVG com um fundo preto:<br/>![Iniciar sação com o botão Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Para obter mais botões de inscrição, aceda às [diretrizes de Branding](./howto-add-branding-in-azure-ad-apps.md "Diretrizes de imagem corporativa").

## <a name="add-a-controller-to-display-users-claims"></a>Adicione um controlador para mostrar as reclamações do utilizador
Este controlador demonstra as utilizações do atributo `[Authorize]` para proteger um controlador. Este atributo restringe o acesso ao controlador, permitindo apenas utilizadores autenticados. O seguinte código faz uso do atributo para exibir as alegações do utilizador que foram recuperadas como parte da inscrição:

1.  Clique com o botão direito na pasta **controladores** e, em seguida, **selecione**  >  **Add Controller**.
2.  Selecione **Controlador MVC {versão} – Vazio**.
3.  Selecione **Adicionar**.
4.  Dê-lhe o nome **ClaimsController**.
5.  Substitua o código da sua classe de controlador pelo seguinte código. Isto adiciona o `[Authorize]` atributo à classe:

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

            //You get the user's first and last name below:
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

### <a name="more-information"></a>Mais informações
Devido à utilização do `[Authorize]` atributo, todos os métodos deste controlador só podem ser executados se o utilizador for autenticado. Se o utilizador não for autenticado e tentar aceder ao controlador, o OWIN inicia um desafio de autenticação e obriga o utilizador a autenticar. O código anterior analisa a lista de reclamações de atributos específicos do utilizador incluídos no token de ID do utilizador. Estes atributos incluem o nome completo e o nome de utilizador do utilizador, bem como o sujeito do identificador de utilizador global. Contém também o ID do *Inquilino,* que representa o ID para a organização do utilizador.

## <a name="create-a-view-to-display-the-users-claims"></a>Criar uma vista para mostrar as reivindicações do utilizador

No Visual Studio, crie uma nova vista para apresentar as afirmações do utilizador numa página Web:

1.  Clique à direita na pasta **'Visualizações',** e, em seguida, **selecione Add View**.
2.  Nomeie o **novo índice de** vista .
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

## <a name="register-your-application"></a>Registar a aplicação

Para registar a sua candidatura e adicionar as informações de registo da sua candidatura à sua solução, tem duas opções:

### <a name="option-1-express-mode"></a>Opção 1: Modo expresso

Para registar rapidamente a sua candidatura, siga estes passos:

1. Vá ao <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">portal Azure - Aplicação registra</a> experiência de arranque rápido.  
1. Introduza um nome para a sua aplicação e xelecione **Registar**.
1. Siga as instruções para descarregar e configurar automaticamente a sua nova aplicação num único clique.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo avançado

Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:

1. Estúdio Visual Aberto, e depois:
   1. no Solution Explorer, selecione o projeto e veja a janela Propriedades (se não vir uma janela do Properties, prima F4).
   1. Alterar SSL Habilitado a `True` .
   1. Clique com o botão direito no projeto no Estúdio Visual, selecione **Propriedades** e, em seguida, selecione o separador **Web.** Na secção **Servidores,** altere a definição **de Url de projeto** para o URL **SSL**.
   1. Copie o URL SSL. Irá adicionar este URL à lista de URIs redirecionados na lista de URIs de redirecionamento do portal de redireccionamento no passo seguinte.<br/><br/>![Propriedades do projeto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
   
1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
1. Introduza um **Nome** para a sua aplicação, por `ASPNET-Tutorial` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
1. Adicione o URL SSL que copiou do Visual Studio no passo 1 (por exemplo, `https://localhost:44368/` ) em **Redirect URI**.
1. Selecione **Registar**.
1. Em **Gestão**, **selecione Autenticação**.
1. Na secção **de fluxos implícitos e híbridos,** selecione **fichas de identificação** e, em seguida, selecione **Save**.
1. Adicione o seguinte no ficheiro web.config, localizado na pasta raiz na `configuration\appSettings` secção:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. `ClientId`Substitua-o pelo ID de aplicação que acabou de registar.
1. `redirectUri`Substitua-se pelo URL SSL do seu projeto.

## <a name="test-your-code"></a>Teste o seu código

Para testar a sua aplicação no Visual Studio, prima F5 para executar o seu projeto. O navegador abre para a <span></span> localização local http://:{port} e vê o Sinal com o botão **Microsoft.** Selecione o botão para iniciar o processo de início de sação.

Quando estiver pronto para executar o seu teste, utilize uma conta AD Azure (trabalho ou conta escolar) ou uma conta pessoal da Microsoft<span>(ao vivo.</span> com ou <span>outlook.</span> com) para iniciar sinsu.

![Iniciar sposição com o botão Microsoft mostrado na página do início do navegador no navegador](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Inscreva-se na sua conta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Permissões e consentimento na plataforma de identidade da Microsoft
As aplicações que se integram com a plataforma de identidade da Microsoft seguem um modelo de autorização que dá aos utilizadores e administradores o controlo sobre a forma como os dados podem ser acedidos. Depois de um utilizador autenticar com a plataforma de identidade da Microsoft para aceder a esta aplicação, estes serão solicitados a consentir as permissões solicitadas pela aplicação ("Ver o seu perfil básico" e "Manter o acesso aos dados a que lhe deu acesso"). Depois de aceitar estas permissões, o utilizador continuará a seguir os resultados da aplicação. No entanto, o utilizador pode, em vez disso, ser solicitado com uma página **de consentimento administrativo Need** se um dos seguintes ocorrer:

- O desenvolvedor de aplicações adiciona quaisquer permissões adicionais que requerem **o consentimento do Administrador**.
- Ou o inquilino é configurado (em **Aplicações empresariais -> Configurações do Utilizador),** onde os utilizadores não podem consentir em aplicações que acedam aos dados da empresa em seu nome.

Para mais informações, consulte [permissões e consentimento na plataforma de identidade da Microsoft.](./v2-permissions-and-consent.md)

### <a name="view-application-results"></a>Ver resultados da aplicação

Depois de iniciar seduada, o utilizador é redirecionado para a página inicial do seu website. A página inicial é o URL HTTPS especificado na informação de registo da sua aplicação no Portal de Registo de Aplicações da Microsoft. A página inicial inclui uma mensagem de boas-vindas *\<user> "Olá",* um link para assinar e um link para visualizar as reclamações do utilizador. O link para as reclamações do utilizador liga-se ao controlador Claims que criou anteriormente.

### <a name="view-the-users-claims"></a>Ver as reclamações do utilizador

Para visualizar as reclamações do utilizador, selecione o link para navegar na vista do controlador que está disponível apenas para utilizadores autenticados.

#### <a name="view-the-claims-results"></a>Ver os resultados das reclamações

Depois de navegar pela vista do controlador, deverá ver uma tabela que contenha as propriedades básicas para o utilizador:

|Propriedade |Valor |Descrição |
|---|---|---|
|**Nome** |Nome completo do utilizador | O primeiro e último nome do utilizador
|**Nome de Utilizador** |utilizador<span>@domain.com</span> | O nome de utilizador que é usado para identificar o utilizador|
|**Assunto** |Assunto |Uma cadeia que identifica exclusivamente o utilizador através da web|
|**ID do inquilino** |GUID | Um **guia** que representa exclusivamente a organização AZure AD do utilizador|

Além disso, deverá consultar uma tabela de todas as reclamações que estejam no pedido de autenticação. Para mais informações, consulte a [lista de reclamações que estão num token de identificação.](./id-tokens.md)

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Teste de acesso a um método que tem um atributo Autorizado (opcional)

Para testar o acesso como utilizador anónimo a um controlador protegido pelo `Authorize` atributo, siga estes passos:

1. Selecione o link para iniciar a assinatura do utilizador e complete o processo de inscrição.
2. No seu navegador, escreva http:// <span></span> local:{port}/alega aceder ao seu controlador protegido pelo `Authorize` atributo.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Resultados esperados após o acesso a um controlador protegido

É-lhe pedido que autentica para usar a vista do controlador protegido.

## <a name="advanced-options"></a>Opções avançadas

### <a name="protect-your-entire-website"></a>Proteja todo o seu site

Para proteger todo o seu website, no ficheiro **Global.asax,** adicione o `AuthorizeAttribute` atributo ao filtro no `GlobalFilters` `Application_Start` método:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```

### <a name="restrict-who-can-sign-in-to-your-application"></a>Restringir quem pode iniciar sôms na sua candidatura

Por padrão, quando constrói a aplicação criada por este guia, a sua aplicação aceitará inscrições de contas pessoais (incluindo outlook.com, live.com e outras) bem como contas de trabalho e escola de qualquer empresa ou organização que esteja integrada na plataforma de identidade da Microsoft. Esta é uma opção recomendada para aplicações SaaS.

Para restringir o acesso ao acesso ao utilizador para a sua aplicação, estão disponíveis várias opções.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opção 1: Restringir os utilizadores de apenas uma instância de Diretório Ativo de uma organização para iniciar sôm na sua aplicação (inquilino único)

Esta opção é frequentemente utilizada para *aplicações LOB*: Se pretender que a sua aplicação aceite inscrições apenas a partir de contas pertencentes a uma instância específica da AD Azure (incluindo contas de hóspedes dessa instância), siga estes *passos:*

1. No ficheiro web.config, altere o valor `Tenant` do parâmetro para o nome do inquilino da `Common` organização, como `contoso.onmicrosoft.com` .
2. Na sua [aula de Startup OWIN,](#configure-the-authentication-pipeline)de `ValidateIssuer` argumente para `true` .

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Opção 2: Restringir o acesso aos utilizadores numa lista específica de organizações

Você pode restringir o acesso de entrada a apenas as contas de utilizador que estão em uma organização AD Azure que está na lista de organizações permitidas:
1. Na sua [aula de Startup OWIN,](#configure-the-authentication-pipeline)de `ValidateIssuer` argumente para `true` .
2. Desa um valor do `ValidIssuers` parâmetro para a lista de organizações autorizadas.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opção 3: Utilize um método personalizado para validar emitentes

Pode implementar um método personalizado para validar emitentes utilizando o parâmetro **EmiterValidator.** Para obter mais informações sobre como utilizar este parâmetro, consulte a classe [TokenValidationParameters.](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba como ligar para apis web protegidos a partir de aplicações web com a plataforma de identidade da Microsoft:

> [!div class="nextstepaction"]
> [Aplicativos web chamando APIs web](scenario-web-app-sign-user-overview.md)
