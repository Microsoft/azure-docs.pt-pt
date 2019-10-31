---
title: Aplicativo Web que conecta os usuários (entrar)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que faz logon de usuários (entrar)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: d727b570361e721c49173138bb60ae89df710e81
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175237"
---
# <a name="web-app-that-signs-in-users---sign-in-and-sign-out"></a>Aplicativo Web que entra em usuários – entrar e sair

Saiba como adicionar entrada ao código para seu aplicativo Web que faz logon de usuários e, em seguida, como deixá-los sair

## <a name="sign-in"></a>Iniciar sessão

A entrada é trazida por duas partes:

- o botão de entrada na página HTML
- a ação de entrada no código por trás do controlador

### <a name="sign-in-button"></a>Botão de entrada

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

No ASP.NET Core, o botão de entrada é exposto em `Views\Shared\_LoginPartial.cshtml` e exibido somente quando não há uma conta autenticada (ou seja, quando o usuário ainda não tiver entrado ou tiver se desconectado).

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

No ASP.NET MVC, o botão de saída é exposto em `Views\Shared\_LoginPartial.cshtml` e exibido somente quando há uma conta autenticada (ou seja, quando o usuário entrou anteriormente).

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

Em nosso início rápido do Java, o botão de saída está localizado no arquivo [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html)

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

No início rápido do Python, não há nenhum botão de conexão. O usuário é solicitado automaticamente a entrar pelo code-behind ao atingir a raiz do aplicativo Web. Consulte [app. py # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="login-action-of-the-controller"></a>`Login` ação do controlador

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

No ASP.NET, pressionar o botão de **entrada** no aplicativo Web dispara a ação de `SignIn` no controlador de `AccountController`. Nas versões anteriores dos modelos do ASP.NET Core, o controlador `Account` foi incorporado ao aplicativo Web, mas não é mais o caso, pois agora ele faz parte do próprio ASP.NET Core Framework.

O código para o `AccountController` está disponível no repositório do ASP.NET Core do [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). O controle de conta desafia o usuário redirecionando para o ponto de extremidade da plataforma de identidade da Microsoft. Para obter detalhes, consulte o método de [entrada](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) fornecido como parte do ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.NET, a saída é disparada a partir do método `SignOut()` em um controlador (por exemplo [, AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Esse método não faz parte da estrutura ASP.NET (ao contrário do que acontece em ASP.NET Core). Fosse

- envia um desafio de logon de OpenId depois de propor um URI de redirecionamento

```CSharp
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

Em Java, a saída é tratada chamando o ponto de extremidade de logout da plataforma de identidade da Microsoft diretamente e fornecendo o post_logout_redirect_uri. Para obter detalhes [, consulte AuthPageController. java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)

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

Ao contrário de outras plataformas, o Python MSAL cuida de permitir que o usuário entre na página de logon. Consulte [app. py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use empty list [] to just sign in,
                           # here we choose to also collect end user consent upfront
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

com o método _build_msal_app () definido em [app. py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) da seguinte maneira:

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
    if accounts:  # So all account(s) belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Depois que o usuário entrar no seu aplicativo, você provavelmente desejará habilitá-lo a sair.

## <a name="sign-out"></a>Terminar sessão

Sair de um aplicativo Web é mais do que remover as informações sobre a conta conectada do estado do aplicativo Web.
O aplicativo Web também deve redirecionar o usuário para a plataforma de identidade da Microsoft `logout` ponto de extremidade para sair. Quando seu aplicativo Web redireciona o usuário para o ponto de extremidade `logout`, esse ponto de extremidade limpa a sessão do usuário do navegador. Se o seu aplicativo não for para o ponto de extremidade `logout`, o usuário se reautenticaria em seu aplicativo sem inserir suas credenciais novamente, pois ele teria uma sessão de logon único válida com o ponto de extremidade da plataforma Microsoft Identity.

Para saber mais, consulte a seção [Enviar uma solicitação de saída](v2-protocols-oidc.md#send-a-sign-out-request) na plataforma de [identidade da Microsoft e a documentação conceitual do protocolo OpenID Connect](v2-protocols-oidc.md) .

### <a name="application-registration"></a>Registo da aplicação

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

Durante o registro do aplicativo, você terá registrado um **URI de logout de postagem**. Em nosso tutorial, você registrou `https://localhost:44321/signout-oidc` no campo **URL de logout** da seção **Configurações avançadas** na página **autenticação** . Para obter detalhes, consulte [registrar o aplicativo webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Durante o registro do aplicativo, você terá registrado um **URI de logout de postagem**. Em nosso tutorial, você registrou `https://localhost:44308/Account/EndSession` no campo **URL de logout** da seção **Configurações avançadas** na página **autenticação** . Para obter detalhes, consulte [registrar o aplicativo webApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="javatabjava"></a>[Java](#tab/java)

Durante o registro do aplicativo, você registrará um **URI de logout de postagem**. Em nosso tutorial, você registrou `http://localhost:8080/msal4jsample/sign_out` no campo **URL de logout** da seção **Configurações avançadas** na página **autenticação** .

# <a name="pythontabpython"></a>[Python](#tab/python)

Durante o registro do aplicativo, você não precisa registrar uma URL de logout extra. O aplicativo será chamado de volta em sua URL principal

---

### <a name="sign-out-button"></a>Botão de saída

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

No ASP.NET Core, o botão de saída é exposto em `Views\Shared\_LoginPartial.cshtml` e exibido somente quando há uma conta autenticada (ou seja, quando o usuário entrou anteriormente).

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

No ASP.NET MVC, o botão de saída é exposto em `Views\Shared\_LoginPartial.cshtml` e exibido somente quando há uma conta autenticada (ou seja, quando o usuário entrou anteriormente).

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

Em nosso início rápido do Java, o botão de saída está localizado no arquivo main/resources/templates/auth_page.html

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

No início rápido do Python, o botão de saída está localizado no arquivo [templates/index. html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

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

### <a name="signout-action-of-the-controller"></a>`Signout` ação do controlador

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

No ASP.NET, pressionar o botão de **saída** no aplicativo Web dispara a ação de `SignOut` no controlador de `AccountController`. Nas versões anteriores dos modelos do ASP.NET Core, o controlador `Account` foi incorporado ao aplicativo Web, mas não é mais o caso, pois agora ele faz parte do próprio ASP.NET Core Framework.

O código para o `AccountController` está disponível no repositório principal do ASP.NET em do [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). O controle de conta:

- Define um URI de redirecionamento de OpenID para `/Account/SignedOut` para que o controlador seja chamado de volta quando o Azure AD tiver concluído a saída
- Chama `Signout()`, que permite que o middleware OpenIdConnect contate a plataforma de identidade da Microsoft `logout` ponto de extremidade que:

  - Limpa o cookie de sessão do navegador e
  - Finalmente, chama a **URL de logout**, que, por padrão, exibe a página de exibição desconectada [. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) também fornecido como parte de ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.NET, a saída é disparada a partir do método `SignOut()` em um controlador (por exemplo [, AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Esse método não faz parte da estrutura ASP.NET (ao contrário do que acontece em ASP.NET Core). Fosse

- envia um desafio de desconexão de OpenId
- limpa o cache
- redireciona para a página que deseja

```CSharp
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

Em Java, a saída é tratada chamando o ponto de extremidade de logout da plataforma de identidade da Microsoft diretamente e fornecendo o post_logout_redirect_uri. Para obter detalhes [, consulte AuthPageController. java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)

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

O código que desconecta o usuário está no [aplicativo. py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48)

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out user and its token cache from session
    return redirect(  # Also need to logout from Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Interceptando a chamada para o ponto de extremidade `logout`

O URI de logout de postagem permite que os aplicativos participem da saída global.

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

O middleware ASP.NET Core OpenIdConnect permite que seu aplicativo intercepte a chamada para o ponto de extremidade de `logout` da plataforma de identidade da Microsoft fornecendo um evento OpenIdConnect chamado `OnRedirectToIdentityProviderForSignOut`. Consulte [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156) para obter um exemplo de como assinar esse evento (para limpar o cache de token)

```CSharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.NET, você delega ao middleware para executar a saída, limpando o cookie de sessão:

```CSharp
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

Em nosso início rápido do Java, o URI de redirecionamento de saída post apenas exibe a página index. html

# <a name="pythontabpython"></a>[Python](#tab/python)

No início rápido do Python, o URI de redirecionamento de saída post apenas exibe a página index. html.

---

## <a name="protocol"></a>Protocolo

Se você quiser saber mais sobre a saída, leia a documentação do protocolo, que está disponível no [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-web-app-sign-user-production.md)
