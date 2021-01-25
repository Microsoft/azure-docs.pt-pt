---
title: Escreva uma aplicação web que assina utilizadores in/out | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação web que assina utilizadores in/out
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 5b0b86321f7f2b320c3fea8e7c5bfa45bd936b77
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752996"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Aplicação web que assina nos utilizadores: Iniciar s-in e iniciar sê-out

Saiba como adicionar o s-in ao código da sua aplicação web que assina nos utilizadores. Então, aprenda a deixá-los assinar.

## <a name="sign-in"></a>Iniciar sessão

A inscrição é constituída por duas partes:

- O botão de inscrição na página HTML
- A ação de inscrição no código-traseiro no controlador

### <a name="sign-in-button"></a>Botão de inscrição

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET Core, para aplicações de plataforma de identidade da Microsoft, o botão **Sign in** é exposto `Views\Shared\_LoginPartial.cshtml` (para uma aplicação MVC) ou `Pages\Shared\_LoginPartial.cshtm` (para uma aplicação Razor). Só é exibido quando o utilizador não é autenticado. Ou seja, é exibido quando o utilizador ainda não assinou ou assinou. Pelo contrário, é apresentado o botão **'Iniciar'** quando o utilizador já está inscrito. Note que o controlador de conta está definido no pacote **Microsoft.Identity.Web.UI** NuGet, na área chamada **MicrosoftIdentity**

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET MVC, o botão de sinalização é exposto em `Views\Shared\_LoginPartial.cshtml` . Só é exibido quando há uma conta autenticada. Ou seja, é exibido quando o utilizador já se inscreveu.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

No nosso quickstart Java, o botão de entrada está localizado no ficheiro [principal/recursos/modelos/index.html.](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="python"></a>[Python](#tab/python)

No início rápido dos Python, não há botão de inscrição. O código-por trás solicita automaticamente ao utilizador o seu insusição quando está a atingir a raiz da aplicação web. Ver [aplicativo.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` ação do controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET, a seleção do botão de iniciar o **'Iniciar's** na aplicação web despoleta a ação `SignIn` no `AccountController` controlador. Em versões anteriores dos ASP.NET modelos de núcleo, o `Account` controlador foi incorporado com a aplicação web. Isso já não acontece porque o controlador faz agora parte do pacote **Microsoft.Identity.Web.UI** NuGet. Consulte [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) para mais detalhes.

Este controlador também trata das aplicações Azure AD B2C.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, a assinatura é desencadeada a partir do `SignOut()` método de um controlador (por exemplo, Controlador de [Contas.cs#L16-L23).](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23) Este método não faz parte da estrutura ASP.NET (ao contrário do que acontece no ASP.NET Core). Envia um desafio de inscrição OpenID depois de propor um URI de redirecionamento.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Em Java, o sign-out é tratado ligando diretamente para o ponto final da plataforma de identidade da Microsoft `logout` e fornecendo o `post_logout_redirect_uri` valor. Para mais informações, consulte [a AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="python"></a>[Python](#tab/python)

Ao contrário de outras plataformas, a MSAL Python cuida de deixar o utilizador iniciar sessão a partir da página de login. Ver [app.py#L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

O `_build_msal_app()` método é definido em [app.py#L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) da seguinte forma:

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Depois de o utilizador ter assinado a sua aplicação, irá querer que assinem.

## <a name="sign-out"></a>Terminar sessão

A assinatura de uma aplicação web envolve mais do que remover a informação sobre a conta de inscrição do estado da aplicação web.
A aplicação web também deve redirecionar o utilizador para o ponto final da plataforma de identidade da Microsoft `logout` para assinar.

Quando a sua aplicação web redireciona o utilizador para o `logout` ponto final, este ponto final limpa a sessão do utilizador do navegador. Se a sua aplicação não for para o `logout` ponto final, o utilizador irá reautenticar a sua app sem voltar a introduzir as suas credenciais. A razão é que eles terão uma sessão de sessão de inscrição única válida com a plataforma de identidade da Microsoft.

Para saber mais, consulte a secção Enviar uma secção [de pedido de sinalização](v2-protocols-oidc.md#send-a-sign-out-request) na plataforma de identidade da Microsoft e na documentação [do protocolo OpenID Connect.](v2-protocols-oidc.md)

### <a name="application-registration"></a>Registo da aplicação

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Durante o registo do pedido, regista-se um URI pós-logout. No nosso tutorial, registou-se `https://localhost:44321/signout-oidc` no campo **URL logout** da secção **Definições Avançadas** na página **autenticação.** Para mais informações, consulte [registar a aplicação webApp.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Durante o registo do pedido, regista-se um URI pós-logout. No nosso tutorial, registou-se `https://localhost:44308/Account/EndSession` no campo **URL logout** da secção **Definições Avançadas** na página **autenticação.** Para mais informações, consulte [registar a aplicação webApp.](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="java"></a>[Java](#tab/java)

Durante o registo do pedido, regista-se um URI pós-logout. No nosso tutorial, registou-se `http://localhost:8080/msal4jsample/sign_out` no campo **URL logout** da secção **Definições Avançadas** na página **autenticação.**

# <a name="python"></a>[Python](#tab/python)

Durante o registo da inscrição, não precisa de registar um URL de logout extra. A aplicação será chamada de volta no seu URL principal.

---

### <a name="sign-out-button"></a>Botão de inscrição

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET, a seleção do botão **Iniciar** na aplicação web despoleta a ação `SignOut` no controlador `AccountController` (ver abaixo)

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET MVC, o botão de sinalização é exposto em `Views\Shared\_LoginPartial.cshtml` . Só é exibido quando há uma conta autenticada. Ou seja, é exibido quando o utilizador já se inscreveu.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

No nosso quickstart Java, o botão de sinalização está localizado no ficheiro principal/recursos/modelos/auth_page.html.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

No quickstart Python, o botão de sinalização está localizado nos [modelos/index.htmficheiro l#L10.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` ação do controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em versões anteriores dos ASP.NET modelos de núcleo, o `Account` controlador foi incorporado com a aplicação web. Isso já não acontece porque o controlador faz agora parte do pacote **Microsoft.Identity.Web.UI** NuGet. Consulte [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) para mais detalhes.

- Define um URI de redirecionamento OpenID para `/Account/SignedOut` que o controlador seja chamado de volta quando o Azure AD tiver completado a assinatura.
- Chamadas `Signout()` , que permite que o middleware OpenID Connect contacte o ponto final da plataforma de identidade da `logout` Microsoft. O ponto final, então:

  - Limpa o cookie de sessão do navegador.
  - Chama de volta a URL logout. Por predefinição, o URL logout apresenta a página de visualização assinada [SignedOut.cshtml.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Pages/Account/SignedOut.cshtml.cs). Esta página também é fornecida como parte de MIcrosoft.Identity.Web.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, a assinatura é desencadeada a partir do `SignOut()` método de um controlador (por exemplo, Controlador de [Contas.cs#L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Este método não faz parte da estrutura ASP.NET, ao contrário do que acontece no ASP.NET Core. É:

- Envia um desafio de assinatura OpenID.
- Limpa a cache.
- Redireciona para a página que quer.

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="java"></a>[Java](#tab/java)

Em Java, o sign-out é tratado ligando diretamente para o ponto final da plataforma de identidade da Microsoft `logout` e fornecendo o `post_logout_redirect_uri` valor. Para mais informações, consulte [AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="python"></a>[Python](#tab/python)

O código que assina o utilizador está na [aplicação.py#L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Intercetando a chamada para o `logout` ponto final

O URI pós-logout permite que as aplicações participem na assinatura global.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O middleware core openID connect ASP.NET permite que a sua aplicação intercete a chamada para o ponto final da plataforma de identidade da `logout` Microsoft, fornecendo um evento OpenID Connect denominado `OnRedirectToIdentityProviderForSignOut` . Isto é tratado automaticamente pelo Microsoft.Identity.Web (que limpa contas no caso em que a sua aplicação web chama apis web)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, delega no middleware para executar a assinatura, limpando o cookie da sessão:

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="java"></a>[Java](#tab/java)

No arranque rápido de Java, o redirecionamento de uri pós-logout apenas exibe a página de index.html.

# <a name="python"></a>[Python](#tab/python)

No quickstart Python, o redirecionamento de uri pós-logout apenas exibe a página de index.html.

---

## <a name="protocol"></a>Protocolo

Se quiser saber mais sobre a assinatura, leia a documentação do protocolo disponível no [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Próximos passos

Passe para o próximo artigo neste cenário, [passe para a produção](scenario-web-app-sign-user-production.md).