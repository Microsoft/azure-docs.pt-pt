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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086463"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Aplicativo Web que conecta usuários-entrar

Saiba como adicionar entrada ao código para seu aplicativo Web que faz logon de usuários.

## <a name="sign-in"></a>Iniciar sessão

O código que examinamos na [configuração do código do aplicativo do](scenario-web-app-sign-user-app-configuration.md) artigo anterior é tudo o que você precisa para implementar a entrada.
Depois que o usuário entrar no seu aplicativo, você provavelmente desejará habilitá-lo a sair. O ASP.NET Core manipula a saída para você.

## <a name="what-sign-out-involves"></a>O que a saída envolve

Sair de um aplicativo Web é mais do que remover as informações sobre a conta conectada do estado do aplicativo Web.
O aplicativo Web também deve redirecionar o usuário para o ponto `logout` de extremidade da plataforma Microsoft Identity para sair. Quando seu aplicativo Web redireciona o usuário para o ponto de `logout` extremidade, esse ponto de extremidade limpa a sessão do usuário no navegador. Se seu aplicativo não for para o `logout` ponto de extremidade, o usuário se reautenticaria em seu aplicativo sem inserir suas credenciais novamente, pois ele teria uma sessão de logon único válida com o ponto de extremidade da plataforma de identidade da Microsoft.

Para saber mais, consulte a seção [Enviar uma solicitação de saída](v2-protocols-oidc.md#send-a-sign-out-request) na plataforma de [identidade da Microsoft e a documentação conceitual do protocolo OpenID Connect](v2-protocols-oidc.md) .

### <a name="application-registration"></a>Registo da aplicação

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

Durante o registro do aplicativo, você terá registrado um **URI de logout de postagem**. Em nosso tutorial, você se `https://localhost:44321/signout-oidc` registrou no campo **URL de logout** da seção **Configurações avançadas** na página **autenticação** . Para obter detalhes, consulte [registrar o aplicativo webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Durante o registro do aplicativo, você terá registrado um **URI de logout de postagem**. Em nosso tutorial, você se `https://localhost:44308/Account/EndSession` registrou no campo **URL de logout** da seção **Configurações avançadas** na página **autenticação** . Para obter detalhes, consulte [registrar o aplicativo webApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="javatabjava"></a>[Java](#tab/java)

Durante o registro do aplicativo, você registrará um **URI de logout de postagem**. Em nosso tutorial, você se `http://localhost:8080/msal4jsample/` registrou no campo **URL de logout** da seção **Configurações avançadas** na página **autenticação** .

# <a name="pythontabpython"></a>[Python](#tab/python)

Durante o registro do aplicativo, você não precisa registrar uma URL de logout. O exemplo não implementa a saída global

---

### <a name="sign-out-button"></a>Botão de saída

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

No ASP.NET Core, o botão de saída é exposto no `Views\Shared\_LoginPartial.cshtml` e exibido somente quando há uma conta autenticada (ou seja, quando o usuário entrou anteriormente).

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

No ASP.NET MVC, o botão de saída é exposto no `Views\Shared\_LoginPartial.cshtml` e exibido somente quando há uma conta autenticada (ou seja, quando o usuário entrou anteriormente).

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

No início rápido do Python, o botão de saída está localizado no arquivo [templates/display.html](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout()`ação do controlador

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

No ASP.net, pressionar o botão de **saída** no aplicativo Web aciona a `SignOut` ação no `AccountController` controlador. Nas versões anteriores dos modelos do ASP.NET Core, o `Account` controlador foi incorporado com o aplicativo Web, mas não é mais o caso, pois agora ele faz parte do próprio ASP.NET Core Framework.

O código para o `AccountController` está disponível no repositório do ASP.NET Core em do [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). O controle de conta:

- Define um URI de redirecionamento de OpenID para `/Account/SignedOut` para que o controlador seja chamado de volta quando o Azure ad realizou a saída
- Chamadas `Signout()`, que permitem que o middleware OpenIdConnect entre em contato com o `logout` ponto de extremidade da plataforma de identidade da Microsoft, que:

  - Limpa o cookie de sessão do navegador e
  - Finalmente, chama a **URL de logout**, que, por padrão, exibe a página de exibição desconectada [. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) também fornecido como parte de ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.net, a saída é disparada `SignOut()` a partir do método em um controlador (por exemplo, AccountController). Esse método não faz parte da estrutura ASP.NET (ao contrário do que acontece em ASP.NET Core). Ele não usa `async`, e é por isso que:

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

Em Java, a saída é tratada chamando o ponto de extremidade de logout da plataforma de identidade da Microsoft diretamente e fornecendo o post_logout_redirect_uri.

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

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Interceptando a chamada para o `logout` ponto de extremidade

O URI de logout de postagem permite que os aplicativos participem da saída global.

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

O middleware ASP.NET Core OpenIdConnect permite que seu aplicativo intercepte a chamada para o ponto de extremidade da `logout` plataforma de identidade da Microsoft fornecendo um `OnRedirectToIdentityProviderForSignOut`evento OpenIdConnect chamado. Consulte [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156) para obter um exemplo de como assinar esse evento (para limpar o cache de token)

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

## <a name="protocol"></a>Protocol

Se você quiser saber mais sobre a saída, leia a documentação do protocolo, que está disponível no [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-web-app-sign-user-production.md)
