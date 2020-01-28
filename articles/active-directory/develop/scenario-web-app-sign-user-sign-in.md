---
title: Gravar um aplicativo Web que entra/sai de usuários-plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo Web que entra/sai de usuários
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cbec6c5644690f6d7e522294a37c8ea5e2d49e30
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701523"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Aplicativo Web que conecta usuários: entrar e sair

Saiba como adicionar entrada ao código para seu aplicativo Web que faz logon em usuários. Em seguida, saiba como deixá-las sair.

## <a name="sign-in"></a>Iniciar sessão

A entrada consiste em duas partes:

- O botão de entrada na página HTML
- A ação de entrada no code-behind no controlador

### <a name="sign-in-button"></a>Botão de sessão

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET Core, o botão de inserção é exposto em `Views\Shared\_LoginPartial.cshtml`. Ele é exibido somente quando não há nenhuma conta autenticada. Ou seja, ele é exibido quando o usuário ainda não tiver entrado ou tiver se desconectado.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET MVC, o botão de sinalização é exposto em `Views\Shared\_LoginPartial.cshtml`. Ele é exibido somente quando há uma conta autenticada. Ou seja, ele é exibido quando o usuário entrou anteriormente.

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

No início rápido dos Python, não há botão de inscrição. O code-behind solicita automaticamente que o usuário entre quando está atingindo a raiz do aplicativo Web. Ver [app.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` ação do controlador

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET, a seleção do botão **'Iniciar sessão'** na aplicação web aciona a ação `SignIn` no controlador `AccountController`. Em versões anteriores dos modelos centrais ASP.NET, o controlador `Account` foi incorporado na aplicação web. Esse não é mais o caso porque o controlador agora faz parte da estrutura de ASP.NET Core.

O código para `AccountController` está disponível no repositório ASP.NET Core em [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). O controlo da conta desafia o utilizador redirecionando para o ponto final da plataforma de identidade da Microsoft. Para mais detalhes, consulte o método [SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) fornecido como parte do ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, a assinatura é desencadeada a partir do método `SignOut()` num controlador (por exemplo, [AccountController.cs#L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Este método não faz parte do quadro ASP.NET (ao contrário do que acontece no Núcleo ASP.NET). Ele envia um desafio de logon de OpenID depois de propor um URI de redirecionamento.

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

# <a name="javatabjava"></a>[Java](#tab/java)

Em Java, o sign-out é tratado ligando para a plataforma de identidade da Microsoft `logout` ponto final diretamente e fornecendo o valor `post_logout_redirect_uri`. Para mais detalhes, consulte [AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Ao contrário de outras plataformas, o Python MSAL cuida de permitir que o usuário entre na página de logon. Ver [app.py#L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

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

O método `_build_msal_app()` é definido em [app.py#L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) da seguinte forma:

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

Depois que o usuário tiver entrado em seu aplicativo, você desejará habilitá-los para sair.

## <a name="sign-out"></a>Terminar sessão

Sair de um aplicativo Web envolve mais do que remover as informações sobre a conta conectada do estado do aplicativo Web.
A aplicação web também deve redirecionar o utilizador para a plataforma de identidade da Microsoft `logout` ponto final para assinar. 

Quando a sua aplicação web redireciona o utilizador para o ponto final `logout`, este ponto final iliba a sessão do utilizador a partir do navegador. Se a sua aplicação não for para o ponto final `logout`, o utilizador reapagará na sua aplicação sem voltar a introduzir as suas credenciais. O motivo é que eles terão uma sessão de logon único válida com o ponto de extremidade da plataforma Microsoft Identity.

Para saber mais, consulte a secção [de pedido de inscrição](v2-protocols-oidc.md#send-a-sign-out-request) na plataforma de identidade da Microsoft e a documentação [do protocolo OpenID Connect.](v2-protocols-oidc.md)

### <a name="application-registration"></a>Registo da aplicação

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Durante o registro do aplicativo, você registra um URI de pós-logout. No nosso tutorial, registou `https://localhost:44321/signout-oidc` no campo URL de **Logout** da secção **Definições Avançadas** na página **de Autenticação.** Para mais detalhes, consulte [Registar a aplicação webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Durante o registro do aplicativo, você registra um URI de pós-logout. No nosso tutorial, registou `https://localhost:44308/Account/EndSession` no campo URL de **Logout** da secção **Definições Avançadas** na página **de Autenticação.** Para mais detalhes, consulte [Registar a aplicação webApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="javatabjava"></a>[Java](#tab/java)

Durante o registro do aplicativo, você registra um URI de pós-logout. No nosso tutorial, registou `http://localhost:8080/msal4jsample/sign_out` no campo URL de **Logout** da secção **Definições Avançadas** na página **de Autenticação.**

# <a name="pythontabpython"></a>[Python](#tab/python)

Durante o registo da candidatura, não precisa de registar um URL de logout extra. O aplicativo será chamado de volta em sua URL principal.

---

### <a name="sign-out-button"></a>Botão de inscrição

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET Core, o botão de sinalização é exposto em `Views\Shared\_LoginPartial.cshtml`. Ele é exibido somente quando há uma conta autenticada. Ou seja, ele é exibido quando o usuário entrou anteriormente.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET MVC, o botão de sinalização é exposto em `Views\Shared\_LoginPartial.cshtml`. Ele é exibido somente quando há uma conta autenticada. Ou seja, ele é exibido quando o usuário entrou anteriormente.

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

No quickstart Python, o botão de saída está localizado no ficheiro [templates/index.html#L10.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` ação do controlador

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET, a seleção do botão **Sign-out** na aplicação web despoleta a ação `SignOut` no controlador `AccountController`. Em versões anteriores dos modelos ASP.NET Core, o controlador `Account` foi incorporado na aplicação web. Esse não é mais o caso porque o controlador agora faz parte da estrutura de ASP.NET Core.

O código para o `AccountController` está disponível a partir do ASP.NET repositório principal em [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). O controlo da conta:

- Configura um redirecionamento OpenID para `/Account/SignedOut` de modo a que o controlador seja chamado de volta quando o Azure AD tiver concluído a inscrição.
- As chamadas `Signout()`, que permite ao openID Connect contactar a plataforma de identidade da Microsoft `logout` ponto final. Em seguida, o ponto de extremidade:

  - Limpa o cookie de sessão do navegador.
  - Chama novamente a URL de logout. Por predefinição, o URL de logout apresenta a página de visualização [assinada SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml). Essa página também é fornecida como parte do ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, a assinatura é desencadeada a partir do método `SignOut()` num controlador (por exemplo, [AccountController.cs#L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Esse método não faz parte da estrutura ASP.NET, ao contrário do que acontece em ASP.NET Core. Fosse

- Envia um desafio de desconexão de OpenID.
- Limpa o cache.
- Redireciona para a página que deseja.

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

# <a name="javatabjava"></a>[Java](#tab/java)

Em Java, o sign-out é tratado ligando para a plataforma de identidade da Microsoft `logout` ponto final diretamente e fornecendo o valor `post_logout_redirect_uri`. Para mais detalhes, consulte [AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

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

# <a name="pythontabpython"></a>[Python](#tab/python)

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

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Intercetando a chamada para o ponto final `logout`

O URI de pós-logout permite que os aplicativos participem da saída global.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

O ASP.NET Core OpenID Connect permite que a sua aplicação intercete a chamada para a plataforma de identidade da Microsoft `logout` ponto final, fornecendo um evento OpenID Connect chamado `OnRedirectToIdentityProviderForSignOut`. Para um exemplo de como subscrever este evento (para limpar a cache simbólica), consulte [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156).

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.NET, você delega ao middleware para executar a saída, limpando o cookie de sessão:

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

# <a name="javatabjava"></a>[Java](#tab/java)

No início rápido do Java, o URI de redirecionamento pós-Logout apenas exibe a página index. html.

# <a name="pythontabpython"></a>[Python](#tab/python)

No início rápido do Python, o URI de redirecionamento pós-Logout apenas exibe a página index. html.

---

## <a name="protocol"></a>Protocolo

Se quiser saber mais sobre a inscrição, leia a documentação do protocolo disponível no [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-web-app-sign-user-production.md)
