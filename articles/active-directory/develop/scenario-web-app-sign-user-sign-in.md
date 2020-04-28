---
title: Escreva uma aplicação web que insigne os utilizadores - plataforma de identidade da Microsoft / Azure
description: Saiba como construir uma aplicação web que ine/out utilizadores
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0926f41fb030e27ab8be54a2672ff9ed20e15206
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181584"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Aplicação web que assina nos utilizadores: Iniciar sessão e iniciar sessão

Saiba como adicionar sessão ao código da sua aplicação web que assina nos utilizadores. Então, aprenda a deixá-los assinar.

## <a name="sign-in"></a>Iniciar sessão

O signin-in é composto por duas partes:

- O botão de inscrição na página HTML
- A ação de inscrição no código por trás no controlador

### <a name="sign-in-button"></a>Botão de sessão

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET Core, para aplicações da plataforma de identidade microsoft, o botão **Sign in** é exposto `Views\Shared\_LoginPartial.cshtml` (para uma aplicação MVC) ou `Pages\Shared\_LoginPartial.cshtm` (para uma aplicação Razor). Só é apresentado quando o utilizador não é autenticado. Ou seja, é exibido quando o utilizador ainda não assinou ou assinou. Pelo contrário, o botão **'Sinal de saída'** é apresentado quando o utilizador já está inscrito. Note que o controlador de conta é definido no pacote **Microsoft.Identity.Web.UI** NuGet, na área chamada **MicrosoftIdentity**

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

Em ASP.NET MVC, o botão `Views\Shared\_LoginPartial.cshtml`de inscrição é exposto em . Só é exibido quando há uma conta autenticada. Ou seja, é exibido quando o utilizador já assinou.

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

No nosso java quickstart, o botão de início de sessão está localizado no ficheiro [main/resources/templates/index.html.](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html)

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

No início rápido dos Python, não há botão de inscrição. O código por trás automaticamente solicita ao utilizador o sessão quando está a chegar à raiz da aplicação web. Ver [app.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn`ação do controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET, a seleção do botão **'Iniciar sessão'** na aplicação web desencadeia a `SignIn` ação no `AccountController` controlador. Em versões anteriores dos modelos centrais ASP.NET, o `Account` controlador foi incorporado com a aplicação web. Isso já não acontece porque o controlador faz agora parte do pacote **Microsoft.Identity.Web.UI** NuGet. Consulte [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) para mais detalhes.

Este controlador também trata das aplicações Azure AD B2C.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, a assinatura é `SignOut()` desencadeada a partir do método num controlador (por exemplo, [AccountController.cs#L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Este método não faz parte do quadro ASP.NET (ao contrário do que acontece no Núcleo ASP.NET). Envia um desafio de entrada openID depois de propor um URI redirecionado.

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

Em Java, o sign-out é tratado `logout` ligando diretamente para o `post_logout_redirect_uri` ponto final da plataforma de identidade da Microsoft e fornecendo o valor. Para mais detalhes, consulte [AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

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

Depois de o utilizador ter assinado a sua aplicação, deverá permitir que assinem.

## <a name="sign-out"></a>Terminar sessão

A assinatura de uma aplicação web envolve mais do que remover a informação sobre a conta de inscrição do estado da aplicação web.
A aplicação web também deve redirecionar `logout` o utilizador para o ponto final da plataforma de identidade da Microsoft para assinar.

Quando a sua aplicação web `logout` redireciona o utilizador para o ponto final, este ponto final iliba a sessão do utilizador a partir do navegador. Se a sua aplicação `logout` não for para o ponto final, o utilizador reapagará na sua aplicação sem voltar a introduzir as suas credenciais. A razão é que eles terão uma sessão de inscrição única válida com o ponto final da plataforma de identidade da Microsoft.

Para saber mais, consulte a secção [de pedido de inscrição](v2-protocols-oidc.md#send-a-sign-out-request) na plataforma de identidade da Microsoft e a documentação [do protocolo OpenID Connect.](v2-protocols-oidc.md)

### <a name="application-registration"></a>Registo da aplicação

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Durante o registo de candidatura, registe um URI pós-logout. No nosso tutorial, `https://localhost:44321/signout-oidc` registou-se no campo URL de **Logout** da secção **Definições Avançadas** na página **de Autenticação.** Para mais detalhes, consulte [Registar a aplicação webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Durante o registo de candidatura, registe um URI pós-logout. No nosso tutorial, `https://localhost:44308/Account/EndSession` registou-se no campo URL de **Logout** da secção **Definições Avançadas** na página **de Autenticação.** Para mais detalhes, consulte [Registar a aplicação webApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="java"></a>[Java](#tab/java)

Durante o registo de candidatura, registe um URI pós-logout. No nosso tutorial, `http://localhost:8080/msal4jsample/sign_out` registou-se no campo URL de **Logout** da secção **Definições Avançadas** na página **de Autenticação.**

# <a name="python"></a>[Python](#tab/python)

Durante o registo da candidatura, não precisa de registar um URL de logout extra. A aplicação será chamada de volta no seu URL principal.

---

### <a name="sign-out-button"></a>Botão de inscrição

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET, a seleção do botão **'Sign out'** na aplicação web desencadeia a `SignOut` ação no `AccountController` controlador (ver abaixo)

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

Em ASP.NET MVC, o botão `Views\Shared\_LoginPartial.cshtml`de inscrição é exposto em . Só é exibido quando há uma conta autenticada. Ou seja, é exibido quando o utilizador já assinou.

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

No nosso início rápido de Java, o botão de sinalização está localizado no ficheiro main/resources/templates/auth_page.html.

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

No quickstart Python, o botão de saída está localizado no ficheiro [templates/index.html#L10.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

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

### <a name="signout-action-of-the-controller"></a>`SignOut`ação do controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em versões anteriores dos modelos centrais ASP.NET, o `Account` controlador foi incorporado com a aplicação web. Isso já não acontece porque o controlador faz agora parte do pacote **Microsoft.Identity.Web.UI** NuGet. Consulte [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) para mais detalhes.

- Configura um Redirecionamento `/Account/SignedOut` OpenID para que o controlador seja chamado de volta quando o Azure AD tiver concluído a inscrição.
- Chamadas `Signout()`, que permitem ao openID Connect entrar `logout` em contato com o ponto final da plataforma de identidade da Microsoft. O ponto final, então:

  - Limpa o cookie da sessão do navegador.
  - Chama de volta o URL de logout. Por predefinição, o URL de logout apresenta a página de visualização [assinada SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml). Esta página também é fornecida como parte de MIcrosoft.Identity.Web.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, a assinatura é `SignOut()` desencadeada a partir do método num controlador (por exemplo, [AccountController.cs#L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Este método não faz parte do quadro ASP.NET, ao contrário do que acontece no Núcleo ASP.NET. É:

- Envia um desafio de inscrição OpenID.
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

Em Java, o sign-out é tratado `logout` ligando diretamente para o `post_logout_redirect_uri` ponto final da plataforma de identidade da Microsoft e fornecendo o valor. Para mais detalhes, consulte [AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

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

O código que assina o utilizador está em [app.py#L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Intercetando a `logout` chamada para o ponto final

O URI pós-logout permite que as aplicações participem no sign-out global.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O ASP.NET Core OpenID Connect permite que a sua aplicação `logout` intercete a chamada para o `OnRedirectToIdentityProviderForSignOut`ponto final da plataforma de identidade da Microsoft, fornecendo um evento OpenID Connect nomeado . Isto é tratado automaticamente pela Microsoft.Identity.Web (que limpa contas no caso em que a sua aplicação web chama apis web)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, você delega no middleware para executar a inscrição, limpando o cookie da sessão:

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

No quickstart de Java, o redirecionamento pós-logout URI apenas exibe a página index.html.

# <a name="python"></a>[Python](#tab/python)

No quickstart python, o redirecionamento pós-logout URI apenas exibe a página index.html.

---

## <a name="protocol"></a>Protocolo

Se quiser saber mais sobre a inscrição, leia a documentação do protocolo disponível no [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-web-app-sign-user-production.md)
